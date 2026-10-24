# All login modes in v1: local + OAuth + SSO

Uroboros is self-hosted by teams with wildly different auth postures, so v1 ships a pluggable auth layer with all three login modes configurable: local bootstrap admin (email + password on first run), OAuth (social providers), and SSO (OIDC). Roles are Admin (full control, key management) and Viewer (read-only runs/insights, receives review requests); SSO/OAuth users map to a role via config, defaulting to Viewer.
