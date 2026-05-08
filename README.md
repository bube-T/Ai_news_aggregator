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

## `uv add` Commands (you run these)

Run from the project root after `uv init`.

### Core runtime deps

```bash
uv add fastapi uvicorn sqlalchemy alembic psycopg[binary] pydantic pydantic-settings
uv add feedparser trafilatura readability-lxml beautifulsoup4 lxml requests
uv add openai jinja2 apscheduler python-dotenv email-validator
```

### Optional but recommended extras

```bash
uv add tenacity structlog
```

### Dev/test deps

```bash
uv add --dev pytest pytest-asyncio httpx ruff mypy
```

## Why SMTP first for email

- Lowest setup friction for an educational build
- No additional paid vendor required to send one daily digest
- Easy upgrade path later to Resend/SendGrid by swapping `app/services/email/`

## Next Step

Once you confirm, I can create the actual folder tree on disk next.
