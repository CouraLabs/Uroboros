Uroboros: self-hosted agent service that connects to Dev Platforms (GitHub first) via MCP and autonomously reviews, fixes, and verifies code. Parent spec: repo issue #1. Vocabulary is binding (see Docs).

## Docs

- `packages/agent-core/GLOSSARY.md`: the domain vocabulary — Project, Run, Finding, Bug, Link, Rules, Autonomy Tier… Use these terms in code, APIs, events, tests. Never invent synonyms; when naming anything, resolve it against this file first.
- `apps/web/GLOSSARY.md`: dashboard screen and flow terms.
- `docs/adr/`: read the ADRs for any area you touch before changing architecture, datastore, auth, ingestion, or platform integration. They are the "why"; do not re-litigate them in PRs.
- Issue tracker and triage vocabulary: `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`.

## Architecture

Docker Compose: `api` + `worker` + Postgres (+ optional Caddy profile). Each package below is one architectural seam and one import boundary (ADR-0011):

- `apps/api`: REST + SSE server; the only thing tests and the dashboard talk to; later serves the built `apps/web` statics.
- `apps/worker`: run orchestrator; claims Runs from the queue; spawns one ephemeral container per Run.
- `apps/web`: Angular v22 dashboard. Spec-first, not built until the engine works (ADR-0006).
- `packages/domain`: Zod schemas + DTO contracts. No runtime dependencies.
- `packages/persistence`: schema, entities, all Postgres access. The only package that touches SQL.
- `packages/job-queue`: queue + scheduler on Postgres. The only source of Run triggers.
- `packages/agent-core`: AI SDK v5 agent loop + tool assembly. Platform-agnostic: it knows only the normalized platform surface.
- `packages/platform-mcp`: Link → MCP client factory. The only place a Dev Platform adapter may exist (ADR-0001).
- `packages/qa-runtime`: Playwright QA driver + video capture.

Import direction: apps compose packages; packages depend on `domain` (and what they own); `agent-core` never imports `platform-mcp` — it receives the normalized surface. Nothing imports across from `web` to engine internals.

### Hard invariants (apply in every tier, every ticket)

- The agent never pushes or merges to the default branch; every applied fix gets a named human review before merge (ADR-0004).
- Link credentials and keys are encrypted at rest and never returned by any read; credentials never enter run containers — platform access is brokered (ADR-0009).
- Platform integration is MCP-only; no hand-rolled REST calls to a Dev Platform (ADR-0001).
- Postgres is the only datastore: entities, queue, audit, Insights; state never lives in files or Redis. Artifacts go to the volume, served through short-lived signed URLs (ADR-0002).
- Single-org schema: no tenant columns anywhere (ADR-0002).
- Rules and Project Skills live in the central config store as versioned bases + pinned overrides; no tenant code in the runtime (ADR-0005, ADR-0009).

## Coding standards

- **SOLID**: one reason to change per module; boundaries live at the package seams above. A module that changes for two reasons needs two modules.
- **YAGNI**: build what the current ticket names — nothing for wave-2 platforms, SaaS, or future features. No speculative interfaces, config, or schema columns.
- **DRY**: every meaning has one authoritative home — config in the store, schemas in `domain`, vocabulary in the glossary. Two copies of a meaning is a bug waiting to drift.
- **Code speaks for itself**: names, types, and structure carry the meaning. No code comments. If the "why" cannot live in code, it lives in the ADR or the PR body, never a comment. A comment explaining clever code means the code gets rewritten, not annotated.
- **Tight**: the smallest expression that satisfies the ticket. No defensive clutter, no pre-optimization, no abstractions with a single implementation behind them.

## Toolchain — Bun first

- `bun <file>` to run; `bun test` to test; `bun check` to type-check; `bun install` to install; `bunx` over `npx`.
- `Bun.serve()` with WebSockets/routes for servers; never express. `Bun.sql` for Postgres; never `pg`/`postgres.js`/`bun:sqlite` (SQLite is not in this stack).
- `Bun.file` over `node:fs` reads; `Bun.$` over execa; built-in `WebSocket`/fetch over polyfills. Bun auto-loads `.env`; never dotenv.

## Testing

Seams follow spec #1 — tests assert external behavior only, never implementation detail:

- Primary seam: HTTP + SSE into `api` with deterministic double set — mock LLM (scripted), fake Dev Platform MCP (stateful, mutation journal), stub run executor, real Postgres. Runs green under `bun test`.
- QA fixture seam: browser-fixture tests only where the API seam cannot see — recorded video/artifact correctness in `qa-runtime`.
- No per-package unit seams. If a behavior isn't observable at the API seam, it isn't the behavior we built.

## Workflow

Tickets live on GitHub (`gh` CLI, usage in `docs/agents/issue-tracker.md`). Every issue carries a type label (`spec` or `ticket`) and component labels (`app:*` / `pkg:*`, one per seam it must touch — stamping logic in `docs/agents/triage-labels.md`). Work the open frontier: any `ready-for-agent` ticket whose blockers are all closed. Tracer-bullet slices land vertical and demoable; a finished ticket means its acceptance criteria are checked and its seam tests pass.
