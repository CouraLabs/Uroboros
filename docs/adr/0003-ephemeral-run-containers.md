# Ephemeral run containers for agent execution

The agent loop executes builds, tests, and browser QA, i.e. AI-driven shell and tool work against a real repo. Each Run executes inside an ephemeral container spawned by the worker: repo cloned in, network restricted to Dev Platform MCP + package registries, destroyed after the run. Blast radius of agent mistakes (rm -rf, credential exfiltration, runaway loops) is bounded to the container's lifetime and filesystem.

v1 QA subjects are builds served inside the run container itself; QA against platform preview URLs (arbitrary external hosts) is deferred until a Rules-configured egress allowlist exists.
