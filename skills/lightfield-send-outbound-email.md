---
name: Draft and send a Lightfield email through a connected mailbox
description: Read prior correspondence for context, draft an email for review, and send it through a connected mailbox with retry-safe semantics.
api: openapi/lightfield-openapi-original.yml
base_url: https://api.lightfield.app/v1
operations:
  - contact.retrieve
  - email.list
  - email.retrieve
  - email.draft
  - email.send
scopes:
  - contacts:read
  - emails:read
  - emails:create
generated: '2026-07-19'
method: generated
---

# Draft and send an email

Outbound email is the one operation where a duplicate is visible to a customer. Treat it carefully.

## Before every request

```
Authorization: Bearer $LIGHTFIELD_API_KEY
Lightfield-Version: 2026-03-01
```

## Steps

1. **Load the recipient.** Call `contact.retrieve` (`GET /v1/contacts/{id}`) for the person's
   current email address and name fields.

2. **Read the thread history.** Call `email.list` (`GET /v1/emails`) filtered to the contact, then
   `email.retrieve` (`GET /v1/emails/{id}`) on the messages you need in full. `EMAIL`-typed fields
   support the `contains` operator only — not `equal` or `startsWith`.

3. **Draft first.** Call `email.draft` (`POST /v1/emails/draft`) with an `Idempotency-Key`. A draft
   lands in the connected mailbox for a human to review and send. **Prefer this path** for any
   agent-authored message unless the caller has explicitly authorized autonomous sending.

4. **Send only when authorized.** Call `email.send` (`POST /v1/emails/send`) with an
   `Idempotency-Key`.

   Note the caveat Lightfield documents: remote email providers do not natively support Lightfield
   idempotency keys, so retry protection on `email.send` is **best effort** on Lightfield's side. A
   timeout on this call is genuinely ambiguous. Before blindly retrying, call `email.list` and check
   whether the message already went out.

## Error handling

- `403 forbidden` — the key lacks `emails:create` (send/draft) or `emails:read` (history).
- `409 idempotency_conflict` — an identical send is in flight. Wait, then retry with the same key.
- `422 unprocessable_content` — a field failed validation; read `param`.
- `429 too_many_requests` — honour `Retry-After`.
- `503 service_unavailable` / `504` — retry with exponential backoff, then verify via `email.list`
  before sending again.

## Rules

- Never fabricate commitments, pricing, or dates. Ground the message in the retrieved thread and
  CRM fields only.
- One `Idempotency-Key` per intended message; reuse it on retry, never on a revised body.
- Requires a mailbox already connected to the Lightfield workspace; the API sends through it rather
  than from a Lightfield-owned domain.
