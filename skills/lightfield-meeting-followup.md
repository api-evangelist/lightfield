---
name: Summarize a Lightfield meeting and log the follow-up
description: Pull a meeting and its transcript, write a note summarizing it against the account, and create the follow-up task.
api: openapi/lightfield-openapi-original.yml
base_url: https://api.lightfield.app/v1
operations:
  - meeting.list
  - meeting.retrieve
  - note.create
  - task.create
  - account.retrieve
scopes:
  - meetings:read
  - notes:create
  - tasks:create
  - accounts:read
generated: '2026-07-19'
method: generated
---

# Summarize a meeting and log the follow-up

The flow Lightfield's own meeting-prep agents follow: read what happened, write it down against the
right record, and leave the next action.

## Before every request

```
Authorization: Bearer $LIGHTFIELD_API_KEY
Lightfield-Version: 2026-03-01
```

## Steps

1. **Find the meeting.** Call `meeting.list` (`GET /v1/meetings`) filtering on the meeting date
   field (a `DATETIME`, so `equal` and `greaterThan`-family operators are available). Remember
   `limit` maxes out at 25.

2. **Retrieve it with the transcript.** Call `meeting.retrieve` (`GET /v1/meetings/{id}`). The
   transcript is exposed on the `$transcript` field. See the transcript retrieval guide at
   https://docs.lightfield.app/using-the-api/retrieving-meeting-transcripts/ — transcripts are large,
   so fetch them per meeting rather than over a list.

3. **Identify the related account.** Read the meeting's `relationships` map for the account (and
   opportunity, if present) ids, and confirm with `account.retrieve`
   (`GET /v1/accounts/{id}`) if you need current field values for the summary.

4. **Know the note shape.** A note is a rich markdown document that can mention CRM entities; its
   documented fields are title, body, account and opportunity, with the body carried as `MARKDOWN`.
   Unlike accounts, contacts, opportunities and tasks, the captured OpenAPI exposes no
   `notes/definitions` operation — if a write fails with `unknown_field`, read `param` and consult
   https://docs.lightfield.app/using-the-api/fields-and-relationships/.

5. **Write the note.** Call `note.create` (`POST /v1/notes`) with an `Idempotency-Key`, a title, a
   markdown body containing the summary, and `relationships` linking it to the account and
   opportunity from step 3.

6. **Create the follow-up task.** Call `task.create` (`POST /v1/tasks`) with a fresh
   `Idempotency-Key`, a due date, and relationships to the same account/opportunity.

## Grounding rules

- Summarize **only** from the retrieved transcript and record fields. Do not assert deal facts the
  API did not return.
- Quote or paraphrase the transcript; never invent attendees, commitments, or amounts.
- If `$transcript` is absent the meeting was not recorded — say so in the note rather than
  synthesizing a summary.

## Error handling

- `404 not_found` — the meeting id is wrong or the record was deleted.
- `403 forbidden` — the key lacks `meetings:read`, `notes:create` or `tasks:create`.
- `400 unknown_field` — the note body/title key differs in this workspace; re-read step 4.
- `429 too_many_requests` — honour `Retry-After`.
