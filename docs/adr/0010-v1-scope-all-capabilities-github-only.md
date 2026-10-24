# All seven capability loops in v1, GitHub-only

Scope record from the grilling: v1 ships **all seven** capability loops — periodic review, PR review, issue→reproduce→fix-or-propose with human-check, QA, video capture, feature-reading, and per-Project pluggability — even though QA and video pull the browser-sandbox runtime (ADR-0003, qa-runtime) into v1. The original recommendation (defect-loop core first, QA/video in v2) was rejected: the full capability surface is the product's pitch from the first release. Platform scope is deliberately narrower than the pitch: v1 supports **GitHub only** — fine-grained-pat Dev Platform Links over the official MCP server; a GitHub App identity is a later upgrade. Azure DevOps and Jira are wave two, selected per Project when their Links land. Consequences: the v1 build includes the sandbox runtime and the auth surface (ADR-0007) before platform breadth; "works across platforms" is true of the architecture (ADR-0001), not yet of the shipped adapters.

## Considered options

- Defect-loop core (review + PR + issue-fix) first, QA/video/feature-reading in v2 — rejected by product owner: slower to the pitch, though cheaper to a first working loop.
- GitHub + Azure DevOps day one — rejected: widest OAuth/MCP testing matrix for the least-proven engine.
