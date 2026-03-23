# Forge Hunt — Context Guide

## Purpose

Forge Hunt is a business development pipeline app for **Forge Technical (Potamus Equity)**, an M&A platform acquiring commercial AV (audiovisual) system integrators across the US and Canada. The app manages ~1000 target companies through a full BD lifecycle: import seed lists, enrich with AI, qualify fit, generate personalized cold outreach, send via Microsoft Graph API, and track engagement.

The target audience for acquisition: AV integrator owners/founders/CEOs, typically $5M-$40M revenue, $500K-$4M EBITDA, commercial/enterprise/government focus.

## Tech Stack

### Backend
- **Python 3.11+** with **FastAPI** (uvicorn, hot-reload)
- **SQLite** in WAL mode (raw `sqlite3` — no ORM), UUID primary keys
- **Pydantic** for request/response models
- **Anthropic API** (Claude) for qualification and outreach drafting
- **Ollama** (llama3.1:8b, runs on local RTX 3090) for initial screening
- **Apollo.io API** for company/contact intelligence (org enrichment, people search)
- **Microsoft Graph API** for email sending via shared mailboxes
- **crawl4ai** for web scraping target company sites
- **httpx** for async HTTP calls

### Frontend
- **React 19** + **TypeScript** + **Vite 8**
- **Tailwind CSS 4** + **shadcn/ui** (component library)
- **TanStack Query** for data fetching/caching
- **React Router 7** for routing
- **Lucide React** for icons
- **Sonner** for toast notifications

### Infrastructure
- Runs on David's production PC (RTX 3090) behind **Cloudflare Tunnel**
- Accessible at `hunt.forge-technical.com` (protected by Cloudflare Access)
- Python venv at `~/venvs/docs/bin/python`
- No cloud hosting — self-hosted with tunnel

## Architecture

### Directory Structure

| Directory | Purpose |
|-----------|---------|
| `api/` | FastAPI app factory, dependencies, 10 routers |
| `api/routers/` | Route handlers: companies, contacts, enrichment, outreach, batches, users, import, analytics, sequences, test_harness |
| `db/` | Database connection (`database.py`), Pydantic models (`schema.py`), SQL migrations |
| `db/migrations/` | 9 sequential SQL migration files (001-009) |
| `pipeline/` | Core business logic: AI calls, Apollo client, Graph client, enrichment, outreach, scraping, rate limiting |
| `frontend/` | React SPA (Vite project) |
| `frontend/src/pages/` | 9 pages: Dashboard, CompanyList, CompanyDetail, BatchList, BatchDetail, Import, Outreach, Analytics, Settings |
| `frontend/src/components/` | Shared components (badges, layout, shadcn/ui primitives) |
| `frontend/src/lib/` | API client, types, queries, constants, utils |
| `prompts/` | AI prompt templates for screening, qualification, outreach drafting, LinkedIn, owner identification |
| `assets/` | Brand context doc and leadership bios |
| `scripts/` | `deploy.sh` (pull + build + test), `setup_tunnel.sh` (Cloudflare Tunnel) |
| `docs/` | Implementation plan, forward plan, meeting notes, strategy docs |
| `research/` | Market research (email platforms, sales intel, web scraping) |
| `tests/` | 23 pytest test files, 95+ tests total |
| `data/` | SQLite DB file (gitignored, auto-created on startup) |
| `runs/` | Pipeline run artifacts (gitignored) |

### Entry Points

- **Backend**: `run.py` starts uvicorn on port 8001 with hot-reload
- **Frontend dev**: `cd frontend && npm run dev` on port 5173 (proxies to :8001)
- **Production**: Frontend built to `frontend/dist/`, served by FastAPI as static files with SPA fallback
- **Deploy**: `scripts/deploy.sh` (git pull, pip install, npm build, pytest)

### Company Status State Machine

```
imported -> scraping -> scraped -> screening -> screened -> enriching -> enriched
    -> qualifying -> qualified -> researching -> researched -> ready
    -> in_outreach -> responded -> in_conversation
                   -> passed
screening -> disqualified
```

