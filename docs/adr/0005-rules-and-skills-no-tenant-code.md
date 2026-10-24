# Pluggability: Rules + Project Skills, no tenant code

Users customize agent behavior per Project in two ways only: (a) declarative **Rules** (schedules, path scopes, style constraints, Autonomy Tier) and (b) **Project Skills** — versioned instruction definitions plus tool whitelists, loaded by the agent per Project. User-authored code plugins executed inside our runtime are explicitly out until deliberately revisited: arbitrary tenant JS running in a process that holds repo credentials is a security cliff for a self-hosted appliance.
