# Self-hosted, Docker Compose, Postgres-only datastore

Uroboros v1 is self-hosted by the user (not a hosted SaaS). Deployment shape is Docker Compose: `api` service (AI SDK engine + REST/SSE, also serves the built Angular statics), `worker` service (agent runs), one Postgres container. Postgres is the single datastore — runs, metrics, and the job queue/scheduling live on top of it; no Redis, no external message bus. The v1 schema is single-org (implicit owning organization, no tenant_id); a future SaaS would be a deliberate migration, not speculative structure today.

## Considered options

- Single Bun binary + SQLite — rejected: queue/scheduling and Insights aggregations get hacky, worker+api sharing a SQLite file is fragile.
- Kubernetes/Helm — rejected: wrong install friction for v1 self-hosters.
- tenant_id from day one — rejected: query noise for zero current multi-org users.
