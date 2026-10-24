# MCP-only Dev Platform integration

Uroboros must work across code hosts and issue trackers (GitHub, Azure DevOps, Jira, …) without hand-written per-platform integration code. We connect only through MCP: the engine is an MCP client; each platform is reached through the MCP server it exposes (official where available: GitHub, Atlassian Rovo, Azure DevOps remote). If no MCP server exists for a platform, that platform is unsupported until one does — we do not add bespoke API integration code.

## Considered options

- Per-platform REST clients — rejected: unbounded maintenance, contradicts the product's own pitch.
- Generic OpenAPI adapters — rejected: auth quirks and pagination make them bespoke clients with worse ergonomics.
