# Forge Unified Brand Guide

> Canonical reference for aligning all Forge applications to a single visual identity.
> Source of truth: `forge-technical` design system + `brandExploration` brand decisions.
> Last updated: 2026-03-21

---

## 1. Current State Audit

### forge-technical (public marketing site)
**Status: ON-BRAND — This IS the brand.**
- Custom CSS with semantic design tokens (`tokens.css` → `alloy.css` → `mode.css` → `components.css` → `site.css`)
- Alloy palette: Charcoal + Glacier Blue + Forge Copper
- Fonts: Halogen (display), Space Grotesk (body), DM Mono (mono), Roboto Slab (slab)
- Full dark/light mode with system preference detection
- Zero hardcoded colors — all via `var(--*)`

### forge-hunt (BD pipeline app)
**Status: OFF-BRAND — Needs migration.**
- Tailwind CSS 4.x with OKLCH color space (shadcn/ui defaults)
- Geist Variable font (not a Forge font)
- Generic grayscale theme — no Forge colors anywhere
- shadcn/ui + Base UI + CVA components (good architecture, wrong tokens)
- Lucide React icons
- Has dark mode support (`.dark` class) — good, just needs correct colors

### dd-orchestrator (due diligence platform)
**Status: PARTIALLY ALIGNED — Uses Forge navy, needs token migration.**
- 3 interfaces: PDF reports, FastAPI UI, Cloudflare Worker chat widget
- Uses `#0d2137` (Forge's dark navy) and `#1a5276` as primary — close but not canonical
- Helvetica Neue font — not a Forge font
- All colors hardcoded as hex — no CSS variables
- No dark/light mode toggle
- Bootstrap-adjacent status colors (greens, yellows, reds)

---

## 2. Canonical Forge Alloy Palette

### Brand Colors

| Token | Dark Mode | Light Mode | Usage |
|-------|-----------|------------|-------|
| `--accent-primary` | `#64A8E6` (Glacier Blue) | `#2E6AA8` (Deep Blue) | Primary CTA, links, active states, focus rings |
| `--accent-primary-soft` | `rgba(100,168,230,0.1)` | `rgba(46,106,168,0.1)` | Hover backgrounds, subtle fills |
| `--accent-primary-border` | `rgba(100,168,230,0.28)` | `rgba(46,106,168,0.28)` | Focused borders, accent outlines |
| `--accent-secondary` | `#D37A22` (Forge Copper) | `#8A5210` (Deep Copper) | Secondary accent, endorsement badges, gradient |
| `--accent-secondary-soft` | `rgba(211,122,34,0.1)` | `rgba(138,82,16,0.1)` | Secondary hover fills |
| `--accent-secondary-border` | `rgba(211,122,34,0.28)` | `rgba(138,82,16,0.28)` | Secondary accent borders |
| `--accent-glow` | `#82BDF0` | `#4A8EC7` | Hover emphasis, glow effects |
| `--gradient-bar` | `linear-gradient(90deg, #D37A22 0%, #64A8E6 100%)` | `linear-gradient(90deg, #8A5210 0%, #2E6AA8 100%)` | Brand accent bar, dividers |

### Backgrounds

| Token | Dark Mode | Light Mode | Usage |
|-------|-----------|------------|-------|
| `--bg-primary` | `#222830` (Charcoal) | `#F4F7FA` (Light Canvas) | Page background |
| `--bg-surface` | `#2A3545` (Dark Surface) | `#EBE8E3` (Warm Tan) | Cards, panels, sidebars |
| `--bg-surface-hover` | `#324055` | `#e0dcd6` | Interactive surface hover |
| `--bg-surface-alt` | `rgba(42,53,69,0.5)` | `rgba(244,247,250,0.5)` | Overlays, frosted backgrounds |

### Text

| Token | Dark Mode | Light Mode | Usage |
|-------|-----------|------------|-------|
| `--text-primary` | `#F5F7FA` (Off-White) | `#222830` (Charcoal) | Headings, primary content |
| `--text-secondary` | `rgba(245,247,250,0.55)` | `rgba(34,40,48,0.65)` | Body text, descriptions |
| `--text-muted` | `rgba(245,247,250,0.50)` | `rgba(34,40,48,0.58)` | Labels, metadata |
| `--text-faint` | `rgba(245,247,250,0.38)` | `rgba(34,40,48,0.45)` | Placeholders, disabled text |

### Borders & Shadows

| Token | Dark Mode | Light Mode | Usage |
|-------|-----------|------------|-------|
| `--border-color` | `rgba(255,255,255,0.07)` | `rgba(0,0,0,0.08)` | Default borders |
| `--border-subtle` | `rgba(255,255,255,0.05)` | `rgba(0,0,0,0.05)` | Minimal dividers |
| `--border-strong` | `rgba(255,255,255,0.12)` | `rgba(0,0,0,0.14)` | Emphasized borders |
| `--card-shadow` | `rgba(0,0,0,0.4)` | `rgba(0,0,0,0.10)` | Card elevation |
| `--card-shadow-hover` | `rgba(0,0,0,0.55)` | `rgba(0,0,0,0.18)` | Card hover elevation |

### Semantic Status Colors (Shared Across Apps)

These extend the Alloy palette for app-specific status indicators. They should feel at home on both dark and light Forge surfaces.

| Status | Background (Dark) | Text (Dark) | Background (Light) | Text (Light) | Usage |
|--------|-------------------|-------------|---------------------|--------------|-------|
| Success/Approve | `rgba(40,167,69,0.15)` | `#5cb85c` | `#d4edda` | `#155724` | Qualified, approved, complete |
| Warning/Caution | `rgba(255,193,7,0.15)` | `#ffc107` | `#fff3cd` | `#856404` | In-progress, needs attention |
| Danger/Reject | `rgba(220,53,69,0.15)` | `#e74c3c` | `#f8d7da` | `#721c24` | Disqualified, rejected, errors |
| Info | `rgba(100,168,230,0.15)` | `#64A8E6` | `#e8f4f8` | `#1a5276` | Informational (uses Glacier Blue) |
| Neutral | `rgba(255,255,255,0.06)` | `var(--text-muted)` | `#f5f5f5` | `#757575` | Default, unset |

---

## 3. Typography

### Font Stack

| Role | Font Family | Source | CSS Token | Usage |
|------|-------------|--------|-----------|-------|
| **Display** | Halogen, Michroma | Adobe Typekit (`leq1sdk`) | `--font-display` | Logos, section headings, brand marks |
| **Body** | Space Grotesk | Google Fonts (300, 400, 500, 600) | `--font-body` | Body text, UI labels, descriptions |
| **Slab** | Roboto Slab | Google Fonts (300, 400, 600) | `--font-slab` | Taglines, quotes, emphasis blocks |
| **Mono** | DM Mono | Google Fonts (400, 500) | `--font-mono` | Labels, tags, metadata, code, eyebrows |

### App Typography Scale

For SaaS application interfaces (forge-hunt, dd-orchestrator, forge-deals), use this scale derived from the marketing site but tuned for dense data UIs:

| Element | Font | Size | Weight | Tracking | Notes |
|---------|------|------|--------|----------|-------|
| App logo/brand | `--font-display` | 15–18px | 700 | 0.27em | Uppercase, sidebar/nav |
| Page title | `--font-body` | 20–24px | 600 | -0.01em | Top of page |
| Section heading | `--font-body` | 16–18px | 600 | normal | Card headers, panel titles |
| Eyebrow/label | `--font-mono` | 10–12px | 500 | 0.14em | Uppercase, muted color |
| Body text | `--font-body` | 14–15px | 400 | normal | Standard content |
| Table text | `--font-body` | 13–14px | 400 | normal | Data tables |
| Small/meta | `--font-body` | 12px | 400 | normal | Timestamps, IDs |
| Badge text | `--font-mono` | 11px | 500 | 0.06em | Status badges, tags |
| Numeric data | `--font-mono` | 13px | 400 | normal | Financial data, scores, IDs |

### Font Loading

All Forge apps must include:
```html
<!-- Typekit (Halogen + DM Mono) -->
<link rel="stylesheet" href="https://use.typekit.net/leq1sdk.css">

<!-- Google Fonts (Space Grotesk + Roboto Slab) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600&family=Roboto+Slab:wght@300;400;600&display=swap" rel="stylesheet">
```

---

## 4. Layout — Unified App Shell (forge-deals)

The forge-deals project will provide a unified shell that hosts forge-hunt, dd-orchestrator portal, and future Forge tools as sections of one SaaS experience.

### Shell Structure

```
┌──────────────────────────────────────────────────┐
│  Top Bar (40px) — gradient bar accent             │
│  FORGE wordmark (--font-display) │ app nav │ mode│
├────────┬─────────────────────────────────────────┤
│        │                                          │
│  Side  │  Main Content Area                       │
│  Nav   │  (--bg-primary)                          │
│  56px  │                                          │
│  wide  │  Cards use --bg-surface                  │
│        │  with --card-shadow                      │
│(--bg-  │                                          │
│surface)│                                          │
│        │                                          │
├────────┴─────────────────────────────────────────┤
│  4px gradient bar (--gradient-bar)                │
└──────────────────────────────────────────────────┘
```

### Top Bar
- Height: 40px, background: `var(--bg-surface)`
- FORGE wordmark: `--font-display`, 15px, uppercase, letter-spacing 0.27em
- Navigation links: `--font-mono`, 10px, uppercase, letter-spacing 0.14em
- Active link: `color: var(--accent-primary)`
- Mode toggle: sun/moon (dark/light), stored in `localStorage('forge-mode')`
- Bottom border: 4px `var(--gradient-bar)` (copper → blue)

### Sidebar
- Width: 224px (w-56), background: `var(--bg-surface)`
- Section labels: `--font-mono`, 10px, uppercase, muted
- Nav items: `--font-body`, 14px, weight 500, rounded-md
- Active: `background: var(--accent-primary-soft)`, `color: var(--accent-primary)`
- Hover: `background: var(--bg-surface-hover)`
- App sections separated by subtle dividers (`--border-subtle`)

### Main Content
- Background: `var(--bg-primary)`
- Padding: 24px
- Cards: `var(--bg-surface)`, border `var(--border-color)`, border-radius 8px, shadow `var(--card-shadow)`
- Card hover: shadow `var(--card-shadow-hover)`, translateY(-1px)

---

## 5. Shared Component Standards

### Buttons

| Variant | Background | Text | Border | Hover |
|---------|------------|------|--------|-------|
| Primary | `var(--accent-primary)` | white | none | `var(--accent-glow)` |
| Secondary | transparent | `var(--accent-primary)` | `var(--accent-primary-border)` | `var(--accent-primary-soft)` bg |
| Ghost | transparent | `var(--text-secondary)` | none | `var(--bg-surface-hover)` bg |
| Destructive | `rgba(220,53,69,0.1)` | `#e74c3c` (dark) / `#721c24` (light) | danger border | darker bg |
| Copper accent | `var(--accent-secondary)` | white | none | lighter copper |

All buttons: `--font-body`, 14px, weight 500, border-radius 6px, padding 8px 16px.
Small variant: 12px, padding 6px 12px.

### Cards

```css
.forge-card {
  background: var(--bg-surface);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 4px 12px var(--card-shadow);
  transition: box-shadow 0.2s ease, transform 0.2s ease;
}
.forge-card:hover {
  box-shadow: 0 8px 24px var(--card-shadow-hover);
  transform: translateY(-1px);
}
```

Accent variant (top border):
```css
.forge-card--accent {
  border-top: 3px solid var(--accent-primary);
}
.forge-card--copper {
  border-top: 3px solid var(--accent-secondary);
}
```

### Tables

| Element | Dark Mode | Light Mode |
|---------|-----------|------------|
| Header bg | `var(--bg-surface)` | `var(--bg-surface)` |
| Header text | `var(--text-primary)`, weight 600 | same |
| Row border | `var(--border-subtle)` | same |
| Row hover | `var(--bg-surface-hover)` | same |
| Even row | `var(--bg-surface-alt)` | same |
| Cell text | `var(--text-secondary)`, 13–14px | same |

Font: `--font-body` for text columns, `--font-mono` for numeric columns.

### Badges / Status Indicators

```css
.forge-badge {
  font-family: var(--font-mono);
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.06em;
  padding: 2px 8px;
  border-radius: 100px;  /* pill shape */
  text-transform: uppercase;
  white-space: nowrap;
}
```

Use semantic status colors from Section 2.

### Form Inputs

```css
.forge-input {
  font-family: var(--font-body);
  font-size: 14px;
  padding: 8px 12px;
  background: var(--bg-primary);
  color: var(--text-primary);
  border: 1px solid var(--border-strong);
  border-radius: 6px;
  transition: border-color 0.15s, box-shadow 0.15s;
}
.forge-input:focus {
  border-color: var(--accent-primary);
  box-shadow: 0 0 0 2px var(--accent-primary-soft);
  outline: none;
}
```

---

## 6. Dark/Light Mode Implementation

### Approach
- Default: dark mode (matches brand identity — Forge is a dark-first brand)
- Toggle: `data-mode="dark"` / `data-mode="light"` on `<html>`
- Persist: `localStorage.setItem('forge-mode', mode)`
- System fallback: `@media (prefers-color-scheme: ...)` when no explicit mode set
- Transition: `transition: background 0.25s ease, color 0.25s ease` on body

### Shared Mode Toggle Script (all apps)

Include this in `<head>` before any CSS loads to prevent flash of wrong mode:

```html
<script>
(function(){
  var m = localStorage.getItem('forge-mode');
  if (!m) m = matchMedia('(prefers-color-scheme:light)').matches ? 'light' : 'dark';
  document.documentElement.setAttribute('data-mode', m);
})();
</script>
```

### Shared Toggle Button

```html
<button id="mode-toggle" aria-label="Toggle dark/light mode">
  <span class="mode-icon"></span>
</button>
<script>
document.getElementById('mode-toggle').addEventListener('click', function() {
  var html = document.documentElement;
  var next = html.getAttribute('data-mode') === 'dark' ? 'light' : 'dark';
  html.setAttribute('data-mode', next);
  localStorage.setItem('forge-mode', next);
});
</script>
```

### Portable CSS Token Block (embed in any app)

This is the minimal CSS that any Forge app needs for full dark/light mode. It's derived from forge-technical's `tokens.css` + `alloy.css` + `mode.css` but self-contained:

```css
/* Forge Alloy — Portable Design Tokens */
:root {
  /* Fonts */
  --font-display: 'halogen', 'Michroma', sans-serif;
  --font-body:    'Space Grotesk', sans-serif;
  --font-slab:    'Roboto Slab', serif;
  --font-mono:    'DM Mono', 'Courier New', monospace;

  /* Default to dark */
  --bg-primary:        #222830;
  --bg-surface:        #2A3545;
  --bg-surface-hover:  #324055;
  --bg-surface-alt:    rgba(42,53,69,0.5);
  --text-primary:      #F5F7FA;
  --text-secondary:    rgba(245,247,250,0.55);
  --text-muted:        rgba(245,247,250,0.50);
  --text-faint:        rgba(245,247,250,0.38);
  --accent-primary:        #64A8E6;
  --accent-primary-soft:   rgba(100,168,230,0.1);
  --accent-primary-border: rgba(100,168,230,0.28);
  --accent-secondary:      #D37A22;
  --accent-secondary-soft: rgba(211,122,34,0.1);
  --accent-secondary-border: rgba(211,122,34,0.28);
  --accent-glow:       #82BDF0;
  --border-color:      rgba(255,255,255,0.07);
  --border-subtle:     rgba(255,255,255,0.05);
  --border-strong:     rgba(255,255,255,0.12);
  --card-shadow:       rgba(0,0,0,0.4);
  --card-shadow-hover: rgba(0,0,0,0.55);
  --gradient-bar:      linear-gradient(90deg, #D37A22 0%, #64A8E6 100%);
}

/* System preference: light */
@media (prefers-color-scheme: light) {
  :root:not([data-mode="dark"]) {
    --bg-primary:        #F4F7FA;
    --bg-surface:        #EBE8E3;
    --bg-surface-hover:  #e0dcd6;
    --bg-surface-alt:    rgba(244,247,250,0.5);
    --text-primary:      #222830;
    --text-secondary:    rgba(34,40,48,0.65);
    --text-muted:        rgba(34,40,48,0.58);
    --text-faint:        rgba(34,40,48,0.45);
    --accent-primary:        #2E6AA8;
    --accent-primary-soft:   rgba(46,106,168,0.1);
    --accent-primary-border: rgba(46,106,168,0.28);
    --accent-secondary:      #8A5210;
    --accent-secondary-soft: rgba(138,82,16,0.1);
    --accent-secondary-border: rgba(138,82,16,0.28);
    --accent-glow:       #4A8EC7;
    --border-color:      rgba(0,0,0,0.08);
    --border-subtle:     rgba(0,0,0,0.05);
    --border-strong:     rgba(0,0,0,0.14);
    --card-shadow:       rgba(0,0,0,0.10);
    --card-shadow-hover: rgba(0,0,0,0.18);
    --gradient-bar:      linear-gradient(90deg, #8A5210 0%, #2E6AA8 100%);
  }
}

/* Explicit dark override */
[data-mode="dark"] {
  --bg-primary:        #222830;
  --bg-surface:        #2A3545;
  --bg-surface-hover:  #324055;
  --bg-surface-alt:    rgba(42,53,69,0.5);
  --text-primary:      #F5F7FA;
  --text-secondary:    rgba(245,247,250,0.55);
  --text-muted:        rgba(245,247,250,0.50);
  --text-faint:        rgba(245,247,250,0.38);
  --accent-primary:        #64A8E6;
  --accent-primary-soft:   rgba(100,168,230,0.1);
  --accent-primary-border: rgba(100,168,230,0.28);
  --accent-secondary:      #D37A22;
  --accent-secondary-soft: rgba(211,122,34,0.1);
  --accent-secondary-border: rgba(211,122,34,0.28);
  --accent-glow:       #82BDF0;
  --border-color:      rgba(255,255,255,0.07);
  --border-subtle:     rgba(255,255,255,0.05);
  --border-strong:     rgba(255,255,255,0.12);
  --card-shadow:       rgba(0,0,0,0.4);
  --card-shadow-hover: rgba(0,0,0,0.55);
  --gradient-bar:      linear-gradient(90deg, #D37A22 0%, #64A8E6 100%);
}

/* Explicit light override */
[data-mode="light"] {
  --bg-primary:        #F4F7FA;
  --bg-surface:        #EBE8E3;
  --bg-surface-hover:  #e0dcd6;
  --bg-surface-alt:    rgba(244,247,250,0.5);
  --text-primary:      #222830;
  --text-secondary:    rgba(34,40,48,0.65);
  --text-muted:        rgba(34,40,48,0.58);
  --text-faint:        rgba(34,40,48,0.45);
  --accent-primary:        #2E6AA8;
  --accent-primary-soft:   rgba(46,106,168,0.1);
  --accent-primary-border: rgba(46,106,168,0.28);
  --accent-secondary:      #8A5210;
  --accent-secondary-soft: rgba(138,82,16,0.1);
  --accent-secondary-border: rgba(138,82,16,0.28);
  --accent-glow:       #4A8EC7;
  --border-color:      rgba(0,0,0,0.08);
  --border-subtle:     rgba(0,0,0,0.05);
  --border-strong:     rgba(0,0,0,0.14);
  --card-shadow:       rgba(0,0,0,0.10);
  --card-shadow-hover: rgba(0,0,0,0.18);
  --gradient-bar:      linear-gradient(90deg, #8A5210 0%, #2E6AA8 100%);
}
```

### For Tailwind Apps (forge-hunt)

Import the portable token block above in `index.css` before tailwind imports. Then map to Tailwind's theme:

```css
@theme {
  --color-forge-bg: var(--bg-primary);
  --color-forge-surface: var(--bg-surface);
  --color-forge-accent: var(--accent-primary);
  --color-forge-copper: var(--accent-secondary);
  --color-forge-text: var(--text-primary);
  --color-forge-muted: var(--text-muted);
  /* ... */
}
```

Replace the current `.dark` class with `data-mode` attribute:
```css
/* Replace: @custom-variant dark (&:is(.dark *)); */
@custom-variant dark (&:is([data-mode="dark"] *));
```

### For Plain CSS Apps (dd-orchestrator)

Embed the portable token block in `<style>` within `base.html` and `base_ui.html`. Replace all hardcoded hex values with `var(--*)` references. The tokens handle mode switching automatically.

**WeasyPrint note:** CSS custom properties may not be fully supported in WeasyPrint. For PDF reports, keep hardcoded hex values but use the canonical dark-mode palette (`#222830`, `#64A8E6`, `#D37A22`). Light mode is not needed for PDF output.

### For Inline-Style React (chat widget)

Create a `theme.ts` constants file:
```typescript
export const THEME = {
  bg: 'var(--bg-primary)',
  surface: 'var(--bg-surface)',
  accent: 'var(--accent-primary)',
  text: 'var(--text-primary)',
  textMuted: 'var(--text-secondary)',
  border: 'var(--border-color)',
  // ...
} as const;
```
Reference `THEME.bg` etc. in inline styles. Since the widget runs inside report pages that already have the token CSS, the `var()` references resolve correctly in both modes.

---

## 7. Icons

**Recommended:** Lucide (already used in forge-hunt). Lightweight, MIT-licensed, consistent stroke style.
- Default size: 16px (size-4)
- Stroke width: 2px (default)
- Color: `currentColor` (inherits from text)

For dd-orchestrator PDF reports: continue using Unicode characters (WeasyPrint compatibility).

---

## 8. Migration Roadmap

### forge-hunt → Forge Alloy (High Priority)

| Change | From | To |
|--------|------|----|
| Font | Geist Variable | Space Grotesk (body), DM Mono (mono) |
| Primary colors | OKLCH grayscale | Forge Alloy tokens |
| Dark mode trigger | `.dark` class | `data-mode="dark"` attribute |
| Component colors | shadcn defaults | Forge semantic tokens |
| Brand accent | none | Glacier Blue primary, Copper secondary |
| Sidebar | `bg-muted/40` | `var(--bg-surface)` |
| Active nav | `bg-primary` (black) | `var(--accent-primary-soft)` + blue text |

**Approach:** Replace OKLCH values in `index.css` with Forge CSS variables. shadcn/ui components will automatically pick up the new tokens since they reference CSS variables. Swap Geist for Space Grotesk + DM Mono imports.

### dd-orchestrator → Forge Alloy (Medium Priority)

| Interface | Change | Approach |
|-----------|--------|----------|
| PDF Reports | Replace hardcoded hex → CSS vars where WeasyPrint supports | Test WeasyPrint CSS variable support; may need to keep hex but use canonical values |
| FastAPI UI | Replace inline `<style>` hex values | Add Forge token imports to `base_ui.html` |
| Chat Widget | Replace inline React styles | Create a shared style constants file using Forge values |
| All | Font stack | Replace `Helvetica Neue, Arial` → Space Grotesk + DM Mono |
| All | `#0d2137` → `#222830` | Close but not canonical — update to Charcoal |
| All | `#1a5276` → `#64A8E6` | Shift from dark navy blue to Glacier Blue |

**Note on `#0d2137`:** dd-orchestrator uses this as its primary dark color. The canonical Forge charcoal is `#222830` (lighter, warmer). The `#0d2137` is a darker, bluer navy. Replace with `var(--bg-primary)` / `var(--bg-surface)` as appropriate.

### forge-deals (New — Unified Shell)

Build from scratch using the canonical Forge design system:
- Import `tokens.css`, `alloy.css`, `mode.css` directly (or package as `@forge/design-tokens`)
- Tailwind + shadcn/ui with Forge token overrides (same approach as migrated forge-hunt)
- App shell hosts sub-apps via routing or micro-frontend approach
- Each sub-app section gets a sidebar entry with appropriate icon

---

## 9. Shared Package Strategy (Future)

When forge-deals is established, consider extracting a shared package:

```
@forge/design-tokens
├── tokens.css          (semantic variables)
├── alloy.css           (palette values)
├── mode.css            (dark/light mapping)
├── tailwind-preset.js  (Tailwind theme preset for React apps)
├── status-colors.css   (semantic status palette)
└── fonts.css           (font imports + fallback declarations)
```

This would be imported by forge-hunt, forge-deals, and dd-orchestrator portal, ensuring single-source token management.

---

## 10. Brand Identity Quick Reference

| Element | Value |
|---------|-------|
| **Brand name** | FORGE (DBA) / Forge Technical (legal) |
| **Tagline** | "Tempered for Growth. Built to Last." |
| **Promise** | "Your name stays on the door. Your culture stays in the building." |
| **Palette** | Forge Alloy (Charcoal + Glacier Blue + Forge Copper) |
| **Primary accent** | Glacier Blue `#64A8E6` (dark) / `#2E6AA8` (light) |
| **Secondary accent** | Forge Copper `#D37A22` (dark) / `#8A5210` (light) |
| **Background** | Charcoal `#222830` (dark) / Canvas `#F4F7FA` (light) |
| **Surface** | `#2A3545` (dark) / Warm Tan `#EBE8E3` (light) |
| **Display font** | Halogen (Typekit `leq1sdk`) |
| **Body font** | Space Grotesk (Google Fonts) |
| **Mono font** | DM Mono (Google Fonts) |
| **Default mode** | Dark |
| **Icons** | Lucide (apps), Unicode (PDF reports) |
| **Logo** | Anvil mark + FORGE wordmark (pending designer execution) |
| **Gradient** | Copper → Blue (`#D37A22` → `#64A8E6`) |