Companies are tiered 1/2/3/untiered based on qualification scores. Tier 1 gets human-reviewed draft emails; tier 2-3 get auto-sent.

### API Structure

All API routes under `/api/`:
- `/api/companies` — CRUD, status transitions, bulk operations, stats
- `/api/contacts` — CRUD for company contacts
- `/api/enrichment` — Trigger pipeline runs (screening, enrichment, web scrape)
- `/api/outreach` — Generate drafts, send emails, view replies, follow-ups
- `/api/batches` — Create/manage outreach batches, assign companies
- `/api/sequences` — Multi-step outreach sequences
- `/api/import` — CSV/Excel seed list import with preview
- `/api/analytics` — Funnel, outreach stats, domain health, timeline, credits
- `/api/users` — Team member management
- `/api/test-harness` — Dev/testing utilities
- `/api/health` — Health check

### Database

SQLite with 9 migrations. Key tables:
- `companies` — target companies with status, tier, fit_score, screen_rating
- `contacts` — people at target companies (name, title, email, LinkedIn)
- `enrichment_results` — AI/API enrichment data (JSON blobs)
- `outreach_history` — emails sent/drafted, Graph message IDs, reply tracking
- `batches` / `batch_assignments` — group companies for outreach campaigns
- `pipeline_runs` — track async pipeline execution status
- `reply_messages` — inbound replies with bounce detection
- `outreach_sequences` — multi-step follow-up definitions

## Key Files

| File | Purpose |
|------|---------|
| `run.py` | Server entry point |
| `config.py` | All configuration: env vars, constants, state machine, tier thresholds |
| `api/app.py` | FastAPI factory: CORS, router registration, SPA fallback, startup cleanup |
| `db/database.py` | SQLite connection management, migration runner |
| `db/schema.py` | All Pydantic request/response models (~360 lines) |
| `pipeline/ai_calls.py` | Claude API and Ollama calls for screening/qualification/drafting |
| `pipeline/apollo.py` | Apollo.io API client (org enrichment, people search/match) |
| `pipeline/graph.py` | Microsoft Graph API client (send email, check replies, OAuth token) |
| `pipeline/enrichment.py` | Enrichment pipeline orchestrator |
| `pipeline/outreach.py` | Outreach generation and sending logic |
| `pipeline/scraper.py` | Web scraping with crawl4ai |
| `pipeline/process.py` | Pipeline processing orchestrator |
| `prompts/screening_context.md` | Screening criteria for Ollama (what makes a good AV integrator target) |
| `assets/brand_context.md` | Brand voice, value prop, tone rules for outreach drafting |
| `docs/implementation-plan.md` | Full implementation spec for all 4 chunks |
| `docs/forward-plan.md` | Post-chunk-4 roadmap (chunks 5-10) |
| `scripts/deploy.sh` | Production deploy script |

## Deployment

