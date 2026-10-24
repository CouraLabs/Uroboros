# Central config store: versioned bases + overrides, keys via web UI

All pluggable behavior lives in Postgres, not in files: **Rules** (declarative per-Project operational config) and **Project Skills** (versioned instruction definitions + tool whitelists). Base versions ship with Uroboros via migrations; the self-hoster may create an **Override** that routes execution to a custom version without replacing the base. Overrides pin to base version: when a newer base ships, the pin holds until the user reviews the upgrade diff and re-approves — overrides never silently inherit changed bases. The diff is a field-level structured summary rendered by Uroboros (no AI interpretation of the delta).

Dev Platform credentials and LLM provider keys are entered through the web UI and stored encrypted at rest (AES-GCM) with an instance key supplied via env. The same store is the single source of truth for the runtime, the dashboard, and migrations.

## Considered options

- Skills as committed files in the repo (`.uroboros/skills/`) — rejected despite natural versioning: the override flow, diff viewer, and per-Project activation all live in the config store; dual sources of truth would fight.
- Env-only credentials — rejected: keys via web UI are the self-host UX chosen in Q14.
