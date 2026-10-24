# Three-tier per-Project autonomy, Observe default

AI making changes to real repositories needs an explicit, auditable trust boundary. Per-Project Autonomy Tier: **Observe** (reports and comments only — default for newly connected Projects), **Propose** (draft PRs, never merged without human), **Act** (ready PRs + named human reviewer assignment, applied only when Project Rules allow, e.g. "fix if simple"). Hard invariants across all tiers: the agent never pushes or merges to the default branch, and every applied fix has a named human review before merge. Tier changes are audited events.
