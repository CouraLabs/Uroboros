# Webhooks + polling both from day one

How Uroboros learns about PRs/issues is one of the entry points; both ingestion modes ship in v1: webhooks (HMAC-verified against the Project's Link secret) for freshness, and per-Project scheduled polling (scan interval, default 15 min) as the always-on fallback for hosts that can't or don't expose a public endpoint. Docker Compose optionally ships a Caddy reverse-proxy service so a self-hoster can end with a public HTTPS endpoint with one command; users with their own proxy skip the service.
