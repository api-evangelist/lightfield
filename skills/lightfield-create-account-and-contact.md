---
name: Create a Lightfield account and linked contact
description: Create a new company record and a person on it, discovering valid fields first so the write never fails on an unknown field.
api: openapi/lightfield-openapi-original.yml
base_url: https://api.lightfield.app/v1
operations:
  - account.definitions
  - account.list
  - account.create
  - contact.definitions
  - contact.create
scopes:
  - accounts:read
  - accounts:create
  - contacts:read
  - contacts:create
generated: '2026-07-19'
method: generated
---

# Create an account and a linked contact

Use this when onboarding a new company into Lightfield along with a first point of contact.

## Before every request

Send both headers on every call:

```
Authorization: Bearer $LIGHTFIELD_API_KEY
Lightfield-Version: 2026-03-01
```

Omitting the version header returns `400` with `code: version_header`. Send
`Content-Type: application/json` on any request with a body, or you get `415 unsupported_media_type`.

## Steps

1. **Discover the account schema.** Call `account.definitions` (`GET /v1/accounts/definitions`).
   Lightfield's object model is definition-driven — field and relationship keys vary per workspace,
   and system keys are prefixed with `$` (e.g. `$name`, `$website`). Never guess a field name: an
   undefined field returns `400` with `code: unknown_field` and `param` naming the offender.

2. **Check the account does not already exist.** Call `account.list`
   (`GET /v1/accounts?limit=25`) with a filter on the name field, e.g. `$name[equal]=Acme Corp`.
   Note that list results are served from a search index that may lag recent writes — if you created
   the account seconds ago, use `account.retrieve` by id rather than trusting the list.

3. **Create the account.** Call `account.create` (`POST /v1/accounts`) with a `fields` object.
   Always send an `Idempotency-Key` header (a UUID v4) so a network retry cannot create a duplicate:

   ```
   Idempotency-Key: 6f1c9c2e-...-b3a1
   {"fields": {"$name": "Acme Corp", "$website": ["https://acme.com"]}}
   ```

   Keep the response `id` — that is the account id used for relationships.

4. **Discover the contact schema.** Call `contact.definitions`
   (`GET /v1/contacts/definitions`) to find the relationship key that links a contact to an account
   and its `cardinality`.

5. **Create the contact.** Call `contact.create` (`POST /v1/contacts`) with a fresh
   `Idempotency-Key`, the contact `fields`, and a `relationships` object pointing at the account id
   from step 3. Relationship values are arrays of ids.

## Error handling

- `400 unknown_field` / `unknown_relationship` — re-read the Definitions response; the `param` field
  names exactly what was wrong.
- `400 referenced_resource_missing` / `relationship_entity_missing` — the account id in
  `relationships` does not resolve. Confirm step 3 succeeded.
- `400 relationship_entity_inactive` — the referenced record was deleted.
- `403 forbidden` — the key lacks `accounts:create` or `contacts:create`.
- `409 idempotency_conflict` — an identical request is still in flight. Wait, then retry with the
  **same** key.
- `429 too_many_requests` — honour `Retry-After`; writes are capped at 25 rps per organization.

## Rules

- Reuse the same `Idempotency-Key` when retrying a failed or timed-out write; a new key risks a
  duplicate record. Keys expire after 24 hours and are namespaced per entity type and per
  create-vs-update.
- Never send a different payload with a previously used key — you get the original cached response
  back, not the new write.
- Updates are `POST /v1/{entityType}/{id}`, not `PUT` or `PATCH`.
