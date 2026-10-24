# Uroboros Dashboard

Language the web app (Angular v22) uses. Engine nouns (Project, Run, Finding, Link, Rules, Project Skill, Autonomy Tier, Bug, Regressed Bug, Report, Problematic Code Portion, Fix Success Rate) are defined in the Engine glossary and used here as-is.

## Screens

**Projects**
The screen listing Projects and their state — Link, tier, Rules summary, recent Runs.
_Avoid_: repos, repositories, workspace list

**Run Timeline**
Per-Run step-by-step execution view, including Findings, Artifacts, and status.
_Avoid_: run log, history, execution view

**Insights**
The metrics screen: Fix Success Rate, Regressed Bugs, Problematic Code Portions, usage.
_Avoid_: analytics, reports, stats

**Pending Review**
The queue of Requests For Human Review awaiting a Viewer or Admin.
_Avoid_: approval queue, inbox

## Flows

**Connect flow**
The guided setup that creates a Dev Platform Link — adapter choice, credential entry, repo selection.
_Avoid_: installation wizard, onboarding

**Setup**
First-run initialization: create the Admin account, then run the Connect flow.
_Avoid_: bootstrap (the auth-bootstrap concept), onboarding

**Settings**
Instance configuration screen: LLM provider keys and models, auth modes, storage, notification routes.
_Avoid_: config page, admin panel

## Surface concepts

**Delta View**
The Dashboard's view of a Delta: the field-level structured summary plus re-approval actions.
_Avoid_: Override Diff, code diff

**Signed Artifact URL**
The short-lived URL through which the Dashboard serves Run Artifacts from the volume.
_Avoid_: artifact link, download link
