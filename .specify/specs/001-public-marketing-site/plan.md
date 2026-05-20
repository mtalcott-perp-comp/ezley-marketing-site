# Plan — Public Marketing Site for Ezley.com

**Feature Branch**: `001-public-marketing-site`
**Phase**: `plan`
**Created**: 2026-05-19
**Spec**: [`./spec.md`](./spec.md) (post-clarify, 9 resolutions baked in)
**Status**: Plan-phase deliverable. Implementation follows in `tasks.md` (next phase).

## Stack decision

| Layer | Choice | Why |
|---|---|---|
| Site generator | **Jekyll 4.x** | Static-by-default (NFR-MKT-004 no-JS baseline is natural). Strong GitHub Pages integration. Mature, low-surprise. Founder preference. |
| Theme | **Minimal Mistakes** (`mmistakes/minimal-mistakes`), MIT-licensed, remote-theme | Marketing-appropriate (hero, splash, feature rows, comparison sections). Accessibility-conscious out of the box. Most-active maintenance of any free marketing-friendly Jekyll theme. |
| Hosting | **GitHub Pages** with custom domain `ezley.com` | Free. Native to GitHub. Built-in SSL via Let's Encrypt. Founder preference. Limitations documented below. |
| CI / build | **GitHub Actions** | Free for public repos. Runs `bundle install`, fetches `/api/config/public` snapshot, runs `jekyll build`, deploys to `gh-pages` branch. Replaces GitHub's legacy in-house Jekyll build (which limits plugins). |
| Domain | `ezley.com` via `CNAME` file at repo root | Per [D-023](../../../../ezley-docs/09-decisions.md). DNS A records point to GitHub Pages' four IPs (185.199.108.153, .109.153, .110.153, .111.153). |

**Deliberately not picked**:
- Astro / Next.js / Eleventy — none chosen, despite being modern SSGs, because the founder explicitly prefers Jekyll + GitHub Pages.
- Cloudflare Pages / Vercel / Azure Static Web Apps — none chosen, despite native redirect support, because GitHub Pages is the simplest "free + easy to deploy" path and the redirect deferral (below) makes it acceptable at M0.

## Architecture

```
        ┌────────────────────────────────────────────────────────────┐
        │                       Source of truth                      │
        │  ezley-docs/ (vision, principles, glossary, decisions)     │
        │  ezley-market-net-api/ (Pricing:* config, MUI tokens via   │
        │   token-extraction from ezley-market-ui-react)             │
        └─────────────────────┬──────────────────────────────────────┘
                              │
                              │  pulled at build time
                              ▼
        ┌────────────────────────────────────────────────────────────┐
        │       ezley-marketing-site (this repo, GitHub)             │
        │                                                            │
        │  main branch ──┐                                           │
        │                │  push                                     │
        │                ▼                                           │
        │  ┌─────────────────────────────────────────┐               │
        │  │  GitHub Actions workflow (.github/      │               │
        │  │   workflows/deploy.yml)                 │               │
        │  │                                         │               │
        │  │  1. checkout                            │               │
        │  │  2. setup-ruby                          │               │
        │  │  3. bundle install                      │               │
        │  │  4. curl api.ezley.com/api/config/      │               │
        │  │     public → _data/config.yml (with     │               │
        │  │     last-known-good fallback)           │               │
        │  │  5. jekyll build                        │               │
        │  │  6. deploy _site → gh-pages branch      │               │
        │  └─────────────┬───────────────────────────┘               │
        │                │                                           │
        │                ▼                                           │
        │  gh-pages branch (served at ezley.com)                     │
        └────────────────────────────────────────────────────────────┘
                              │
                              │  scheduled cron (daily) + on-push triggers
                              ▼
                ┌─────────────────────────────┐
                │   ezley.com (served by      │
                │   GitHub Pages CDN)         │
                └─────────────────────────────┘
```

## Build process

The GitHub Actions workflow does what plain `gh-pages` mode cannot — it runs the full Jekyll toolchain with arbitrary gems, AND it fetches the marketplace config at build time so the Pricing page numbers stay in sync with [`Pricing:*`](../../../../ezley-docs/08-glossary.md) without inlining the values (per P-09 and NFR-MKT-009).

**Triggers**:
- `push` to `main` — every commit rebuilds and redeploys
- `schedule: cron: "0 6 * * *"` — daily 06:00 UTC rebuild so config-snapshot freshness honors NFR-MKT-008's 24h max staleness
- `workflow_dispatch` — manual trigger from the GitHub UI

**Steps**:

