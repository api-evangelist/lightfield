---
name: Advance a Lightfield opportunity through its pipeline
description: Find an opportunity, read its stage definitions, and move it to a new stage with a follow-up task, safely and idempotently.
api: openapi/lightfield-openapi-original.yml
base_url: https://api.lightfield.app/v1
operations:
  - opportunity.definitions
  - opportunity.list
  - opportunity.retrieve
  - opportunity.update
  - task.definitions
  - task.create
  - member.list
scopes:
  - opportunities:read
  - opportunities:update
  - tasks:create
  - members:read
generated: '2026-07-19'
method: generated
---

# Advance an opportunity and create the follow-up

Use this to move a deal forward and leave the next action behind it.

## Before every request

```
Authorization: Bearer $LIGHTFIELD_API_KEY
Lightfield-Version: 2026-03-01
```

## Steps

1. **Read the stage vocabulary.** Call `opportunity.definitions`
   (`GET /v1/opportunities/definitions`). The stage field is typically a `SINGLE_SELECT`; its
   definition lists the only values the API will accept. Sending anything else returns `422
   unprocessable_content` with `param` pointing at the field.

2. **Locate the opportunity.** Call `opportunity.list` (`GET /v1/opportunities`) with `limit` (max
   **25**) and `offset`, filtering on a field the type supports — `equal`/`greaterThan` for
   `NUMBER`, `CURRENCY` and `DATETIME`; `equal`/`startsWith` for `TEXT`. Paginate until a page
   returns fewer records than the requested `limit`.

3. **Read the current state.** Call `opportunity.retrieve`
   (`GET /v1/opportunities/{id}`). Do this rather than relying on the list result — list methods are
   index-backed and may not reflect recent changes.

4. **Update the stage.** Call `opportunity.update` (`POST /v1/opportunities/{id}`) with an
   `Idempotency-Key` header and only the fields you are changing:

   ```
   {"fields": {"$stage": "Negotiation"}}
   ```

5. **Assign the follow-up.** Call `member.list` (`GET /v1/members`) to resolve the owner's member
   id, then `task.definitions` to find the owner relationship key and status vocabulary, then
   `task.create` (`POST /v1/tasks`) with a fresh `Idempotency-Key`, the task `fields` (due date,
   status) and a `relationships` object linking the task to the opportunity and the owning member.

## Error handling

- `422 unprocessable_content` — a value failed business validation, e.g. a stage outside the
  `SINGLE_SELECT` options. Read `param`.
- `400 invalid_type` — a field value has the wrong type; `param` names it.
- `409 conflict` — concurrent update on the same record. Re-run step 3 to get fresh state, then
  retry.
- `409 idempotency_conflict` — the same key is still in flight. Wait and retry with the same key.
- `403 forbidden` — the key lacks `opportunities:update` or `tasks:create`.

## Rules

- Update = `POST /v1/opportunities/{id}`. There is no PATCH.
- Send only changed fields; the update is a merge over the `fields` map.
- One `Idempotency-Key` per logical write. The stage update and the task creation are two different
  operations and must use two different keys.
- Read limits and write limits are both 25 rps per organization; back off on `Retry-After`.
