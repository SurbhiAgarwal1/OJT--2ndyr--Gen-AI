# Technical Requirements Document (TRD)

## Proposed Architecture
- **MCP Server:** Python, official MCP SDK
- **MCP Client:** Python, official MCP SDK
- **LLM orchestration:** Claude API (Anthropic), tool-calling via `messages` endpoint
- **Local transport:** stdio (subprocess-based)
- **Networked transport:** HTTP + Server-Sent Events (SSE)
- **HTTP server framework:** FastAPI (or equivalent ASGI framework)
- **Data store:** In-memory store for MVP; swappable for SQLite/Postgres in later iterations
- **Auth:** Token-based per-client credentials with scoped permission tiers
- **Deployment:** Containerized server, deployed to a public cloud host reachable by URL

## Technology Rationale
### Python + Official MCP SDK
Chosen over TypeScript for consistency with the team's existing skill set; the official SDK abstracts protocol-level plumbing (handshake, message framing, schema validation) so effort concentrates on tool/resource logic rather than reimplementing the spec.

### FastAPI
Lightweight, async-native, and has first-class support for SSE-style streaming responses, making it a natural fit for the HTTP/SSE transport layer.

### Claude API
Provides the tool-calling capability required to translate natural-language requests into structured tool invocations; no local model hosting or GPU requirement, aligning with project constraints.

### In-memory store (MVP)
Sufficient for demonstrating protocol behavior (tools, resources, subscriptions) without introducing persistence complexity in the first iteration; designed to be replaced without changing the MCP-facing interface.

### Token-based auth
Simplest mechanism that still demonstrates real per-client permission scoping; avoids the overhead of a full OAuth/SSO flow, which is out of scope for MVP.

## Technical Requirements
| ID | Requirement | Maps to |
|---|---|---|
| TR-001 | MCP server exposes tools with valid JSON Schema inputs | US-001 |
| TR-002 | MCP server exposes readable resources | US-001 |
| TR-003 | Client discovers tools/resources via `list_tools`/`list_resources` | US-001 |
| TR-004 | Dual transport support: stdio and HTTP/SSE, identical tool set on both | US-002 |
| TR-005 | Resource subscription with server-initiated push over SSE | US-003 |
| TR-006 | Per-client token auth with at least two permission tiers | US-004 |
| TR-007 | Client supports concurrent connections to multiple MCP servers | US-005 |
| TR-008 | Protocol/schema version negotiated at connection time | US-006 |
| TR-009 | Publicly reachable server deployment with setup documentation | US-007 |
| TR-010 | Structured error responses for auth failures and schema mismatches | US-004, US-006 |

## NFR Targets
- Tool discovery and invocation success rate: 100% of declared tools callable by a standard MCP client, zero manual config.
- Push notification latency (resource subscription): target under 2 seconds from server-side change to client notification.
- API responses excluding LLM round-trip time: target p95 < 1 second.
- All protocol/schema versions and permission tiers documented and recorded.
- Auth tokens and API keys stored outside source code (environment variables / secrets manager).
- Server must remain operable over stdio even if HTTP/SSE deployment is unavailable (transport independence).