# Lightfield

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
