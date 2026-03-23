# Context Guide: brandExploration

**Path:** `/home/crow/projects/brandExploration/`
**Last updated:** 2026-03-21

---

## Purpose

This is a **strategic planning workspace** -- not a software project. It contains documents for developing the brand, strategy, and visual identity of **Forge** (DBA) / **Forge Technical** (legal entity candidate), a commercial AV (audio/video) services platform being built through acquisition rollup.

The platform acquires independent commercial AV integrators and provides shared back-office infrastructure while preserving each company's local brand, culture, and leadership ("a forge, not a factory").

Two growth engines:
- **Critical Infrastructure** -- healthcare, government, life safety (nurse call, access control, mass notification)
- **Workplace Experience** -- enterprise, higher education, collaboration

Financial targets: $200-250M+ revenue, $25-30M+ EBITDA at Year 5-7 exit. Phase 1: 5-7 acquisitions to $80-100M revenue in 18-24 months. ~$80-90M capital raise (equity + debt).

---

## Tech Stack

There is no application code in this repo. It is a pure documentation/strategy workspace.

- **File formats:** Markdown (.md), HTML (brand guides, palette comparisons), PDF, PNG, DOCX
- **No build system, no tests, no linting, no package.json**

### Related Repos (code lives here, not in brandExploration)

| Repo | Purpose | WSL Path |
|------|---------|----------|
| `forge-technical` | Public website (forge-technical.com) | `/mnt/c/Users/drj07/OneDrive/Documents/GitHub/forge-technical` |
| `phoenixdeploy-portal` | Internal portal site + brand guide | `/mnt/c/Users/drj07/OneDrive/Documents/GitHub/phoenixdeploy-portal` |

Both deployed on **Cloudflare Pages**. The public site uses:
- Cloudflare Pages Functions (serverless) for contact form
- Resend API for email delivery
- Cloudflare Turnstile for bot protection
- Cloudflare Access for preview branch gating

---

## Architecture / Directory Structure

```
brandExploration/
├── CLAUDE.md              # Project context + current status (the most important file)
├── brandStory.md          # Canonical brand narrative (aligned to live site)
├── imagingNotes.md        # Photography integration: per-section rationale + replacement criteria
├── unifiedStrategy.md     # Full M&A strategy doc (~77KB): acquisition model, financials, phases, exit thesis
├── session-summary.md     # Running log of all 15 sessions (~110KB)
├── .gitignore
│
├── reference/             # Decided material, still consulted
│   ├── brandStoriesv1.md  # Original brand narratives (Josh AI + DJ AI)
│   ├── colorConsiderations.md  # Color research, 2026 trends, accessibility
│   ├── paletteComparison.html  # Forge Alloy visual spec + comparison
│   ├── sitePlan.md        # Section-by-section site narrative plan
│   ├── BrandingConcepts.pdf    # Visual logo concepts from Patrick
│   └── partnershipSection.png  # Mockup, not yet incorporated
│
├── logoConcepts/          # Logo research (5 concepts + executive summary)
│   ├── executive-summary.md
│   ├── concept-01-flame.md
│   ├── concept-02-circuit-nodes.md
│   ├── concept-03-signal-arc.md    # Top scorer (6.4/10)
│   ├── concept-04-weld-seam.md
│   └── concept-05-kinetic-spark.md # Tied second (5.1/10)
│
└── archive/               # Completed historical work
    ├── naming/            # 8 files: naming research, evaluations, exec summary, recommendation
    ├── palette/           # 5 files: palette explorations, pre-Forge brand guide, implementation plan
    ├── site/              # 5 files: stale brand guide copy, comments summary, image mapping, pitch deck context
    ├── forgeLogoText.png
    └── team.png
```

---

## Key Files

| File | What It Does |
|------|-------------|
| **`CLAUDE.md`** | Single source of truth for project context, current status, decisions made, and next actions. Read this first -- it is comprehensive and up-to-date as of session 15. |
| **`unifiedStrategy.md`** | The full M&A and platform strategy (~77KB). Covers acquisition targets, two-engine model, financial projections, phase sequencing, exit thesis. The core business document. |
| **`brandStory.md`** | Canonical brand narrative. Defines the "forge not factory" metaphor, three capabilities (Services, Support, Systems), three differentiators, tagline, and promise. Supersedes older narratives. |
| **`session-summary.md`** | Complete history of all 15 working sessions (~110KB). Every decision, file change, and problem is logged here. Invaluable for understanding how the project evolved. |
| **`imagingNotes.md`** | Photography decisions for the website. Per-section rationale, what images were chosen and why, replacement criteria for future swaps. |
| **`logoConcepts/executive-summary.md`** | Comparative evaluation of 5 logo concepts. Signal Arc (03) and Kinetic Spark (05) are the two finalists for a designer brief. |

---

## Deployment

This repo itself is not deployed. The live artifacts it informs:

