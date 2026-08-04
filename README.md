# Lightfield

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Lightfield is an agent-native CRM platform. It captures customer interactions — calls, emails and
meetings — as unstructured data, organizes them into a versioned context graph of CRM objects, and
lets AI agents reason over that graph to generate pipeline, prepare for meetings, enrich records and
drive follow-up.

Backed by Greylock and Lightspeed Venture Partners — https://lightfield.app/

## API

The Lightfield API (public beta) provides read/write access to every CRM entity — accounts,
contacts, opportunities, meetings, notes, tasks, lists, emails, files, members, custom objects and
workflow runs.

| | |
|---|---|
| Base URL | `https://api.lightfield.app/v1` |
| Documentation | https://docs.lightfield.app/ |
| API reference | https://docs.lightfield.app/api/ |
| Spec | OpenAPI 3.1.1 — 39 paths, 55 operations, 69 schemas |
| Auth | Bearer API key (`sk_lf_...`), 26 scopes |
| Versioning | `Lightfield-Version: 2026-03-01` (required date header) |
| Idempotency | `Idempotency-Key` on POST writes, 255 chars, 24h retention |
| Pagination | `limit` (max 25) / `offset` |
| Rate limits | 25 rps per category, per organization |
| MCP server | `https://mcp.lightfield.app/mcp` (Streamable HTTP, OAuth 2.1) |
| Status | https://status.lightfield.app/ |

## SDKs and tooling

- TypeScript — [`lightfield`](https://www.npmjs.com/package/lightfield) on npm
- Python — [`lightfield`](https://pypi.org/project/lightfield/) on PyPI
- Go — [`github.com/Lightfld/lightfield-go`](https://github.com/Lightfld/lightfield-go)
- CLI — `brew install Lightfld/lightfield/lightfield`
- GitHub — https://github.com/Lightfld

## Artifacts in this repo

| Directory | Artifact |
|---|---|
| `openapi/` | Provider-published OpenAPI 3.1.1 description |
| `overlays/` | API Evangelist Overlay 1.0.0 adding servers, tags, applied security and runtime semantics |
| `authentication/` | Auth profile — REST bearer keys plus the MCP OAuth 2.1 surface |
| `scopes/` | 26 published API-key scopes plus MCP OAuth scopes |
| `conventions/` | Idempotency, pagination, filtering, versioning, error envelope, rate limits |
| `errors/` | Status-level problem catalog and the 400-level error-code registry |
| `data-model/` | Derived entity graph over the definition-driven object model |
| `lifecycle/` | Beta status, date-header versioning, status page, support |
| `changelog/` | Weekly dated product changelog, 2026-03-20 onward |
| `asyncapi/` | Event surface — workflow triggers, inbound webhooks, HTTP egress |
| `mcp/` | Hosted MCP server manifest and OAuth metadata |
| `skills/` | Four packaged Agent Skills grounded in real operationIds |
| `agentic-access/` | Recommended `x-agentic-access` contracts for all 55 operations |
| `packages/` | First-party TypeScript, Python, Go and CLI distributions |
| `cli/` | CLI command surface, install methods and flags |
| `well-known/` | RFC 8414 / RFC 9728 discovery documents from the MCP host |
| `security/` | Domain security probe, vulnerability disclosure policy, trust center |
| `conformance/` | Standards conformance assertions |
| `llms/` | llms.txt view of the provider and these artifacts |
