# DD Orchestrator — Context Guide

> Last updated: 2026-03-21

## Purpose

DD Orchestrator is an AI-powered due diligence automation platform for commercial AV integrator acquisitions. It coordinates three AI agents (Claude, Gemini, Ollama) across a 6-phase due diligence workflow, producing structured analysis, cross-phase synthesis, and formatted HTML/PDF reports.

The project serves Forge Technical's M&A pipeline — automating the labor-intensive DD process from document ingestion through Investment Committee memo generation. A companion Cloudflare Worker portal at `deals.forge-technical.com/crucible/` provides stakeholder-facing read-only access to reports and an AI chat interface per deal. The worker also serves the unified Forge Deals landing page at `deals.forge-technical.com/` and the forge-hunt frontend at `deals.forge-technical.com/hunt/`.

## Tech Stack

### Python CLI (primary)
- **Python 3.11+** (3.12 in practice)
- **Click** — CLI framework
- **Rich** — terminal output formatting
- **PyYAML** — config and skill definitions
- **Jinja2** — HTML report templates
- **WeasyPrint** — HTML-to-PDF conversion
- **PyPDF / PyMuPDF** — PDF parsing
- **python-magic** — file type detection
- **openpyxl / python-docx / Pillow** — document extraction
- **pytest** — test suite (815+ tests as of last session)

### Cloudflare Worker (portal)
- **TypeScript** + **Hono** — HTTP routing
- **Cloudflare R2** — static file storage (reports, outputs, research)
- **Durable Objects** (SQLite) — DealChat AI chat persistence
- **Vercel AI SDK** + **@ai-sdk/anthropic** — chat completions
- **React** — chat widget (built via esbuild, embedded in portal)
- **Wrangler** — deployment tooling

### AI Agents
| Agent | Role | Interface |
|-------|------|-----------|
| **Claude** (Anthropic API) | Analysis, synthesis, IC memos, valuation | Subprocess CLI / API key |
| **Gemini** (Google API) | Research, competitive intelligence, market analysis | Subprocess CLI / API key |
| **Ollama** (local) | Document classification (qwen3:14b), reasoning/gap analysis (qwq:32b) | HTTP to localhost:11434 |

### External Services
- **Cloudflare** — Workers, R2, Access (ACL via Entra ID SSO)
- **Microsoft Entra ID** — SSO for portal access control
- API keys for Anthropic and Google (data not used for training)

## Architecture

Two codebases in one repo:

### Python CLI — `src/dd_orchestrator/`

```
src/dd_orchestrator/
├── cli.py               # Click entry point (48KB) — all commands: init, ingest, plan, run, report, publish, sync, doctor, etc.
├── agents/              # Agent wrappers: claude, gemini, ollama, codex (legacy), base class + factory
├── core/
│   ├── orchestrator.py  # Main orchestrator — loads deal dir, runs phases via WorkflowEngine
│   ├── workflow.py      # WorkflowEngine — reads complete_dd.yaml, parallel execution (ThreadPoolExecutor, max 4)
│   ├── state.py         # Workflow state persistence (workflow_state.json)
│   └── config.py        # Config loading/validation
├── ingestion/           # scan → classify (Ollama) → extract → inventory.json
├── planning/            # AI-driven execution plan generation (Claude meta-planning)
├── reports/
│   ├── generator.py     # ReportGenerator — Jinja2 rendering, data preprocessing
│   ├── validator.py     # Post-generation claim verification
│   └── templates/       # 14 HTML templates (base, landing, ic_memo, valuation, financial, etc.)
├── reporting/           # Self-reporting module
├── research/            # Multi-pass research pipeline (Gemini search + Claude synthesis + Ollama gap analysis)
├── skills/
│   ├── definitions/     # 25 YAML skill definitions (prompts, inputs, outputs, agent assignment)
│   └── implementations/ # 25 Python skill implementations (one per skill)
├── synthesis/           # Cross-phase synthesis engine (Claude) — contradictions, risk flags, deal-breakers
├── tracking/            # Cost tracking per skill/phase/agent
├── utils/               # Shared utilities
└── workflows/
    └── complete_dd.yaml # Master workflow — 6 phases, skill ordering, dependencies, parallel groups
```

### Cloudflare Worker — `workers/deals-portal/`

