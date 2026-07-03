# Redesign Rationale — Upskill Example Page

> Lecture 1 challenge: keep every word of the plain `/example/` page, improve the
> design on four dials (typography, colour, grid/spacing, hierarchy), and justify
> every choice. Files: `site/before.html` (original) → `site/index.html` (redesign).

## Design thesis

The page is a *catalogue of every element the portal uses*. Rather than dress it as a
generic marketing landing page, I treated it as an **illuminated manuscript / adventurer's
codex** — a book of specimens. That framing gives the four dials a single source of truth
and a memorable signature.

**Signature — rubrication.** In medieval manuscripts the *rubric* was the red-lettered
heading that told you what kind of passage came next; the word literally means
"red-lettering." The whole brief is about being able to **justify** — the Latin *rubrica*.
So the structural device is a rubricated eyebrow: an oxblood, small-caps label opening every
section, trailed by a single gold hairline rule. One idea carries the theme; everything else
stays quiet.

Deliberately avoided the current AI-design default (warm cream `#F4F1EA` + high-contrast
serif + **terracotta** accent). Parchment + serif is the obvious D&D move, so the accent went
the other way — manuscript **oxblood** and **illuminated gold**, not orange terracotta.

---

## Dial 1 — Typography

| Choice | What | Why |
|---|---|---|
| Pairing | Display: old-style serif (`Iowan Old Style`/`Palatino`/Georgia). Body: system humanist sans. Utility: small-caps serif for eyebrows; monospace for the prompt. | An old-style serif reads "book/codex" and carries the personality; a humanist sans keeps long-form lesson text crisp and legible on screens. Two distinct roles, not one family doing everything. |
| Scale | Fluid modular scale (`--step--1 … --step-4`, ~1.25 minor third) via `clamp()`. | One tunable ratio drives every size; `clamp()` gives responsive headings without breakpoints. Editable at the top of `:root`, as the rubric asks. |
| Measure | Body capped at `--measure: 65ch`. | The lesson block explicitly asks for "around 65 characters per line" — the comfortable reading measure, enforced as a token. |
| Weight/spacing | Headings 600 with slight negative tracking; eyebrows small-caps with `+0.14em`. | Weight and letterspacing (not colour alone) separate levels; small-caps give the rubric its manuscript voice without shouting. |

**Self-contained font tradeoff:** the brief demands one self-contained file, so no Google
Fonts / CDN. Chose **system font stacks** over base64-embedding a display face — zero bytes,
truly offline, no external request. The old-style serif stack still lands the codex feel on
the platforms the room will demo on. (If more flavour is wanted later, a subsetted display
woff2 can be inlined as base64 — still one file.)

## Dial 2 — Colour

A **small token palette, one accent** — exactly what the "Colour" card describes.

- `--bg #ece0c4` aged vellum · `--bg-soft` / `--bg-sunk` for cards & the prompt well.
- `--ink #22190f` iron-gall ink (near-black brown, warmer than `#000`).
- **`--accent #8a1c1c` oxblood** — the one accent, used for actions, links, the rubric, and
  the active pill counters. One accent keeps the hierarchy honest.
- `--gold #9c7420` — a **decorative** second colour for rules, numerals and bullets, used at
  large sizes only (its contrast is fine for ornament, not for body text).
- **Dark mode** (`prefers-color-scheme: dark`) reskins to dungeon obsidian with an *ember*
  accent (`#c6602f`) lifted for contrast on dark — the tokens flip, the layout doesn't.

**Accessibility:** ink-on-parchment is very high contrast; oxblood on parchable meets AA for
its uses; gold is reserved for decoration. Focus rings use a deliberately off-theme teal
(`--focus`) so keyboard focus is unmistakable against the warm palette.

## Dial 3 — Grid & spacing

- Everything is a multiple of an **8px unit** (`--u`) — the "Grid & spacing" card's own rule,
  made literal. Section padding, gaps, and control padding all derive from it.
- **Card grid**: `repeat(auto-fit, minmax(15rem, 1fr))` — reflows 4→2→1 columns with no media
  queries and never drops below a readable card width.
- **Centered codex column** (`--maxw: 66rem`) with the lesson prose narrowed to 65ch inside
  it, so reading width ≠ layout width.
- **Rhythm over boxes**: sections are separated by a single hairline rule and generous vertical
  space rather than heavy containers — the "breathing room" the card asks for.