- **Backend**: Self-hosted on production PC (David's machine, RTX 3090 for Ollama)
- **Backend tunnel**: Cloudflare Tunnel to `hunt.forge-technical.com` (API only)
- **Frontend**: Deployed to `deals.forge-technical.com/hunt/` via R2 (`_hunt/` prefix in `forge-deals` bucket)
- **Frontend build**: `base: "/hunt/"` in vite.config.ts, `basename="/hunt"` in BrowserRouter
- **API calls**: In production, frontend at `deals.forge-technical.com` calls API cross-origin to `hunt.forge-technical.com` (CORS configured)
- **Access control**: Cloudflare Access (Zero Trust, one-time PIN auth)
- **Deploy process**: `scripts/deploy.sh` pulls main, installs deps, builds frontend, runs tests
- **Server start**: `~/venvs/docs/bin/python run.py` (port 8001) + `cloudflared tunnel run`
- **Branch strategy**: `main` = stable/deployed. Feature branches for new chunks, PR to merge.

## Current State

### What's Built (All 4 Foundation Chunks Complete)
1. **Chunk 1** — SQLite schema, FastAPI API, seed list import, company state machine
2. **Chunk 2** — Enrichment pipeline: web scraping, Ollama screening, Claude qualification, Apollo enrichment
3. **Chunk 3** — Outreach engine: Claude draft generation, Graph API sending (all tiers), domain rotation, follow-ups
4. **Chunk 4** — React frontend: 9 pages (Dashboard, Companies, Company Detail, Batches, Batch Detail, Import, Outreach, Analytics, Settings)

### Additional Work Done (Post-Chunk 4)
- **Chunk 5**: Analytics page with funnel, outreach stats, domain health, batch performance, timeline
- **Chunk 6 Phase 1**: Pipeline redesign with real-time feedback
- **Chunk 6 Phase 2**: Reply viewer, Graph retry, bounce detection, business hours, bulk operations
- **Chunk 6 Phase 3**: LinkedIn message generation, Settings page, batch UX, import history
- Draft status workflow, conversation ID capture, test harness

### Outreach Domains
- `josh@forge-outreach.com` and `josh@joiningforge.com` for cold outreach (domain rotation)
- `josh@forge-technical.com` as primary sender (never used for cold outreach)
- Daily limit: 20 sends per mailbox
- Business hours sending: 8am-5pm ET

### Known Gaps / Future Work (from forward-plan.md)
- Automated follow-up sequence execution (table exists, logic partial)
- LinkedIn outreach integration (messages generated but not sent)
- forge-alliance.com domain still pending M365 verification
- Brand context template (`brandContext.md` at root) still has placeholder content

## Dependencies

### Python (pyproject.toml)
- `fastapi >= 0.115.0` — web framework
- `uvicorn[standard] >= 0.34.0` — ASGI server
- `pydantic >= 2.0` — data validation
- `anthropic >= 0.40.0` — Claude API client
- `httpx >= 0.27.0` — async HTTP client (Apollo, Graph)
- `crawl4ai >= 0.4.0` — web scraping
- `openpyxl >= 3.1.0` — Excel file parsing
- `python-dotenv >= 1.0` — env var loading
- `python-multipart >= 0.0.18` — file upload support
- `pytest >= 8.0` (dev)

### Frontend (package.json)
- `react 19`, `react-dom 19`, `react-router-dom 7`
- `@tanstack/react-query 5` — server state management
- `shadcn 4` + `tailwindcss 4` — UI components and styling
- `lucide-react` — icons
- `sonner` — toasts
- `vite 8` — bundler

### External Services
- **Apollo.io** — company/contact intelligence (API key required)
- **Anthropic (Claude)** — qualification scoring, outreach draft generation
- **Ollama** — local LLM screening (llama3.1:8b on RTX 3090)
- **Microsoft Graph API** — email sending via M365 shared mailboxes (OAuth client credentials)
- **Cloudflare** — Tunnel + Access for production hosting

### Required Environment Variables (.env)
- `APOLLO_API_KEY` — Apollo.io master key
- `ANTHROPIC_API_KEY` — Claude API key
- `GRAPH_CLIENT_ID`, `GRAPH_CLIENT_SECRET`, `GRAPH_TENANT_ID` — M365 OAuth
- `OLLAMA_HOST` (optional, defaults to localhost:11434)
- `FORGE_HOST`, `FORGE_PORT` (optional, defaults to 0.0.0.0:8001)

## Notes

- **No ORM**: Raw SQLite with `sqlite3.Row` — deliberate choice given ~1000 row scale. `check_same_thread=False` needed for async FastAPI.
- **Apollo is intel-only**: All email sending goes through Microsoft Graph API, never Apollo. Apollo provides enrichment data (org info, people search, verified emails).
- **Tier-based outreach**: Tier 1 (high-value) creates drafts for human review. Tier 2-3 auto-sends. This is a core design principle.
- **Startup cleanup**: On server restart, orphaned pipeline runs are marked failed and companies stuck in transient statuses are rolled back.
- **95+ tests**: Comprehensive test coverage across all pipeline components and API routes.
- **4 users**: Josh (CEO), David (COO), Jackson, and one more team member use the dashboard.
- **DKIM configured**: for forge-technical.com and forge-outreach.com. joiningforge.com DKIM was fixed (CNAME targets had wrong subdomain).
