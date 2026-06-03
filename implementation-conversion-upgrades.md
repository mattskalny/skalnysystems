# Implementation Plan: Conversion Upgrades

**Status:** Implemented in `index.html`  
**Primary file:** `index.html` (inline CSS + inline JS; Vite scaffold in `main.js` / `counter.js` is unused for the live page)  
**Goal:** Increase engagement and qualified leads via hero interactivity, social proof, lightweight qualification, and a stable layout during animated copy.

---

## Current baseline

| Area | Today |
|------|--------|
| Hero | Static `<h1>`, subheadline, single CTA — no calculator, no typewriter |
| Below hero | Services section (`#services`) |
| Social proof | None (no logo band) |
| Case studies | Full grid of 3 cards (problem + quote + result) lower on page |
| Pre-CTA | Final CTA (`#contact`) only — no health-check quiz |
| JS patterns | Scroll fade-in, mobile menu, stat counters (inline in `index.html`) |

---

## Recommended page order (after implementation)

1. Nav  
2. **Hero** — typewriter headline (fixed-height slot) → subheadline → **automation calculator** → hero CTA  
3. **Trusted by** logo band  
4. Services → Who We Help → How It Works → Results (stats)  
5. **Mini case study** (single featured story: problem / solution / outcome)  
6. Existing case studies grid (optional: trim or cross-link later — out of scope unless copy overlaps)  
7. **Automation health check** (3 questions)  
8. Final CTA  
9. Footer  

---

## Feature specs & checklists

### 1. Automation calculator widget (hero)

**Purpose:** Let visitors quantify time/cost pain immediately; primary conversion hook in the hero.

**Layout rules (required)**

- Calculator stays **horizontally centered** (`margin: 0 auto`, max-width, flex/grid center on parent).
- Calculator has a **fixed min-width / max-width** so it does not shrink when headline text changes.
- **Do not** place calculator inline beside the headline on desktop — stack vertically.
- Add explicit **vertical spacing** (margin/padding) between headline block and calculator so they never overlap or compete for width.

**Suggested calculator inputs (confirm copy with stakeholder)**

| Input | Example |
|-------|---------|
| Hours per week on manual/repetitive work | slider or number |
| Number of people doing that work | number |
| Average hourly cost (optional) | number or preset |

**Outputs (example)**

- Estimated hours/month lost  
- Estimated annual cost (if hourly rate provided)  
- One-line “automation potential” message  

**Implementation checklist**

- [ ] Add `.hero-calculator` wrapper below subheadline (or below headline block if subheadline moves).
- [ ] Style card: dark surface, border, padding consistent with `.service-card` / `.stat-card`.
- [ ] Wire input `input` / `change` handlers; debounce not required for simple math.
- [ ] Format numbers (commas, rounding) for readability.
- [ ] Ensure calculator height is **stable** (reserve space for result text to avoid layout jump).
- [ ] Mobile: full width within container, touch-friendly controls (min 44px tap targets).
- [ ] Accessibility: `<label>` for each control, `aria-live="polite"` on results region.
- [ ] No dependency on Vite unless we deliberately migrate hero JS to a module (default: extend inline script block in `index.html` to match existing patterns).

**Acceptance criteria**

- Resizing window or cycling typewriter phrases does **not** shift calculator horizontally or compress it below usable width.
- Calculator remains centered from 320px through 1440px+ viewports.

---

### 2. Trusted by logos band (below hero)

**Purpose:** Immediate credibility between hero and services.

**Design**

- Full-width band, subtle background (`rgba` border top/bottom or slightly elevated surface).
- Label: “Trusted by” or “Teams we’ve helped” (single line, muted).
- **5–8 logos** in a row; grayscale/muted default, optional hover brighten.
- Use SVG or optimized PNG; consistent visual height (e.g. 28–36px).

**Implementation checklist**

- [ ] New `<section class="trusted-by">` immediately after `.hero`, before `#services`.
- [ ] CSS: flex or grid, `align-items: center`, `justify-content: center`, `gap`, wrap on small screens.
- [ ] `object-fit: contain` on images; max-height enforced.
- [ ] Placeholder assets in repo or documented paths (e.g. `assets/logos/`) — obtain real logos before launch.
- [ ] Alt text per logo (company name).
- [ ] Optional: slow auto-scroll marquee on mobile only if row overflows (keep static row if simpler).

**Acceptance criteria**

