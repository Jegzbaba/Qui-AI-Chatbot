# Quin — QUI AI Personalized Product Chatbot

This is my personal artifact, a **developer starter**, not a certified or independently audited production deployment. I provide a quick step-by-step guide on how to run this chatbot locally. Features: user registration/login, customer onboarding, consent, HttpOnly cookie sessions, CSRF checks, user-owned persistent conversations, account deletion, retrieval from the existing Chroma collection, OpenAI-generated answers, responsive web chat.

## 1. Local installation

Use Python 3.11–3.13 for deployment compatibility. In a terminal from this folder:

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env  # On Windows, use: copy .env.example .env
```

```bash
uvicorn backend.app:app --host 127.0.0.1 --port 8000 --reload
```

## 2. Existing QUI AI knowledge base

The verified local collection from the prototype was:

- Collection: `qui_ai_products_v2`
- Existing records: 12
- Original Windows directory: `C:\Users\johno\OneDrive\Desktop\QUI AI Personalized Chat Bot\chroma_db`

## 3. Website integration

Host this application at a same-origin URL such as `https://quiai.net/quin/` **after** the developer configures reverse-proxy routing for `/api` and `/`. Alternatively host Quin at `https://quin.quiai.net` and link to it. For iframe embedding, serve the frontend and API on the same Quin origin, configure CSP `frame-ancestors` to permit only your QUI AI site, and test third-party-cookie behavior. A first-party reverse proxy is generally more reliable than a cross-site iframe. The sample frontend assumes `/api` on its own origin; change the base path when mounting under `/quin`.

## 4. Production launch checklist — REQUIRED

- Use HTTPS; set `COOKIE_SECURE=true`; configure `ALLOWED_ORIGINS` to the **exact** public origin. Add HSTS and restrictive CSP, and prohibit embedding from untrusted origins.
- Use managed PostgreSQL, backups, migration tooling (Alembic), connection pooling and monitoring. SQLite and the included `create_all` are for a small local prototype.
- Implement **email verification, password reset, MFA or external identity provider**, and account lockout controls. Current sample provides password login but not verified email ownership.
- Add gateway/WAF **IP and account rate limits** to register/login/chat; request quotas, per-user OpenAI cost caps, abuse monitoring, and bot defenses. These are not included.
- Review CSRF, session fixation, token rotation, cookie scope, logout-all-devices, brute-force resistance and session expiration. Session tokens are hashed at rest; frontend keeps the CSRF token only in JS memory.
- Apply strict origin checks at your reverse proxy as well as application layer. If a proxy rewrites origins, explicitly configure and test it. Do not enable wildcard credentialed CORS.
- Set a **documented retention/deletion policy** and scheduled purge job; `RETENTION_DAYS` is a placeholder and is **not automatically enforced** by this starter. Delete logs, backups and provider-held data according to the actual policy; account deletion here removes only app database records.
- Ensure provider terms and OpenAI data processing settings are compatible with your privacy promise. Do not claim messages are never processed externally; product questions, profiles and recent history are sent to OpenAI as part of response generation.
- Minimize profile collection; provide a privacy policy, lawful basis/consent handling, data-subject request workflow and jurisdiction-specific review (including POPIA/GDPR where applicable).
- Add security tests, dependency pinning and scanning, monitoring without sensitive payload logging, backup restore drills, privacy review, and human support/escalation.
- Handle multi-instance Chroma deployment carefully: do not use OneDrive as a live production vector database; use a stable local persistent volume or managed vector service.
- Review RAG grounding, prompt-injection defenses, source attribution, hallucination and cross-account authorization tests. Current code is a functional foundation, not a comprehensive security boundary.

## 5. Code map

- `backend/app.py` — authentication, sessions, customer profiles, SQLite/Postgres ORM, chat API, Chroma retrieval, OpenAI response generation.
- `frontend/index.html` — responsive onboarding/login, privacy checkbox, conversation list and chat UI.
- `.env.example` — safe configuration template.
- `.gitignore` — excludes API keys, local databases and embeddings.
- `docs/HANDOFF.md` — developer deployment notes.

## 6. GitHub

```bash
git init
git add .
git commit -m "Initial Quin chatbot starter"
# Create a private GitHub repository first, then add its remote:
git remote add origin YOUR_REPOSITORY_URL
git branch -M main
git push -u origin main
```

**Use a private repository until a security review is complete.** Never commit `.env`, `quin_app.db`, `chroma_db/`, real customer conversations, or credentials. Public GitHub visibility is not the same as a production-ready public service.
