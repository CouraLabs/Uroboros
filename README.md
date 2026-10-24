# Uroboros

> **Status: pre-v1, in active development.** The engine is being built ticket by ticket — see the [v1 spec](https://github.com/CouraLabs/Uroboros/issues/1) and its 16 ordered sub-tickets. Nothing installable yet; the first milestone (`compose up` → working Admin + first Run) is ticket [01/16](https://github.com/CouraLabs/Uroboros/issues/2).

**Uroboros** is a self-hosted autonomous coding agent service. Connect it to a repository, and it reviews the codebase, reviews pull requests, investigates reported issues by *reproducing them*, fixes the confirmed and simple ones, QA-verifies its own fixes on recorded video, and checks documented features against the actual implementation — all inside a trust boundary you control, with a human reviewing every applied fix before it can merge.

The name is the shape of the loop: the ouroboros — the serpent that eats its own tail. The agent works on your codebase, and its output is fed back into itself: fixes it makes get QA-verified by itself, then get matched against later evidence, so regressions it caused resurface as first-class bugs.

## What it can do (v1)

- **Periodic review** — scheduled codebase reviews produce persisted reports; you decide the cadence per project.
- **Pull request review** — reacts to new or updated PRs (webhook or polling), commenting findings according to your autonomy choice.
- **Issue → reproduce → fix** — investigates issues by attempting reproduction before claiming anything; confirmed simple bugs get fixed; inconclusive cases are handed to a named human, never silently guessed.
- **QA the implementation** — agent fixes get verified by real browser runs.
- **Record video** — every QA run records a webm video, screenshots, and console/network logs as retrievable artifacts.
- **Read features** — sweeps your in-repo specs and labeled issues, comparing documented intent against real behavior, proposing the smallest corrective change.
- **Per-project pluggability** — declarative rules plus overridable, versioned "project skills" (instruction definitions with tool whitelists). Overrides are pinned and upgrade consciously — never silently.

## How it's different

| | Uroboros | Most review bots / CI agents |
| | --- | --- |
| **Runs where you like** | Self-hosted appliance: one `docker compose up`. Your infra, your LLM keys, your data | SaaS-hosted or CI-only |
| **Claims require evidence** | An issue isn't "fixed" — it's first *reproduced*, and the bug holds file + failure signature evidence that later re-detects regressions | Patch-and-pray prompts |
| **You see what it saw** | QA runs on recorded video with screenshots and logs | Text logs at best |
| **Integration** | Only via MCP — GitHub now, Azure DevOps and Jira next; no bespoke per-platform code | Per-platform bespoke adapters |
| **Trust dial** | Per-project autonomy tiers — Observe → Propose → Act — with hard invariants: it can't push or merge to the default branch under any tier, and every applied fix needs named human review | All-or-nothing permissions |

## Requirements

- [Bun](https://bun.com) v1.4+ (JavaScript runtime, rule chain, test runner)
- [Docker](https://www.docker.com/) with Compose v2 — the appliance shape plus the ephemeral per-run containers
- An LLM provider API key (Anthropic or OpenAI first; provider choice is instance configuration)
- A [GitHub fine-grained personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) per connected platform link (v1 scope: GitHub; Azure DevOps + Jira are wave two)

## Installation

Pre-release — nothing published yet. Watch or star to follow along, or read [the roadmap](#roadmap). Once the first milestone lands, the shape will be:

```bash
docker compose up
```

…which starts the API, the worker (which spawns an ephemeral container per agent run), Postgres, and — optionally, in a `caddy` profile — a TLS reverse proxy so webhooks work with one command. First visit sets up your admin account. No Redis, no message bus, no SaaS side-calls: Postgres is the whole state.

## Usage

For contributors (the repo is a Bun + Turborepo monorepo):

```bash
bun install    # install dependencies
bun test       # behavior tests over the API seam (mock LLM + fake platform + real Postgres)
bun check      # type-check the project
```

What using Uroboros v1 will look like (validated per-ticket until the flow is real):

```bash
# 1. bring the appliance up                                     (ticket 01)
docker compose up
# 2. first visit → create the admin account, add the LLM key    (ticket 01)
# 3. connect a GitHub link (fine-grained PAT)                   (tickets 02–03)
# 4. create a Project from the link's repo                      (ticket 02)
# 5. trigger a Run — anything from a periodic review to a manual QA
#    watch it live: steps stream over SSE                       (tickets 04–12)
# 6. findings and reports arrive; a fix opens a PR at your chosen
#    autonomy tier; the human review closes the loop            (tickets 09–10)
# 7. insights disclose what worked: fix success rate, regressed
#    bugs, most problematic code portions                       (ticket 13)
```

Stories, acceptance criteria, and the dependency order of all capabilities live in the [spec](https://github.com/CouraLabs/Uroboros/issues/1).

## Support

- Questions and bugs: [the issue tracker](https://github.com/CouraLabs/Uroboros/issues/new/choose)
- Project vocabulary and how decisions get recorded: `packages/agent-core/GLOSSARY.md` and `docs/adr/` in this repo

## Roadmap

- **v1 — the engine (now).** All seven capability loops over a GitHub link: review, PR review, issue-fix with reproduction and human-check, QA with video, feature reading, per-project rules and skills. Tracked as 16 ordered tickets under [spec #1](https://github.com/CouraLabs/Uroboros/issues/1); the Angular dashboard is spec-written first, built later.
- **Wave 2 — platform breadth.** Azure DevOps and Jira links (both have official MCP servers) implemented against the same normalized platform surface; GitHub App identity replaces the PAT.
- **Later.** Quality gates against platform preview URLs (behind a rules-configured egress allowlist), S3-compatible artifact storage, fuzzier regressed-bug matching, hosted multi-tenant offering — every one of these is a deliberate migration, not speculative scaffolding. Nothing speculative is in the v1 build.

## Contributing

This repository is built agent-first, and the same rules hold for humans:

1. **Read the governance first.** `CLAUDE.md` is the law: architecture seams, hard invariants, coding standards (SOLID, YAGNI, DRY, no code comments), Bun-first toolchain, and the testing seams.
2. **Use the domain vocabulary.** Don't invent synonyms — `Project`, `Run`, `Finding`, `Bug`, `Autonomy Tier` have one meaning each, in the glossary.
3. **Work the frontier.** Pick a `ready-for-agent` ticket whose blockers are all closed (the [implementation order](https://github.com/CouraLabs/Uroboros/issues/1#issuecomment-6386884856) is pinned on the spec). Tracer-bullet slices: a ticket is done when its acceptance criteria are checked and its seam-1 tests pass.
4. **Respect the ADRs.** Architecture is decided in `docs/adr/` and stays consistent; a PR that re-litigates an ADR is welcome — as a proposal, not a silent override.

## Authors and acknowledgment

[Built and maintained by CouraLabs](https://github.com/CouraLabs).

## License

No license has been chosen yet; a decision ships before v1. Until then, all rights reserved by the authors.