```
workers/deals-portal/
├── src/
│   ├── server.ts        # Hono routes (main entry point)
│   ├── static.ts        # R2 static file serving
│   ├── agent.ts         # DealChat — AIChatAgent Durable Object with SQLite persistence
│   ├── classifier.ts    # Adaptive query routing → Haiku/Sonnet/Opus tier
│   ├── context.ts       # CONTEXT_MAP — which R2 files feed each report page (200KB guard)
│   ├── prompt.ts        # System prompts for chat
│   ├── verify.ts        # Numerical claim extraction + cross-check against R2 data
│   ├── env.ts           # Environment type definitions
│   └── widget/          # React chat widget (esbuild → public/)
├── wrangler.toml        # Worker config: R2 binding, Durable Objects, route pattern
└── package.json         # Hono, AI SDK, React, Wrangler
```

### Data Flow

```
raw/ docs → ingest → inventory.json → plan → execution_plan.json
→ run phase{1-6} → outputs/{claude,gemini,ollama}/{skill}.json
→ synthesis → analysis/synthesis_phase{N}.json
→ report → reports/*.html → publish → R2 → portal
```

## Key Files

| File | Purpose |
|------|---------|
| `src/dd_orchestrator/cli.py` | All CLI commands — the primary user interface (48KB, largest file) |
| `src/dd_orchestrator/core/orchestrator.py` | Orchestrator — deal loading, phase execution coordination |
| `src/dd_orchestrator/core/workflow.py` | WorkflowEngine — reads YAML workflow, parallel skill execution |
| `src/dd_orchestrator/workflows/complete_dd.yaml` | Master workflow definition — 6 phases, skill order, dependencies |
| `src/dd_orchestrator/agents/__init__.py` | Agent factory — `get_agent(name)` returns Claude/Gemini/Ollama |
| `src/dd_orchestrator/reports/generator.py` | Report generation — Jinja2 + data preprocessing (valuation, financial, etc.) |
| `src/dd_orchestrator/synthesis/engine.py` | Cross-phase synthesis via Claude |
| `src/dd_orchestrator/research/` | Multi-pass research pipeline (cache, searcher, synthesizer, pipeline) |
| `workers/deals-portal/src/server.ts` | Portal HTTP routes (Hono) |
| `workers/deals-portal/src/agent.ts` | DealChat Durable Object — per-deal AI chat |
| `workers/deals-portal/wrangler.toml` | Worker deployment config (R2, DO, route) |
| `CLAUDE.md` | Build/run commands, architecture overview, conventions |
| `IMPLEMENTATION_PLAN.md` | Planned (not yet built) local web UI (FastAPI + HTMX) |
| `progress.md` | Detailed milestone timeline, 20+ sessions |

## Deployment

### Portal (Cloudflare Worker)
- **URL**: `deals.forge-technical.com` (unified site)
  - `/` — Forge Deals landing page (hardcoded in worker)
  - `/crucible/*` — Deal Desk (dd-orchestrator reports, chat, discussion)
  - `/hunt/*` — Deal Sourcing (forge-hunt React SPA, served from R2 `_hunt/` prefix)
  - `/agents/*` — WebSocket agent routes (DealChat)
- **Worker name**: `forge-deals-portal`
- **Platform**: Cloudflare Workers
- **Storage**: R2 bucket `forge-deals` (HTML reports, JSON outputs, research, hunt frontend)
- **Chat**: Durable Objects with SQLite (DealChat class)
- **Auth**: Cloudflare Access + Microsoft Entra ID SSO
- **Deploy**: `cd workers/deals-portal && npm run deploy` (builds widget first, then wrangler deploy)
- **Path routing**: fetch handler strips `/dd` or `/hunt` prefix before passing to Hono/R2. R2 key structure unchanged.

### Python CLI
- **Install**: `pip install -e .` (editable install from source)
- **Entry point**: `dd-orchestrator` (Click CLI)
- **Local only** — runs on developer machine, calls cloud APIs for AI, publishes to R2

### Three-Step Portal Update Pipeline
1. `dd-orchestrator report --deal ~/deals/X` — regenerate local HTML from templates + data
2. `dd-orchestrator publish --all-deals` — upload HTML/JSON/research to R2
3. `cd workers/deals-portal && npm run deploy` — only if worker TypeScript code changed

## Deal Directory Layout

Each deal lives at `~/deals/CompanyName/`:
```
config.yaml, inventory.json, raw/, processed/, inputs/,
outputs/{claude,gemini,ollama}/, analysis/, research/,
reports/ (overview.html, ic_memo.html/pdf, etc.),
state/ (workflow_state.json, execution_plan.json, cost_summary.json, execution.log),
research_cache/
```