## Dial 4 — Hierarchy

What the eye hits **first, second, third** (the "Hierarchy" card's test):

1. **Hero** — the largest type on the page (`--step-4`), a short measure, and the only
   load-in motion. "Make this look good." is the thesis, so it dominates.
2. **Rubricated eyebrows** — the oxblood small-caps + gold rule make section starts scannable
   before you read a word; they encode *what kind of block* follows.
3. **Body & specimens** — quiet, consistent, high-measure prose and cards.

The numbered list is the one place **numerals earn their place** (it is a real sequence), so
steps use codex roman numerals (I · II · III) in gold; nowhere else invents 01/02/03
decoration.

---

## Content integrity

Every string is reproduced **verbatim** from the source — headings ("A grid of cards", "A
numbered list", …), card copy, the callout, the exact prompt text, the vote labels/counts,
and the footer. Only structure and styling changed. Structural upgrades that don't touch
wording: real `<label>`s on the form (was placeholder-only), buttons for the interactive
pills, a skip link, and landmark elements.

## Verification

- **Self-contained:** grep shows no `http(s)://` / CDN references; renders offline.
- **Valid:** `npx html-validate site/index.html` → 0 errors.
- **Accessible:** `web-ci` runs pa11y (WCAG2AA, htmlcs + axe) on every `site/**` change.
- **Responsive / themed:** verified at 360 / 768 / 1200px, light and dark.

---

## Hosting — chosen: Netlify (Git auto-deploy)

**Why Netlify:**

- **Zero-token CD.** Connecting the GitHub repo means every push to `main` auto-builds and
  deploys. No deploy token is stored in the repo or CI — which fits the project's
  "browser-OAuth, no secrets on disk" guardrail. (This is why Git integration was chosen over
  an Actions + Netlify-CLI pipeline, which would need a `NETLIFY_AUTH_TOKEN` secret.)
- **Deploy previews** on every PR — a live URL per pull request, ideal for the before→after
  pitch.
- **`publish = "site"`** scopes the upload to the site directory, so repo-root files
  (`.claude/`, `scripts/`, `.env`) are never served — a security property, enforced in config.
- **Header control** via `netlify.toml` (CSP + friends) without a server.
- Native form handling if the demo form is ever wired up.

## Alternative hosting researched — GitHub Pages vs Cloudflare Pages

The brief asks to research one alternative and be ready to say why. Two realistic ones:

**GitHub Pages** — *the natural alternative here.*
- **Pros:** the repo already lives on GitHub, so it's zero extra service and zero extra
  account; static-perfect; free; deploys via a simple Actions workflow or branch setting.
- **Cons:** no per-PR preview URLs (previews need extra tooling); **custom response headers
  aren't supported** (no CSP/security headers — a real downside for this project); soft CDN
  caching can lag redeploys.
- **When I'd use it:** a repo already on GitHub where I want the simplest possible free host
  and don't need security headers or PR previews — e.g. project docs or a portfolio page.

**Cloudflare Pages** — *the performance alternative.*
- **Pros:** deploys to a large global edge network (fast TTFB worldwide); Git integration with
  PR previews like Netlify; supports custom headers via a `_headers` file; generous free tier;
  Functions if it later needs edge logic.
- **Cons:** another account/service to manage; dashboard and build config are less beginner-
  friendly than Netlify's; some features assume you're in the Cloudflare ecosystem (DNS etc.).
- **When I'd use it:** a globally-distributed audience where edge latency matters, or when I'm
  already using Cloudflare for DNS/CDN.

**Verdict:** Netlify for *this* task — it's the best balance of one-click Git CD, per-PR
preview URLs for the pitch, security-header support, and no committed secrets. GitHub Pages is
the fallback I'd reach for if I wanted to drop the extra service entirely (accepting no
security headers); Cloudflare Pages if global edge performance were the priority.

---

## Before → after (pitch)

- **Before:** `site/before.html` — the plain page (single accent blue, system sans
  everywhere, flat cards, uppercase micro-eyebrow).
- **After:** `site/index.html` — manuscript palette, serif display + tuned scale, rubricated
  section starts, 8px rhythm, dark mode, a11y upgrades.
- **Screenshots:** capture both from the Netlify deploy preview (light + dark) and drop them
  in `docs/upskill/` for the 2-minute walk-through. (Local capture was unavailable — the dev
  container's firewall blocks the headless-Chromium download.)
