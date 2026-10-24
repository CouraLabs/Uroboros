# Glossary Map

## Contexts

- [Engine](./packages/agent-core/GLOSSARY.md): the agent runtime terminology — Projects, Runs, Links, tiers, config
- [Dashboard](./apps/web/GLOSSARY.md): the web app's view of the same nouns, screens and UI-side concepts

## Relationships

- **Engine → Dashboard**: the Dashboard is a client over the Engine's REST/SSE; all nouns (Project, Run, Finding) share the Engine's definitions
- **Dashboard does not redefine** engine nouns; it only adds surface concepts (screens, flows, views)
