# Uroboros Engine

Language for the agent runtime: how external systems connect, how work is expressed and executed, and how trust is bounded.

## Product

**Uroboros**:
The self-hosted agent service that connects to Dev Platforms and autonomously reviews, fixes, and verifies code.
_Avoid_: Uroboros bot, the agent service, oracle

## Connection

**Dev Platform**:
Any external system Uroboros connects to via MCP (GitHub, Azure DevOps, Jira, …).
_Avoid_: Code Awareness platform, code host, provider

**Dev Platform Link (Link)**:
A single connection to a Dev Platform — adapter type, credentials, and connection config.
_Avoid_: connector, integration, binding

**Project**:
A connected repository plus its configuration — Links, Rules, Project Skills, and Autonomy Tier.
_Avoid_: repository, repo, workspace, app

## Execution

**Run**:
One agent execution against a Project.
_Avoid_: job, task, execution

**Run Type**:
What kind of loop a Run executes: `review`, `pr-review`, `issue-fix`, `qa-verify`, `qa-manual`, `feature-sweep`.
_Avoid_: agent kind, mode

**Finding**:
A structured observation produced by a Run (defect candidate, feature gap, improvement).
_Avoid_: result, alert, issue (reserved for the platform's own object)

**Report**:
A Run's written document of results — findings, evidence, and proposed changes — delivered to the Dashboard and/or the Dev Platform.
_Avoid_: result set, recap

**Bug**:
A defect confirmed by a Run — a Run promotes Finding candidates into Bugs by reproducing or verifying them.
_Avoid_: defect (loose), GitHub Issue (an Issue is a Dev Platform object, not a Bug)

**Regressed Bug**:
A bug previously fixed by a Run and later detected again — same file plus same failure signature.
_Avoid_: reopened bug (that's a Dev Platform concept, not ours)

**Feature Spec**:
In-repo markdown or a labeled GitHub Issue defining intended behavior; input to `feature-sweep`.
_Avoid_: spec, ticket, feature file

**Request For Human Review**:
A Run's explicit ask for a named human to check its output; surfaces on the Dev Platform and in the Dashboard.
_Avoid_: ask for review, human-gate

**Artifact**:
A file output of a Run — QA video, screenshot, log, report.
_Avoid_: output, asset

## Trust

**Autonomy Tier**:
Per-Project permission level: `Observe` (reports and comments only), `Propose` (draft PRs), `Act` (ready PRs + human reviewer assignment). New Projects default to `Observe`.
_Avoid_: mode, level, permission set

**Hard Invariant**:
The non-negotiable rules above every tier: the agent never pushes or merges to the default branch, and every applied fix has a named human review before merge.

## Configuration

**Rules**:
The declarative per-Project operational config — schedules, scan intervals, path scopes, trigger and QA-mode options, Autonomy Tier.
_Avoid_: settings, config file, policy (reserved for tier/Hard Invariant language)

**Project Skill (Skill)**:
A versioned instruction definition plus a tool whitelist loaded into matching Runs; overridable per Project.
_Avoid_: plugin, prompt template, extension

**Base Definition (Base)**:
A Rules or Project Skill version shipped by Uroboros via migration.
_Avoid_: default, template

**Override**:
The self-hoster's routing of a Base Definition to a custom version; the base itself is never replaced. An Override pins the base version it was created against until re-approved.
_Avoid_: fork, replace, patch

**Re-approval**:
The explicit act of re-routing an Override onto a newer Base after viewing its Delta.
_Avoid_: merge, accept

**Delta**:
A field-level structured summary of changes between two definition versions, rendered by Uroboros — never a code diff.
_Avoid_: diff, changelog

## Insights

**Insights**:
The metrics view derived only from Run linkages — never guessed.
_Avoid_: analytics, stats, reports

**Problematic Code Portion**:
A zone (file or path) ranked hot by Findings density plus QA failure counts across Runs.
_Avoid_: hotspot (informal), problem file

**Fix Success Rate**:
The share of agent fixes that merge and do not later become Regressed Bugs.
_Avoid_: success %, fix rate

## Ingestion

**Webhook**:
A Dev Platform event delivered to Uroboros over HTTPS, HMAC-verified against the Link secret.
_Avoid_: event, callback

**Polling**:
A per-Project scheduled scan of the Dev Platform (default 15 min), the always-on ingestion fallback.
_Avoid_: cron sweep, background sync

## Access

**Viewer**:
Dashboard role with read-only access to Projects, Runs, Reports, and Insights; receives Requests For Human Review.
_Avoid_: read-only user, guest

**Admin**:
Dashboard role with full control — Links, keys, Rules, Overrides, Projects, Runs.
_Avoid_: owner, root