- Band visible without scrolling on typical laptop viewport (hero + band in first screen or band starts at fold — acceptable either way if hero is tall).
- Logos remain legible and aligned on mobile (2 rows or horizontal scroll, not broken layout).

---

### 3. Mini case study section (problem / solution / outcome)

**Purpose:** One scannable proof story mid-funnel — faster than the existing 3-card grid.

**Content structure (single featured client)**

| Block | Content |
|-------|---------|
| Problem | 1–2 sentences — pain before automation |
| Solution | 1–2 sentences — what was built |
| Outcome | Metric-forward line (e.g. “74% faster intake”) |

**Implementation checklist**

- [ ] New section e.g. `#mini-case-study` — place after stats (`#results`) or before full case studies (recommended: **before** `.case-studies` to warm reader before depth).
- [ ] Three columns on desktop (`problem` | `solution` | `outcome`); stack on mobile with clear headings.
- [ ] Reuse design tokens: `.section-title`, card borders, blue accent for outcome stat.
- [ ] Optional industry tag (e.g. “Tax office”) — reuse `.case-study-industry` styling if helpful.
- [ ] Copy: pull from strongest existing card or net-new; avoid duplicating all three grid stories verbatim.
- [ ] Fade-in via existing `.fade-in-up` + `IntersectionObserver` pattern.

**Acceptance criteria**

- Reader can scan problem → solution → outcome in under 10 seconds.
- Section visually distinct from hero calculator and from full case study cards.

---

### 4. Three-question automation health check (before final CTA)

**Purpose:** Lightweight qualification + engagement; primes visitor for strategy call.

**UX flow**

1. Section intro + progress (1 of 3, 2 of 3, 3 of 3).  
2. One question visible at a time (or all three visible with step highlight — prefer **one at a time** for focus).  
3. After Q3: show **result tier** (e.g. “High automation potential” / “Moderate” / “Getting started”) based on score.  
4. CTA button: “Book a Free Strategy Call” → `#contact` (same as nav).

**Suggested questions (draft — replace with final copy)**

| # | Question | Scoring idea |
|---|----------|----------------|
| 1 | How much repetitive manual work happens weekly? | Low / medium / high → 1–3 pts |
| 2 | Are your core tools connected (CRM, email, sheets, etc.)? | Rarely / partly / mostly → 1–3 pts |
| 3 | Has your team tried automation before? | Never / tried & stalled / some in place → 1–3 pts |

**Implementation checklist**

- [ ] New section e.g. `#automation-health-check` immediately **above** `.final-cta`.
- [ ] State machine in JS: `currentStep`, `answers[]`, `score`.
- [ ] Buttons or radio cards per answer; “Next” disabled until selection.
- [ ] Result panel hidden until complete; include 2–3 bullet recommendations tied to tier.
- [ ] Optional: pass score tier as query param on CTA URL for analytics (only if booking link supports it).
- [ ] Keyboard: focus management on step change; Enter to advance.
- [ ] Do not block final CTA for users who skip — optional “Skip to booking” link.

**Acceptance criteria**

- All three questions completable without page reload.
- Final CTA in this section and in `.final-cta` both reach `#contact`.
- No layout shift that overlaps footer on mobile when result panel appears.

---

### 5. Typewriter dynamic headline effect

**Purpose:** Rotate industry/pain-specific words while keeping layout stable for the calculator below.

**Copy rules (required)**

- **Static prefix + animated suffix** (or suffix only in second line) — e.g. “Automate your **intake**” / “Automate your **scheduling**”.
- Animated words must be **short** (target ≤ 12 characters; hard max 15) so lines never wrap to a **third** line.
- Curate word list: `intake`, `billing`, `follow-ups`, `scheduling`, `reports`, `onboarding` — audit at `clamp()` min font size on 320px width.
- Avoid long phrases in the rotating slot.

**Layout rules (required)**

- Wrap headline in `.hero-headline-slot` with **fixed height** (e.g. `min-height` + `height` in `em` or `px` tuned per breakpoint) — accommodates exactly **two lines** at largest rotating word.
- `overflow: hidden` on slot optional; prefer height reservation over clipping mid-glyph.
- **Line break / spacing** between headline block and calculator: dedicated margin (e.g. `margin-bottom` on slot + `margin-top` on calculator) — never zero-gap stacking.

**Behavior**