1. **Checkout** `main`.
2. **Setup Ruby 3.2.x**, cache gems via `actions/setup-ruby`.
3. **`bundle install`** — installs Jekyll, Minimal Mistakes via `jekyll-remote-theme`, plus plugins (`jekyll-sitemap`, `jekyll-seo-tag`, `jekyll-feed`).
4. **Fetch public config** — a small shell script (`scripts/fetch-config.sh`) calls `curl -sSf https://api.ezley.com/api/config/public > _data/config.fresh.yml`. On HTTP success, copies the fresh file to `_data/config.yml`. On HTTP failure (timeout, 5xx, network), keeps the existing `_data/config.yml` as the last-known-good snapshot (committed to the repo). Exposes the staleness state in a build log line; never fails the deploy on config-fetch failure alone (per NFR-MKT-007's 99.9% uptime target — config-fetch failure should not take down the marketing site).
5. **`bundle exec jekyll build`** — outputs to `_site/`.
6. **Deploy** to `gh-pages` branch via `actions/deploy-pages` or `peaceiris/actions-gh-pages@v3`.
7. **Post-deploy verification** — a smoke-test step curls `https://ezley.com/` and asserts the build artifact actually serves (catches the case where the gh-pages branch updated but Pages itself hasn't refreshed).

## Configuration data flow

Three categories of values appear on the site. Each has a clear source.

| What | Source | Where it lives in the Jekyll site | Refreshed |
|---|---|---|---|
| Pricing rate per Rail (3.0/3.5/5.5%) | [`api.ezley.com/api/config/public`](../../../../ezley-market-net-api) → `pricing.*` | `_data/config.yml` (fetched at build) | Every build (push or daily cron) |
| Auto-release window default (72 h) | Same endpoint → `payments.autoReleaseHoursDefault` | `_data/config.yml` | Every build |
| Wire-only-min-amount ($25K — described, not numerically named per FR-MKT-054) | Same endpoint → `payments.wireOnlyMinAmount` | `_data/config.yml` | Every build |
| Personas / principles / D-XXX text | `ezley-docs/*.md` (manual quoting per spec) | `_data/content.yml` (manually maintained snippet store) | Manually, on docs change |
| Marketing copy (hero headlines, CTA labels, competitor rows, sub-brand chip text) | This repo, in i18n catalog modules (NFR-MKT-011) | `_data/copy/en-US.yml` | Manually, with PR review |
| Brand tokens (color hex codes, fonts, wordmark SVG) | Manually extracted from `ezley-market-ui-react` MUI theme (NFR-MKT-013) | `_sass/ezley-tokens.scss` + `assets/images/wordmark.svg` | Manually, on marketplace UI token change |

The Jekyll Liquid templating reads from `_data/*.yml` so a fee number in any page renders as `{{ site.data.config.pricing.wireTakeRatePct }}` rather than `3.0` — that's the mechanism that honors P-09 and NFR-MKT-009 (CI lint enforces no bare-literal fee numbers in `.md` / `.html` / `.liquid` files).

## Theme customization plan

Minimal Mistakes ships with a configurable skin system (`skin: "default" | "air" | "aqua" | "contrast" | "dark" | "dirt" | "mint" | "neon" | "plum" | "sunrise"`). Rather than use a prebuilt skin, we ship a custom skin (`_sass/skins/_ezley.scss`) that maps Ezley's brand tokens onto Minimal Mistakes' SCSS variables:

| Minimal Mistakes variable | Ezley source | Notes |
|---|---|---|
| `$primary-color` | extracted MUI 5 `theme.palette.primary.main` | Confirm exact hex during scaffold; Ezley primary should land here. |
| `$secondary-color` | MUI 5 `theme.palette.secondary.main` | Used for accent links, callouts. |
| `$text-color` | MUI 5 `theme.palette.text.primary` | Body text. |
| `$muted-text-color` | MUI 5 `theme.palette.text.secondary` | Captions, fine print, sub-brand chip background. |
| `$background-color` | white / off-white | Mobile-first means high contrast. |
| `$base-font-family` | MUI 5 type baseline (Roboto or Inter — confirm during scaffold) | Read-flow body text. |
| `$header-font-family` | Marketing-only display font (overlay layer per NFR-MKT-013) | Hero headlines, section heads. |

The custom skin is the only theme override that's truly necessary. Layouts (the `.html` files in `_layouts/`) are not overridden globally — only specific pages that need marketing-specific structure (hero block, competitor comparison grid, rate-card row) override their layout.

The marketing-only overlay (NFR-MKT-013): a display font sourced from Google Fonts (no PII, served from `fonts.googleapis.com` — note no analytics impact); hero photography stored in `assets/images/hero/` (sourced separately, see Hero photography below); marketing-specific Liquid includes (`_includes/competitor-row.html`, `_includes/rate-card.html`, `_includes/subbrand-chip.html`).

## Hero photography

The spec doesn't specify a photography source. At M0, options:

1. **Custom shot at first concierge sessions** — visit early sellers, take real photos of their equipment and the handover. Authentic. Slow.
2. **Licensed stock from Unsplash / Pexels** — free, instant, but feels stock. Use sparingly.
3. **Placeholder graphic** — a clean illustrated hero that doesn't pretend to be a photo. Honest.

**Recommendation**: option 3 at M0 (illustrated hero, no fake authenticity); transition to option 1 as concierge volume produces real photos. Document the photography source per asset in `assets/images/CREDITS.md`.

## Routing and page structure

Jekyll's URL structure aligns naturally with the spec's route list. Per Minimal Mistakes conventions, page sources live in `_pages/` with explicit `permalink:` front-matter:

| Spec route | Source file | Layout | Notes |
|---|---|---|---|
| `/` | `index.html` | `splash` | Home page (US1, FR-MKT-010..019) |
| `/tractors` | `_pages/tractors.md` | `splash` | W1 front-door (US2, FR-MKT-020..026) |
| `/cars` | `_pages/cars.md` | `single` (interstitial) | Coming-soon (FR-MKT-023) |
| `/cameras` | `_pages/cameras.md` | `single` (interstitial) | Coming-soon (FR-MKT-023) |
| `/furniture` | `_pages/furniture.md` | `single` (interstitial) | Coming-soon (FR-MKT-023) |
| `/how-it-works` | `_pages/how-it-works.md` | `single` | US3, FR-MKT-030..035 |
| `/pricing` | `_pages/pricing.md` | `single` | US4, FR-MKT-040..048 |
| `/trust` | `_pages/trust.md` | `single` | FR-MKT-050..055 |
| `/agents` | `_pages/agents.md` | `single` | US5, FR-MKT-060..062 |
| `/llms.txt` | `llms.txt` | (raw file) | FR-MKT-070 |
| `/mcp.json` | `mcp.json` | (raw file) | FR-MKT-071 |
| `/sitemap.xml` | (auto-generated by `jekyll-sitemap`) | n/a | FR-MKT-072 |
| `/robots.txt` | `robots.txt` | (raw file) | FR-MKT-073 |

## Implementation sequence (input to `tasks.md`)

Drives the priority of `tasks.md` work — P1 user stories first, P2 after.

| Phase | Outcome | Spec coverage |
|---|---|---|
| **Phase 0 — Foundation** | Repo scaffolds, deploys to GitHub Pages, custom domain resolves, blank Minimal Mistakes site live at `ezley.com` | NFR-MKT-007 (uptime), CNAME |
| **Phase 1 — Home page** | US1 lands. Hero, competitor rows, escrow trust anchor, footer | FR-MKT-001..005, 010..019; NFR-MKT-001..004, 010..013 |
| **Phase 2 — /tractors** | US2 lands. Category-tailored hero, sub-brand chip, Ezley-owned trust signals | FR-MKT-020..026 |
| **Phase 3 — /how-it-works + /pricing** | US3 + US4 land. Glossary-disciplined flow descriptions; one bundled fee per Rail from `_data/config.yml` | FR-MKT-030..048; pricing contract test |
| **Phase 4 — /trust + /agents + agent-discovery files** | US5 lands. /trust depth page; /agents page; llms.txt, mcp.json, robots.txt, sitemap.xml | FR-MKT-050..055, 060..074; NFR-MKT-005, 006 |
| **Phase 5 — Coming-soon interstitials** | /cars, /cameras, /furniture | FR-MKT-023 |
| **Phase 6 — CI gates** | axe-core, Lighthouse, P-14 grep, glossary-discipline grep in GitHub Actions | NFR-MKT-001, 002, 010; test plan |
| **Phase 7 — Concierge offline / no-JS / mobile pass** | US6, US7, US8 cross-cutting acceptance | NFR-MKT-002, 003, 004, 008 |

Each phase delivers a complete user story (Phase 1 = US1, etc.), so the site is shippable at any phase boundary — the founder can decide where to stop or pause.

## Known limitations documented in this plan

### L1 — D-023 301 redirect from `ezley.com/l/*` to `app.ezley.com/l/*` is deferred

GitHub Pages does not support server-side redirects. The marketing site at M0 does not implement the D-023 301. Consequences:

- Any external link, search-engine cache, or agent-graph reference still using the legacy `ezley.com/l/<ListingId>/...` form will hit a 404 at the marketing site rather than the intended app.
- At M0 this protects ≈ zero indexed URLs (D-023 was decided 2026-05-19; the marketplace had not yet published any `ezley.com/l/*` URLs to Google).
- **Mitigation**: marketplace UI sets `<link rel="canonical" href="https://app.ezley.com/l/<ListingId>/...">` on every listing detail page, anchoring future SEO authority to the `app.` host directly.
- **Revisit trigger**: Google Search Console shows ≥ 100 impressions/month for `ezley.com/l/*` URLs (i.e., the legacy form has indexed enough to be worth protecting). At that point, either (a) put Cloudflare in front of GitHub Pages as a Page Rule that handles the 301, or (b) migrate hosts to Cloudflare Pages or Azure Static Web Apps (both support native 301).
- A note to this effect will be appended to D-023 Consequences in the same PR that lands this plan.

### L2 — Brand-asset parity becomes value reuse, not package reuse

NFR-MKT-013 calls for parity with `ezley-market-ui-react`'s MUI 5 tokens, wordmark, and type-scale baseline. Jekyll's SCSS pipeline cannot consume MUI's React-based theme package directly. The marketing site instead extracts:

- Color hex codes from MUI's `theme.palette.*` → encoded in `_sass/ezley-tokens.scss`.
- Wordmark SVG from `ezley-market-ui-react/Solution1/public/wordmark.svg` (or wherever it lives) → copied to `assets/images/wordmark.svg`.
- Type-scale baseline (font family, base size, scale ratio) from MUI's `theme.typography.*` → encoded in `_sass/ezley-tokens.scss`.

**Maintenance contract**: When `ezley-market-ui-react` changes any of these values, a PR to this repo updates the SCSS. CI grep at the marketplace UI level catches accidental token drift (a future task — not in this spec).

**Revisit trigger**: visible brand drift between `ezley.com` and `app.ezley.com` reported by ≥ 2 visitors (or noticed by founder during demo). At that point, escalate to a shared design-tokens npm package consumed by both repos (Style Dictionary or similar).

## Test gates (CI)

| Gate | Tool | When |
|---|---|---|
| Pricing contract — rendered HTML matches `_data/config.yml` rate keys | Custom Ruby script + Jekyll's `--dry-run` | Every PR |
| P-14 grep — zero matches for `Stripe / Connect / concierge fee / reserve / breakdown` in seller-facing copy | `grep -r -i ... _site/` | Every PR |
| Glossary discipline — forbidden synonyms zero-match | `grep -r -i ... _site/` for `\border\b`, `\bitem\b`, `\bhold funds\b`, `\bcharge\b`, `\bvendor\b`, `\bcustomer\b`, `\bplatform fee\b` | Every PR |
| Accessibility (axe-core) — zero critical/serious violations on every route | `@axe-core/cli` against `_site/` post-build | Every PR |
| Lighthouse — LCP ≤ 2.5s, CLS ≤ 0.1, TBT ≤ 200ms on Slow 4G + mid-tier mobile | `@lhci/cli` (Lighthouse CI) | Every PR |
| Visual regression — 320px and 1280px snapshots reviewed manually | `playwright` or `percy` (TBD) | Every PR; reports surfaced, not auto-blocking |
| Sitemap parses | xmllint | Every PR |
| Agent-UA crawl — known agent UAs (`OpenAI-GPT/1.0`, `Anthropic-Claude/1.0`, `PerplexityBot/1.0`) get 200 + structured data | small Ruby script | Every PR + nightly |

## Quickstart

See [`./quickstart.md`](./quickstart.md) for local-dev and deploy instructions.

## Open items for `tasks.md` phase

- Exact color hex codes for primary / secondary / text — pending extraction from `ezley-market-ui-react` MUI theme.
- Display headline font choice — pending design preference. Working hypothesis: a clean geometric sans-serif from Google Fonts (e.g., Manrope, DM Sans, Sora). Not Inter (already the body font candidate) to give the hero visual weight.
- The marketplace API endpoint URLs for MCP / ACP / UCP / AP2 referenced on `/agents` page — these endpoints have not yet shipped in `ezley-market-net-api`. At M0, the `/agents` page may say "agent endpoints documented at /agents/docs (coming soon, ETA M3)" rather than linking to non-existent URLs.
- Hero photography source decision (illustrated vs stock vs custom) — see Hero photography section above.

These items do not block plan approval — they are flagged for `tasks.md` to schedule appropriately.
