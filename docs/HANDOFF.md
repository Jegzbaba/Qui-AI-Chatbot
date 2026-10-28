# QUI AI website developer handoff

1. Review `README.md`, especially all required production launch checks.
2. Run the backend locally and verify login, profile, conversations, account deletion and restart recovery.
3. Provision verified QUI AI ChromaDB collection with matching original embedding model.
4. Choose deployment origin and reverse proxy; adjust frontend API prefix and frame/CSP policy as needed.
5. Migrate from development SQLite to managed PostgreSQL with Alembic migrations.
6. Implement email verification, password recovery, rate limits, monitoring, data retention enforcement and security testing.
7. Connect privacy policy and support contacts; review provider data processing and consent wording.
8. Build CI for dependency/security checks and integration tests before exposing the service publicly.

Suggested acceptance tests: user A cannot access user B conversations (including guessed IDs); session persists across reload; logout invalidates cookie; cross-origin and missing CSRF token are rejected; account deletion removes user and messages; chat refuses to run if Chroma collection missing rather than creating a blank one; chat returns useful grounded answers with existing knowledge.
