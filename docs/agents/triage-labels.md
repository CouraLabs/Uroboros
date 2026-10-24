# Triage Labels

The issue labels form three dimensions. Every issue carries exactly one **type** label, zero or more **component** labels, and zero or one **status** label. Skills stamp types and components at publish time; status is the triage workflow's own surface.

## Type (exactly one per issue)

| Label        | Meaning                                                                    | Stamped by |
| ------------ | -------------------------------------------------------------------------- | ---------- |
| `spec`       | Design/spec document — explores the design space, defines the build        | `/to-spec` |
| `ticket`     | Tracer-bullet implementation ticket — vertical, demoable, blockers declared | `/to-tickets` |

Type is exclusive: an issue is a spec or a ticket, never both. Spec and ticket are linked as parent/child sub-issues — children of a `spec` issue are always `ticket` issues.

## Component (zero or more per issue)

Component labels mirror the ADR-0011 seam map one-to-one; label names are the ADR's package names. They say **which seams the change must be implemented in** — the implementer reads them as the blast-radius declaration.

| Label                | Seam                                                            |
| -------------------- | --------------------------------------------------------------- |
| `app:api`            | REST/SSE server (`apps/api`)                                    |
| `app:worker`         | Run orchestrator (`apps/worker`)                                |
| `app:web`            | Angular dashboard (`apps/web`)                                  |
| `pkg:domain`         | Zod schemas + DTO contracts (`packages/domain`)                 |
| `pkg:persistence`    | Postgres schema, entities, queries (`packages/persistence`)     |
| `pkg:job-queue`      | Queue + scheduler on Postgres (`packages/job-queue`)            |
| `pkg:agent-core`     | AI SDK agent loop + tool assembly (`packages/agent-core`)       |
| `pkg:platform-mcp`   | Link → MCP client factory (`packages/platform-mcp`)             |
| `pkg:qa-runtime`     | Playwright QA + video capture (`packages/qa-runtime`)           |

Stamping logic:

- Stamp every seam the implementation **must touch** to deliver the ticket — not the seams that merely consume the result. A package consumes the work through its consumer's ticket, its own label appears when its own code changes.
- A new seam (package or app added outside a revision of this file) is missing unless an ADR records the seam first; component labels follow ADRs, never precede them.
- Never stamp spec issues with component labels: the spec spans the design; its children declare the seams.

## Status (zero or one per issue)

The five canonical triage roles, used as-is:

| Label in mattpocock/skills | Label in our tracker | Meaning                                  |
| -------------------------- | -------------------- | ---------------------------------------- |
| `needs-triage`             | `needs-triage`       | Maintainer needs to evaluate this issue  |
| `needs-info`               | `needs-info`         | Waiting on reporter for more information |
| `ready-for-agent`          | `ready-for-agent`    | Fully specified, ready for an AFK agent  |
| `ready-for-human`          | `ready-for-human`    | Requires human implementation            |
| `wontfix`                  | `wontfix`            | Will not be actioned                     |

When a skill mentions a role (e.g. "apply the AFK-ready triage label"), use the corresponding label string from this table.

## Publish-time stamping (the logic agents apply)

1. `/to-spec` publishes → apply `spec`. (`ready-for-agent` too: a published spec is agent-readable, until triage says otherwise.)
2. `/to-tickets` publishes → for each ticket apply `ticket` + one component label per seam it touches + `ready-for-agent` (agent-grabbable by construction).
3. Editing an existing issue's scope (blockers change, seams move) → update the component labels in the same edit. Labels are kept truthful in place, not re-triaged.
