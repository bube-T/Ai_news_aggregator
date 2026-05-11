# AI News Aggregator (MVP Scaffold)

Python backend that ingests content from YouTube channels and blog sources, stores normalized content in PostgreSQL, generates a daily AI digest with OpenAI, and emails the result.

## Updated MVP Decisions

- LLM provider: OpenAI
- Database: local Docker PostgreSQL container (no external DB)
- ORM/migrations: SQLAlchemy + Alembic
- Blog ingestion: full-page context scraping (store extracted content body)
- Prompt organization: dedicated `agent/` folder for system prompts and templates
- Scheduling: daily digest job (24h cadence, app-level scheduler first)
- Email delivery: easiest path for viewers is SMTP via Gmail app password (free tier friendly)

## Proposed Project Structure (folders only)

```text
ai_news_aggregator/
├─ app/
│  ├─ api/
│  ├─ core/
│  ├─ db/
│  ├─ models/
│  ├─ schemas/
│  ├─ services/
│  │  ├─ ingestion/
│  │  │  ├─ youtube/
│  │  │  └─ blogs/
│  │  ├─ digest/
│  │  ├─ email/
│  │  └─ llm/
│  ├─ jobs/
│  └─ utils/
├─ agent/
│  ├─ prompts/
│  └─ templates/
├─ docker/
│  └─ postgres/
├─ scripts/
├─ tests/
│  ├─ unit/
│  └─ integration/
└─ docs/
```

## Install dependencies

Dependencies live in `pyproject.toml`. From the project root:

```bash
uv sync
uv sync --group dev   # optional: pytest, ruff, mypy, httpx
```

If you do not use `uv`, create a virtualenv and install in editable mode:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -e .
pip install pytest pytest-asyncio httpx ruff mypy
```

Copy `.env.example` to `.env` and set secrets. Start Postgres:

```bash
docker compose -f docker/postgres/docker-compose.yml up -d
```

Run the API locally:

```bash
uv run uvicorn app.main:app --reload
```

Then open `http://127.0.0.1:8000/health`.

## Why SMTP first for email

- Lowest setup friction for an educational build
- No additional paid vendor required to send one daily digest
- Easy upgrade path later to Resend/SendGrid by swapping `app/services/email/`

## Next steps (implementation)

1. SQLAlchemy models for `sources` and `articles`, then Alembic migrations.
2. Ingestion jobs (YouTube RSS, blog fetch + extract).
3. Daily digest job (OpenAI) and SMTP email send.