## Pipeline Phases

| Phase | Focus | Agent Mix | Key Skills |
|-------|-------|-----------|------------|
| 1: Screening | Market fit | Gemini + Claude | MSA density, company intel, competitive position, strategic fit |
| 2: Financial | Numbers | Ollama + Claude | Financial extract, normalization, revenue quality, margin, working capital, QoE |
| 3: Operational | Operations | Gemini + Claude | Tech stack, service delivery, human capital, customer mapping |
| 4: Strategic | Strategy | Gemini + Claude | Market deep dive, synergy planning, deal structure |
| 5: Compliance | Legal | Claude + Gemini | Contract legal review, cybersecurity assessment |
| 6: Final | Decision | Claude | Risk assessment, IC memo, final valuation |

Cross-phase synthesis runs automatically after each phase (Claude).

## Current State

- **Status**: Alpha (v0.1.0), 80 commits, 26 sessions logged
- **Test suite**: 815+ tests passing (pytest)
- **Phase completion**: P2 (planning + research) complete. P3 (cost tracking) in progress.
- **Active deals**: At least 2 — Lumibuild, azsound (Project Sun)
- **What's built**:
  - Full 6-phase DD pipeline (ingest → plan → run → synthesize → report)
  - 25 skills with YAML definitions + Python implementations
  - 3 AI agents (Claude, Gemini, Ollama) with factory pattern
  - Multi-pass research pipeline (Gemini search → Claude synthesis → Ollama gap analysis)
  - AI-driven execution planning (Claude meta-planner)
  - HTML/PDF report generation (14 templates, 8+ report types)
  - Portal with R2 static serving + AI chat per deal
  - Publish/sync commands for R2
  - Prompt scope boundaries to reduce agent output redundancy
  - Report navigation, data coverage gap banners, claim verification

- **What's planned/in progress**:
  - Local web UI (FastAPI + HTMX + SSE) — designed but not started (`IMPLEMENTATION_PLAN.md`)
  - P3 cost/token tracking foundation
  - Real-agent dry-run on Project Sun
  - API key migration (replace subprocess CLI wrappers with API client libraries)
  - Finance-specialized local LLMs (qwen-finance-8b considered)
  - Citations API integration (waiting on vercel/ai#11968)

## Dependencies

### Python (pyproject.toml)
- click, pyyaml, rich, pypdf, pymupdf, python-magic, Pillow, openpyxl, python-docx
- Dev: pytest, pytest-cov, pytest-mock, black, isort, mypy

### Worker (package.json)
- @ai-sdk/anthropic, @cloudflare/ai-chat, agents, ai, hono, react, react-dom, zod
- Dev: @cloudflare/workers-types, esbuild, typescript, wrangler

### External Services
- Anthropic API (Claude) — analysis, synthesis, chat
- Google Gemini API — research, competitive intelligence
- Ollama (local, port 11434) — qwen3:14b (classification), qwq:32b (reasoning)
- Cloudflare R2 — report/output storage
- Cloudflare Workers — portal hosting
- Cloudflare Access + Microsoft Entra ID — portal auth/SSO

## Notes

- **Data privacy by design**: Raw deal documents (financials, contracts) never leave the machine — classified/extracted locally via Ollama. Only AI-generated analysis goes through cloud APIs. API keys used (not CLI login) to prevent training data exposure.
- **No earnouts**: Deal structures use equity rollover only. This is enforced in skill prompts (final_valuation, risk_assessment, ic_memo).
- **Two entry points, one repo**: Python CLI (`dd-orchestrator`) and TypeScript worker (`workers/deals-portal/`) are independent but connected through R2 (CLI publishes, worker serves).
- **Codex eliminated**: Was used for content extraction; replaced by pure Python table extraction + Ollama classification.
- **`--force` flag**: Bypasses both precondition checks and execution plan — needed for phase reruns on mature deals.
- **Always use `dd-orchestrator` CLI entry point**, not `python -m dd_orchestrator.cli` (known issue #16 — the latter may silently no-op).
- **MCP debug server**: `deals-portal-debug` configured in `.mcp.json` for Claude Code integration.
- **Engagement phases** (Forge M&A workflow): Screening → Analysis → LOI → Exclusivity → Closed — tracked in deal config.yaml.
- **Per-deal cost**: Estimated $250-400 after local model refactor (down from $500-750).
- **GitHub repo**: `github.com/drj0717/dd-orchestrator` (private).