- Type: character-by-character; delete; pause; next word.
- Respect `prefers-reduced-motion`: show first word static, no animation.
- Cursor: blinking `|` or underscore in accent color; hide when reduced motion.

**Implementation checklist**

- [ ] Replace static `<h1>` text with structure: e.g. `<span class="hero-headline-static">…</span><span class="hero-headline-dynamic" aria-live="polite"></span>`.
- [ ] JS module or IIFE in `index.html`: word array, type/delete timers, `requestAnimationFrame` or `setInterval` with cleanup on `visibilitychange` optional.
- [ ] Measure tallest word at load + on `resize` (optional) to set `--hero-headline-min-height` once.
- [ ] Test all words at 320px, 768px, 1440px — **max 2 lines** verified.
- [ ] Coordinate with calculator placement (feature 1): implement typewriter + fixed slot **before** or together with calculator so QA is one pass.

**Acceptance criteria**

- No word in rotation causes a third line at any standard breakpoint.
- Calculator position does not move vertically when words change (headline slot height constant).
- Screen readers get sensible text (avoid spelling letter-by-letter in aria; update `aria-live` on word complete).

---

## Cross-cutting technical notes

| Topic | Recommendation |
|-------|----------------|
| File organization | Phase 1: all in `index.html`. Phase 2 (optional): extract `hero-typewriter.js`, `hero-calculator.js`, `health-check.js` and load via `<script type="module">` if file grows past ~200 lines. |
| CSS | Add blocks after existing `HERO SECTION` comment; use BEM-like prefixes (`hero-`, `trusted-by-`, `health-check-`). |
| Performance | Logo SVGs; no heavy libs for typewriter. |
| Analytics | Hook `data-event` attributes on calculator CTA, health-check completion, final CTA for future GTM. |
| Content | Block launch until real logos, legal approval on case study claims, and final health-check questions. |

---

## Suggested implementation order

Dependencies: **#5 (typewriter + fixed headline slot)** and **#1 (calculator)** should be built and tested **together** in the hero. Everything else can proceed in parallel after hero is stable.

| Order | Item | Rationale |
|-------|------|-----------|
| 1 | Typewriter + fixed-height headline slot | Establishes stable hero layout |
| 2 | Hero automation calculator | Depends on stable headline slot |
| 3 | Trusted by band | Independent; quick win below hero |
| 4 | Mini case study | Independent content section |
| 5 | Health check | Independent; sits above final CTA |
| 6 | QA pass | Full responsive + reduced-motion + a11y |

---

## Master checklist (copy for tracking)

### Hero: typewriter + layout stability (#5)

- [ ] Fixed-height `.hero-headline-slot` (max 2 lines)
- [ ] Short-word-only rotation list vetted at 320px
- [ ] `prefers-reduced-motion` fallback
- [ ] Spacing between headline block and calculator

### Hero: automation calculator (#1)

- [ ] Centered calculator card; fixed width constraints
- [ ] Inputs + live results; stable result area height
- [ ] Mobile + a11y labels / `aria-live`

### Trusted by (#2)

- [ ] Section below hero
- [ ] Logo assets + alt text
- [ ] Responsive row/wrap

### Mini case study (#3)

- [ ] Problem / solution / outcome blocks
- [ ] Placement before full case studies grid
- [ ] Fade-in animation

### Health check (#4)

- [ ] 3 questions with scoring
- [ ] Result tier + CTA
- [ ] Section above final CTA
- [ ] Skip option + keyboard flow

### Integration QA

- [ ] Hero: typewriter cycle does not move calculator
- [ ] No third-line headline wrap at any breakpoint
- [ ] All primary CTAs link to `#contact`
- [ ] Cross-browser smoke (Safari, Chrome, mobile Safari)
- [ ] Lighthouse: no major CLS regression in hero

---

## Out of scope (this plan)

- Wiring Vite/React migration  
- Booking calendar embed (CTA remains link placeholder `#` until URL provided)  
- CMS or backend for quiz results  
- Replacing or removing the existing 3-card case studies section  

---

## Open questions (resolve before coding)

1. Final calculator formula and whether to show dollar amounts or hours only.  
2. Approved client logos and permissions for “Trusted by”.  
3. Which single client story powers the mini case study.  
4. Final three health-check questions and score → message mapping.  
5. Exact typewriter static line + word list (legal/brand review).  

---

*Document: `implementation-conversion-upgrades.md` — planning only.*