### forge-technical.com (Public Site)
- **Hosting:** Cloudflare Pages (`forge-technical` project)
- **Zone:** `bf051adbf17230ad1f73a6f15c05b8b0`
- **Account:** `78e7db707d120ce122abeca1e3e38c2d`
- **Pages:** `index.html` (seller/investor), `Leadership.html` (bios), `LetsTalk.html` (contact form)
- **Backend:** Pages Function `functions/api/contact.js` -- validates, verifies Turnstile, sends via Resend
- **Env vars (Pages dashboard):** `TURNSTILE_SECRET`, `CONTACT_EMAIL`, `RESEND_API_KEY`
- **Preview pipeline:** `preview` branch deploys to `preview.forge-technical.com`, gated by Cloudflare Access (OTP for `@forge-technical.com` and `@potamusequity.com`)
- **Production:** `main` branch auto-deploys

### phoenixdeploy-portal (Internal Portal)
- **Hosting:** Cloudflare Pages (`phoenixdeploy-portal` project)
- **D1 Database:** `forge-comments` (ID: `90ce6af5-7bee-481a-9c51-ff9fb311de05`)
- **Access:** Comments section gated by Cloudflare Access
- **Houses:** `ForgeBrandGuide.html` (primary brand guide, 9 pages)

### CSS Architecture (forge-technical)
- `tokens.css` -> `alloy.css` -> `mode.css` -> `components.css`
- Font system: Halogen (Typekit `leq1sdk`) + Michroma fallback | Space Grotesk body | DM Mono labels
- Dark/light mode toggle via `styles/settings-bar.js`

---

## Current State (as of session 15, 2026-03-02)

### Decided / Complete
- **Brand name:** Forge (DBA). Legal second word TBD -- "Forge Technical" is the leading candidate pending trademark search.
- **Color palette:** Forge Alloy -- single palette, palette switcher removed. Blue leads (`#64A8E6`), Forge Copper grounds (`#D37A22`).
- **Brand archetype:** Builder (primary) + Explorer (secondary)
- **Tagline:** "Tempered for Growth. Built to Last."
- **Brand promise:** "Your name stays on the door. Your culture stays in the building."
- **Public site:** Live and fully operational at forge-technical.com (3 pages + contact form)
- **Portal site:** Built and deployed with comments system
- **Brand guide:** 9 pages in ForgeBrandGuide.html (lives in phoenixdeploy-portal repo)
- **Logo research:** 5 concepts evaluated; Signal Arc (03) + Kinetic Spark (05) shortlisted
- **Brand story:** Written and aligned to live site
- **Directory reorganization:** Root decluttered to 4 active docs
- **Social media project:** Initialized at `~/projects/social/` with full context structure

### In Progress / Pending
- Photography changes on `preview` branch awaiting partner review before merge to `main`
- LinkedIn company page setup (tagline, description, specialties, cover image drafted)
- Designer brief for logo (Signal Arc vs Kinetic Spark directions)
- Trademark search on "Forge Technical"
- Acquisition of `forgetechnical.com` domain (parked squatter)
- ForgeBrandGuide.html typography section rewrite
- `reference/partnershipSection.png` not yet incorporated into site

### Domains Secured
crossfoxalliance.com, crossfoxav.com, crossfoxcollective.ai, crossfoxcollective.com, crossfoxsystems.com, forgeav.com, forgesystems.ltd, forge-technical.com, forgetechnology.ai, crossfoxtechnologies.com

---

## Dependencies / External Services

| Service | Purpose |
|---------|---------|
| **Cloudflare Pages** | Hosting for both sites |
| **Cloudflare Access** | OTP-gated preview and comments |
| **Cloudflare D1** | Comments database for portal |
| **Cloudflare Turnstile** | Bot protection on contact form |
| **Resend** | Email delivery for contact form (replaced MailChannels) |
| **Adobe Typekit** | Halogen font (`leq1sdk`) |
| **Wrangler 4.69.0** | Cloudflare CLI for deployments |

---

## Notes

- **This is a strategy/brand workspace, not code.** There are no build steps, no tests, no CI. The "deployment" artifacts live in separate repos (`forge-technical`, `phoenixdeploy-portal`).
- **CLAUDE.md is the canonical reference.** It is meticulously maintained and contains the most current status, decisions, file locations, and next actions. Always read it first.
- **session-summary.md is the decision log.** Every session's decisions, rationale, problems, and file changes are recorded there. It is large (~110KB, 15 sessions) but invaluable for understanding "why" something was done.
- **The "Forge" name is decided but the legal entity name is not.** "Forge Technical" is the leading candidate but trademark clearance and domain acquisition are still pending.
- **Two related projects exist outside this repo:**
  - `~/projects/social/` -- social media content generation context (LinkedIn strategy, voice guide, bios)
  - The actual website repos live at Windows-side GitHub paths under `/mnt/c/Users/drj07/OneDrive/Documents/GitHub/`
- **MailChannels was replaced by Resend** in session 11 -- MailChannels ended free service Oct 2024 and was silently dropping all mail.
- **Image assets** for the site are WebP (quality 85, two sizes: 1600px/800px) converted via ffmpeg. Source files were in a `temp/` directory that has since been deleted.
