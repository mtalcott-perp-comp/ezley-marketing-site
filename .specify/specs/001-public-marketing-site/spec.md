# Feature Specification: Public Marketing Site for Ezley.com

**Feature Branch**: `001-public-marketing-site`
**Feature ID**: F-MKT-001
**Created**: 2026-05-19
**Status**: Draft — `specify` phase only; `clarify` / `plan` / `tasks` / `analyze` / `implement` deferred
**Input**: User description: see [`.specify/inputs/001-public-marketing-site.input.md`](../../inputs/001-public-marketing-site.input.md) (the body between `BEGIN PROMPT` / `END PROMPT`)
**Source pinning**: `ezley-docs` `main` as of 2026-05-19 — vision, personas, principles, glossary, decisions, roadmap, target-state

## Canonical location

This file (`.specify/specs/001-public-marketing-site/spec.md`) is the canonical spec for the Ezley public marketing site. It is owned by the `ezley-marketing-site` repo and has no companion API spec — the marketing site only CONSUMES the marketplace API's existing public-config endpoint (`GET /api/config/public`, shipped in the marketplace's `004-pricing-and-gating` API spec) and does not own any server-side write surface.

## Scope by repository

### `ezley-marketing-site` (this repo) — owns

Every Functional Requirement in this document. The marketing site is the sole owner of `ezley.com` root + the category front-door routes.

### `ezley-market-net-api` — consumed only

- `GET /api/config/public` for the bundled-fee rates (`Pricing:CardTakeRatePct`, `Pricing:AchTakeRatePct`, `Pricing:WireTakeRatePct`) and any other M0 threshold values displayed on the Pricing or Trust & Safety pages.

### `ezley-market-ui-react` — referenced only

CTAs deep-link into the marketplace UI at `app.ezley.com` (per [D-023](../../../../ezley-docs/09-decisions.md)) carrying UTM parameters. The marketing site does NOT render auth, signup, listing creation, browse, or checkout — those live in the marketplace UI repo per [D-003](../../../../ezley-docs/09-decisions.md) and [D-010](../../../../ezley-docs/09-decisions.md).

## Alignment with project rules

This spec follows the SpecKit handoff workflow defined in [`ezley-docs/10-speckit-handoff.md`](../../../../ezley-docs/10-speckit-handoff.md), uses the canonical vocabulary from [`ezley-docs/08-glossary.md`](../../../../ezley-docs/08-glossary.md), serves the personas in [`ezley-docs/02-personas-and-jobs.md`](../../../../ezley-docs/02-personas-and-jobs.md), and honors the principles in [`ezley-docs/03-product-principles.md`](../../../../ezley-docs/03-product-principles.md). Tech-stack selection is deferred to the `plan` phase per [D-014](../../../../ezley-docs/09-decisions.md).

## Strategic intent (founder summary)

Ezley.com is the public front door. It has two jobs and only two jobs at M0:

1. **Tell a first-time visitor what Ezley is and why it is different** — within 30 seconds, on a phone, in farm country.
2. **Route the visitor into the marketplace app with the right intent** — "Start selling" or "Browse listings" or "Notify me when [category] launches".

It is not the marketplace app. It does not host listings, auth, checkout, escrow, or dashboards. It is the brand surface, the trust pitch, the competitor contrast, and the agent-discovery surface — nothing more.

The site is HORIZONTAL at the brand level (D-002, P-01) — the root home page says "marketplace for high-value used goods", not "marketplace for tractors". The W1 category emphasis lives at the front-door level (`/tractors` is unambiguously a farm-equipment page) but never at the brand level.

## Clarifications

### Session 2026-05-19 (founder seed, pre-`clarify` — then grill-with-docs + inline clarify pass)

Eleven decisions resolved during the 2026-05-19 sessions, listed below. Q1–Q3 originated as `[NEEDS CLARIFICATION]` markers (capped at 3 per [`ezley-docs/10-speckit-handoff.md §8`](../../../../ezley-docs/10-speckit-handoff.md)); Q4–Q7 were domain-model gaps surfaced during the grill-with-docs pass and resolved inline; Q8–Q9 were the smaller follow-on clarify items flagged during the consistency-check pass; Q10 was an in-flight founder review of the rendered home-page copy; Q11 was an in-flight founder review of buyer-protection accuracy on /trust and /how-it-works. All eleven are now closed.

- **Q1 — Marketplace-app hostname**: ~~Working assumption is `app.ezley.com`.~~ **RESOLVED 2026-05-19** by [D-023 — Hostname split](../../../../ezley-docs/09-decisions.md). Marketing site canonically owns `ezley.com/` root; marketplace UI canonically lives at `app.ezley.com/`. Listing URL form per glossary §9 is now `https://app.ezley.com/l/<ListingId>/<title-slug>`. A permanent 301 from `ezley.com/l/*` to `app.ezley.com/l/*` covers legacy inbound traffic. All CTAs in this spec deep-link to `https://app.ezley.com/?utm_source=marketing-site&...`. The hostname comes from `marketing.config.json`, not inline (P-09).
- **Q2 — Brand assets**: ~~Working assumption is design-token parity with the marketplace app.~~ **RESOLVED 2026-05-19** — parity-plus-marketing-overlay. The marketing site SHARES the MUI 5 color tokens, wordmark, and type-scale baseline with `ezley-market-ui-react` (per [D-009](../../../../ezley-docs/09-decisions.md)) so a visitor going `ezley.com/pricing` → `app.ezley.com/signup` sees one consistent Ezley brand across the funnel. The marketing site ADDS a marketing-only overlay layer: hero photography, a display headline font for hero/section heads, looser whitespace, and a small set of marketing-specific components (testimonial cards, competitor-comparison rows, hero photographs). See NFR-MKT-013.
- **Q3 — Pre-launch front-door behavior for W2/W3/W4**: ~~Three options were on the table.~~ **RESOLVED 2026-05-19** — option (b): a static "coming soon" interstitial with a wedge-thesis snippet from [`ezley-docs/01-vision.md §4`](../../../../ezley-docs/01-vision.md), site chrome unchanged, no email capture. Email capture is deferred to F-MKT-002, which will be a separate spec with its own notification-pipeline and captured-leads data-store work. See FR-MKT-023.

- **Q4 — Competitor framing in "Why Ezley"**: **RESOLVED 2026-05-19** — hybrid voice. Facebook Marketplace and Craigslist remain named (escrow / trust-layer gap, well-established and defensible). eBay's February 2026 agent ban is referenced as a historical fact but is NOT the headline of its row (so it ages gracefully if eBay reverses). TractorHouse, Machinery Pete, and other vertical-catalog incumbents are referred to anonymously as "category-specific catalog sites without escrow" — never named in body copy or headline. See FR-MKT-012..016.

- **Q5 — Sub-brand naming and visibility at M0**: **RESOLVED 2026-05-19** — section-tag plus page title plus body copy, NOT header replacement. Header wordmark stays "Ezley" everywhere (FR-MKT-020). Sub-brand names (Ezley Equipment / Motors / Gear / Home) surface in the HTML `<title>`, a section-tag chip above the H1, and body copy. eBay Motors playbook: "Motors" is a section, not a logo. See FR-MKT-026.

- **Q6 — Trust-anchor copy at M0**: **RESOLVED 2026-05-19** — Ezley-owned trust signals only. Home page and /tractors trust strips MUST use Ezley-owned concrete language (wire payment, escrow-held funds, identity-verified sellers, concierge support) and MUST NOT name Machinery Pete, RFD-TV, Successful Farming, Farm Journal, county Farm Bureau, or any other third-party brand at M0. Named third-party social proof is deferred to a future F-MKT-003 spec and requires real signed partnerships first. See FR-MKT-019, User Story 2 scenario 2.

- **Q7 — Single trust anchor for the home page hero**: **RESOLVED 2026-05-19** — escrow / buyer-funds-held. The hero MUST anchor the trust pitch on a single mechanism (escrow), NOT a compound bundle. Other trust mechanisms (identity tiers, concierge, dispute process) live on the /trust depth page, never in the hero. Rationale: escrow maps to the visitor's actual high-AOV fear, is the cleanest differentiator vs named alternatives, and is the most concrete provable claim. See FR-MKT-010a.

- **Q8 — Dispute SLA + resolution-rate claims at M0**: **RESOLVED 2026-05-19** — defer numeric claims; describe the process only. /trust and /how-it-works describe the dispute *process* (defined adjudication framework, named human operator, documented steps, escalation rights) but MUST NOT publish a "< 5 business days" SLA target, a "> 90 %" resolution-rate target, or any other aspirational dispute number. F-MKT-003 (post-M9) publishes measured numbers once meaningful dispute volume exists. Honors P-02: "we won't accept growth that erodes trust" — no numeric promises before they are operational. See FR-MKT-035, FR-MKT-052, FR-MKT-055.

- **Q9 — Internal-ops debug panel on /pricing**: **RESOLVED 2026-05-19** — dropped from M0. FR-MKT-047 retired. No internal-ops debug panel ships in this spec. If a future ops or finance need surfaces for cost-itemization on a marketing-side page, it lands in a separate spec with a named consumer, a proper auth gate (not a query-string toggle), and an explicit copy template. Ops itemization lives on internal admin tools, not the public site. See FR-MKT-047 retirement notice, User Story 4 scenario 6 retirement notice.

- **Q10 — "Agent-native" in public lead copy**: **RESOLVED 2026-05-19** — drop from headlines and meta descriptions; keep only on `/agents` (audience-appropriate) and in the home-page "Why Ezley" row about AI shopping assistants (translated to consumer language: "ChatGPT, Claude, Gemini" rather than "agents"). Internal vision in `ezley-docs/01-vision.md` is unchanged — agent-native is real strategic posture, just not consumer-friendly vocabulary. Three rendering surfaces updated: home hero (`_data/copy/en-US.yml` headline_vision), home meta description (`index.html` front-matter), site description (`_config.yml`). See FR-MKT-010.

- **Q11 — Buyer protection accuracy on /trust and /how-it-works**: **RESOLVED 2026-05-19** — the original copy ("The seller never holds your money before you've received the item") was strictly false. Auto-release fires per the chosen window even without explicit buyer confirmation, by design (P-06: "Buyer confirmation is one trigger. Time-based auto-release with seller evidence is another."). The marketing copy must therefore (a) drop the over-promise, and (b) surface the post-release dispute window from glossary §6 `Escrow:DisputeWindowDays` so a visitor reading the page understands the full safety envelope — not just the pre-release portion. FR-MKT-053 tightened with four required framing points. `/how-it-works` buyer flow steps 6 + 7 rewritten to reflect that release is triggered by either confirmation OR window-pass, and that a post-release dispute window exists. The 14-day numeric value is intentionally not stated on the page, matching FR-MKT-054's wire-threshold framing. See FR-MKT-053.

## Decision context (motivating documents)

The product-level decisions and principles that motivate this spec:

| ID | Title | Effect on this spec |
|---|---|---|
| [D-001](../../../../ezley-docs/09-decisions.md) | Domain: Ezley.com | Site lives at `ezley.com` root; category front-doors are sub-paths, not sub-domains. |
| [D-002](../../../../ezley-docs/09-decisions.md) | Marketplace shape: broad horizontal, wedge-launched | Brand-level copy is horizontal ("marketplace for high-value used goods"). Category emphasis lives at front-door level, never brand level. |
| [D-009](../../../../ezley-docs/09-decisions.md) | UI library: MUI 5 | Marketing site reuses MUI 5 color tokens + wordmark + type baseline from `ezley-market-ui-react` for funnel-consistency (NFR-MKT-013). |
| [D-014](../../../../ezley-docs/09-decisions.md) | Spec depth: PM-style (what + why), SpecKit fills how | This spec does NOT pick a tech stack. `plan.md` picks framework, build tool, hosting. |
| [D-016](../../../../ezley-docs/09-decisions.md) | Free venue, paid trust + paid distribution | Pricing page leads with "Free to list. Pay only when you sell." |
| [D-019](../../../../ezley-docs/09-decisions.md) | Take-rate by payment path, single bundled rate | Pricing page reads three Rail rates from `/api/config/public` and renders one bundled fee per Rail. Never itemizes Stripe / Connect / concierge / reserve. |
| [D-023](../../../../ezley-docs/09-decisions.md) | Hostname split: ezley.com = marketing, app.ezley.com = marketplace | Marketing site canonically owns `ezley.com/`. Marketplace UI at `app.ezley.com/`. All CTAs deep-link to the `app.` host (resolution of Q1). |
| [P-01](../../../../ezley-docs/03-product-principles.md) | Horizontal infra, vertical GTM | Root brand is category-agnostic. Category front-doors are tailored. |
| [P-02](../../../../ezley-docs/03-product-principles.md) | Trust is the product | Every page surfaces the trust-first value prop. |
| [P-03](../../../../ezley-docs/03-product-principles.md) | Agent-native, not agent-tolerant | Site is crawlable. No Cloudflare gating. `llms.txt` + `mcp.json` pointer. |
| [P-06](../../../../ezley-docs/03-product-principles.md) | Buyer-funds-held until receipt | Hero trust anchor (Q7); /trust depth page explains the mechanism in plain English. |
| [P-08](../../../../ezley-docs/03-product-principles.md) | Mobile-first, accessible | 320 px first. WCAG 2.1 AA. 48 px touch targets. |
| [P-09](../../../../ezley-docs/03-product-principles.md) | Configurable, not hardcoded | Every fee, threshold, window number comes from `/api/config/public` or `marketing.config.json` — never inline. |
| [P-11](../../../../ezley-docs/03-product-principles.md) | Free venue, paid trust | Pricing page copy embeds this directly. |
| [P-14](../../../../ezley-docs/03-product-principles.md) | Bundle the rails into the value, not the price | Pricing page shows ONE bundled fee per Rail. Never "Stripe X + Connect Y + concierge Z". |

## User Scenarios & Testing *(mandatory)*

### User Story 1 — A first-time visitor understands what Ezley is and decides whether to sign up (Priority: P1)

**As a** working farmer / rural homeowner / dealer / AI buying agent landing on `ezley.com` for the first time,
**I want to** understand within 30 seconds what Ezley is, what category of mine it serves, how my money is protected, and why I would pick Ezley over the alternatives I currently use (Facebook Marketplace, Craigslist, general auction marketplaces, category-specific catalog sites),
**so that** I can decide in one session whether to sign up to sell or to browse listings.

**Why this priority**: This is the ONE job of the marketing site at M0. Every other page (How it works, Pricing, Trust & Safety, category front-doors, agent surface) is a depth surface that supports the home-page pitch. P1 because if this user story fails, no other page on the site matters.

**Independent test**: A first-time visitor (no prior Ezley awareness) lands on `ezley.com` on a 320 px-wide phone over a simulated Slow 4G + mid-tier mobile profile. Within 2.5 s the Largest Contentful Paint completes. Within 30 s of scrolling, they can recite (a) the one-line vision, (b) one concrete differentiator vs Facebook Marketplace, (c) the fact that escrow protects their money, and (d) the primary CTA destination. A click on the primary CTA lands on `app.ezley.com` (per [D-023](../../../../ezley-docs/09-decisions.md)) carrying `utm_source=marketing-site&utm_campaign=home&utm_content=hero-primary-cta`.

**Acceptance scenarios**:

1. **Given** I land on `ezley.com` for the first time, **when** the home page renders, **then** the hero copy names Ezley as **"the open, agent-native marketplace for high-trust, high-value, peer-to-peer commerce"** (verbatim from [`ezley-docs/01-vision.md` §"One-line vision"](../../../../ezley-docs/01-vision.md)), with a single primary CTA and at most one secondary CTA — no more.
2. **Given** I scroll past the hero, **when** the "Why Ezley" section renders, **then** it shows contrasts in the hybrid voice resolved in Q4: **Facebook Marketplace** (named) and **Craigslist** (named) appear with concrete differentiators; **general auction marketplaces** appears as a row referencing eBay's early-2026 agent ban in supporting body copy (not headline); **category-specific catalog sites without escrow** appears as an anonymous row covering the W1 vertical alternatives. No row uses vague language; no vertical-catalog incumbent (TractorHouse, Machinery Pete) is named.
3. **Given** the "Why Ezley" section, **when** I read the general-auction-marketplaces row, **then** the body copy notes that eBay banned third-party "buy-for-me" agents in early 2026 (per FR-MKT-013 — eBay named in body, not headline) and the differentiator framed for that row is Ezley's agent-native posture, not a fee comparison. The row's framing gracefully degrades to a structured-listings differentiator if eBay reverses the ban.
4. **Given** I scroll past "Why Ezley", **when** the "How it works" preview renders, **then** it shows the buyer flow and the seller flow as a compressed visual (full detail on the dedicated How it works page), using EXACTLY the glossary terms `Listing` / `Transaction` / `Offer` / `Escrow` / `Handover` / `Auto-release`.
5. **Given** I scroll to the bottom of the home page, **when** the footer renders, **then** it links to How it works, Pricing, Trust & Safety, Agent-readiness, and the four category front-doors (`/tractors`, `/cars`, `/cameras`, `/furniture`), plus standard legal footer (Terms, Privacy, Contact).
6. **Given** I click the primary CTA from the hero, **when** the click resolves, **then** I am deep-linked to the marketplace app carrying `utm_source=marketing-site&utm_campaign=home&utm_content=hero-primary-cta`. The marketing site itself renders NO authentication, signup, or listing-creation form — those live in the marketplace app per [D-003](../../../../ezley-docs/09-decisions.md) + [D-010](../../../../ezley-docs/09-decisions.md).

### User Story 2 — A farmer arriving from a `/tractors` flyer sees a category-tailored landing (Priority: P1)

**As an** S-FARMER who saw a flyer in a dealer parts department,
**I want to** land on `ezley.com/tractors` and see content that speaks to my world (heavy equipment, wire payments, no shipping required, concierge available),
**so that** I trust this isn't a generic e-commerce site that treats a tractor like a couch.

**Why this priority**: P-01 (horizontal infra, vertical GTM) explicitly calls out category-specific front doors. The W1 wedge depends on S-FARMER and S-DEALER trust at the front door, and these personas are skeptical of new platforms. P1 because if S-FARMER bounces in the first 5 s of `/tractors`, the W1 acquisition channel breaks.

**Independent test**: A visitor lands directly on `ezley.com/tractors` (no referrer). Within 2.5 s LCP completes. The hero, social-proof strip, and "Why Ezley" rows are TAILORED to S-FARMER's language ("Sell your tractor to a verified buyer. Wire payment, no shipping, concierge available."), but the wordmark, primary navigation, and footer are byte-identical to the root home page (per P-01: front-door, not fork).

**Acceptance scenarios**:

1. **Given** I land directly on `ezley.com/tractors`, **when** the page renders, **then** the hero copy is category-tailored to S-FARMER per [`ezley-docs/02-personas-and-jobs.md` §S-FARMER](../../../../ezley-docs/02-personas-and-jobs.md) (mentions farm-equipment-relevant concerns: wire payment, no shipping, concierge); the brand wordmark in the header reads **"Ezley"** (never "Ezley Tractors" or "Ezley Equipment" — per FR-MKT-020); AND a small section-tag chip reading **"Ezley Equipment"** renders above the H1 per FR-MKT-026 (resolution of Q5). The `<title>` element follows the pattern `"Ezley Equipment — Tractors, Combines, Implements | Ezley"`.
2. **Given** I land on `/tractors`, **when** the trust strip renders, **then** it surfaces only **Ezley-owned trust signals** (verbatim from FR-MKT-019: wire payment, escrow-held funds, identity-verified sellers, concierge availability) and MUST NOT name Machinery Pete, RFD-TV, Successful Farming, Farm Journal, county Farm Bureau, or any other third-party trust anchor from [`ezley-docs/02-personas-and-jobs.md` §S-FARMER §Trust anchors](../../../../ezley-docs/02-personas-and-jobs.md). Named third-party social proof is deferred to F-MKT-003 and requires real signed partnerships first. [Resolution of Q6, 2026-05-19]
3. **Given** I land on `/tractors`, **when** I click the primary CTA, **then** I am deep-linked to the marketplace app with `utm_source=marketing-site&utm_campaign=tractors&utm_content=hero-primary-cta`.
4. **Given** I land on `/cars`, `/cameras`, or `/furniture` (W2/W3/W4 — not live at M0), **when** the page renders, **then** I see a `coming soon` interstitial with a static snippet from [`ezley-docs/01-vision.md §4`](../../../../ezley-docs/01-vision.md) explaining the wedge thesis, and the navigation/footer are unchanged. Email capture is OUT OF SCOPE here (deferred to F-MKT-002) per FR-MKT-023.

### User Story 3 — A buyer/seller reads "How it works" and understands the escrow flow (Priority: P1)

**As a** prospective buyer or seller researching Ezley before committing,
**I want to** read a plain-English explanation of how a transaction works end-to-end (offer → escrow-funded → handover → auto-release → seller paid),
**so that** I trust the trust layer enough to put real money through it.

**Why this priority**: P-02 ("trust is the product") explicitly states the marketplace IS the trust layer. The How it works page is where that pitch lands. P1 because trust is what differentiates Ezley from Facebook Marketplace and Craigslist, and the public marketing site is where buyers and sellers first read the trust story.

**Independent test**: A visitor opens `ezley.com/how-it-works` and reads the page top-to-bottom. The buyer flow and seller flow both render using ONLY the glossary terms `Listing`, `Transaction`, `Offer`, `Escrow`, `Handover`, `Auto-release` — no synonyms. The page never says "order", "item", "hold funds", or "charge".

**Acceptance scenarios**:

1. **Given** I open `/how-it-works`, **when** the buyer flow renders, **then** the steps are `Browse listings → Make an Offer → Buyer funds Escrow (Captured) → Seller and buyer arrange Handover → Buyer confirms → Auto-release → Seller paid in ≤ 9 days from buyer confirmation` (per [`ezley-docs/05-target-state.md §Escrow and payments`](../../../../ezley-docs/05-target-state.md)).
2. **Given** I open `/how-it-works`, **when** the seller flow renders, **then** the steps are `Create a Listing → Concierge auto-approves or reviews per tier (D-018) → Listing goes Live → Buyer funds Escrow → Handover → Buyer confirms → Auto-release → Paid out` and explicitly says "Concierge reviewing" never "Awaiting approval" (per [D-018](../../../../ezley-docs/09-decisions.md) UI copy contract).
3. **Given** I read the Auto-release section, **when** the copy describes the window, **then** the window value (72 hours default per `Payments:AutoReleaseHoursDefault`) is read at render time from `/api/config/public` — it is NOT a string literal in the marketing site source per P-09. If the config endpoint is unreachable, the most recent successful snapshot in `marketing.config.json` is used; if that's also missing, the page renders the window as "the buyer's chosen review window (typically 1–7 days)" rather than printing a wrong number.
4. **Given** I read about identity verification, **when** the copy describes the tiers, **then** the tier names are exactly `BASIC`, `VERIFIED`, `TRUSTED_SELLER` (per [`ezley-docs/08-glossary.md §5.7`](../../../../ezley-docs/08-glossary.md)) — never "bronze/silver/gold" or other invented names.

### User Story 4 — A seller reads the Pricing page and sees one bundled fee per Rail (Priority: P1)

**As a** prospective seller deciding whether Ezley is worth the fee,
**I want to** see a clear, honest, single-line bundled fee per payment path (Card / ACH / Wire),
**so that** I know what I'll net on a sale and can compare against the alternatives without doing math.

**Why this priority**: D-019 + P-14 together commit Ezley to bundled pricing. If the marketing site itemizes Stripe / Connect / concierge / reserve, it violates the principle and creates a competitive vulnerability (per P-14 rationale). P1 because the pricing page is the second-most-read page after the home page, and getting this wrong is corrosive to long-term pricing power.

**Independent test**: A visitor opens `ezley.com/pricing`. Three rate cards render, one per Rail. Each card shows ONE bundled percentage and ONE plain-language description of what's included. A grep of the rendered HTML for `"Stripe"`, `"Connect"`, `"concierge fee"`, `"reserve"`, `"breakdown"` returns ZERO matches in seller-facing copy. The percentages match the values returned by `GET /api/config/public` at render time.

**Acceptance scenarios**:

1. **Given** I open `/pricing`, **when** the page renders, **then** I see three rate cards in this order: **Wire (3.0 %)**, **ACH (3.5 %)**, **Card (5.5 %)** — with the percentages sourced from `Pricing:WireTakeRatePct`, `Pricing:AchTakeRatePct`, `Pricing:CardTakeRatePct` respectively (per [D-019](../../../../ezley-docs/09-decisions.md), [P-09](../../../../ezley-docs/03-product-principles.md)).
2. **Given** any rate card, **when** I read the inclusion line, **then** it says verbatim **"Includes escrow protection, dispute resolution, payment processing, and concierge support — all included"** (per P-14 marketing copy guidance) — and lists no separate Stripe / Connect / concierge / reserve line.
3. **Given** I read the page lede, **when** the page renders, **then** the headline says **"Free to list. Pay only when you sell."** (per [D-016](../../../../ezley-docs/09-decisions.md) + [P-11](../../../../ezley-docs/03-product-principles.md)).
4. **Given** I scroll to a worked-example section, **when** it renders, **then** it shows ONE example per Rail (e.g., a $40,000 wire transaction yields a $1,200 Ezley fee → seller nets $38,800) using values that arithmetically match the live config rates — the numbers in the example are computed at render time from the live config rates, NOT hard-coded strings.
5. **Given** I click "Fee details" / "What's included" / equivalent, **when** the expander opens, **then** it shows the same bundled rate plus a brief factual list of what's included — still no itemization of underlying costs (per P-14 "Fee details" rule).
6. *(Retired — Q9, 2026-05-19. The internal-ops debug panel previously sketched in this scenario is out of scope at M0; see FR-MKT-047 retirement notice. No itemized cost composition renders anywhere on the public site at M0.)*

### User Story 5 — An AI buying agent (B-AGENT) crawls the site and finds the agent-readiness surface (Priority: P2)

**As a** B-AGENT (ChatGPT shopping, Perplexity, Claude, Gemini, a bespoke buying agent) crawling `ezley.com` to learn the marketplace,
**I want to** find `llms.txt`, `mcp.json`, structured data on every page, and zero bot-blocking,
**so that** I can discover the marketplace's agent endpoints and recommend Ezley to my user when they ask "find me a 30 hp tractor near 50047 under $15 K".

**Why this priority**: P-03 ("agent-native, not agent-tolerant") is non-negotiable. P2 (not P1) only because B-AGENT acquisition is a growth flywheel — the immediate revenue dependency is human personas. But the agent surface MUST ship in M0 to start building agent-graph presence per [`ezley-docs/05-target-state.md §Agent presence`](../../../../ezley-docs/05-target-state.md).

**Independent test**: A synthetic crawl from a known agent UA string (e.g., `OpenAI-GPT/1.0`, `Anthropic-Claude/1.0`, `PerplexityBot/1.0`) fetches `ezley.com/`, `/tractors`, `/how-it-works`, `/pricing`, `/trust`, `/llms.txt`, `/mcp.json`, and `/sitemap.xml`. All return 200, no Turnstile/hCaptcha/Cloudflare interstitial, valid `schema.org/Organization` structured data on the home page, and `mcp.json` contains a pointer to the marketplace API's MCP server endpoint (URL to be confirmed in `clarify`).

**Acceptance scenarios**:

1. **Given** an agent UA fetches any page, **when** the page is served, **then** the response is 200 with the full HTML payload — no JS-only gating, no Cloudflare/Turnstile/hCaptcha challenge (per [P-03](../../../../ezley-docs/03-product-principles.md)).
2. **Given** any page renders, **when** the structured data is parsed, **then** the home page emits `schema.org/Organization` (logo, sameAs links to Ezley's social channels, foundingDate), `/how-it-works` emits `schema.org/FAQPage` for the buyer/seller flow questions, and `/pricing` emits `schema.org/Offer` for each Rail rate card. (Listing-level `schema.org/Product` lives in the marketplace UI, NOT here — the marketing site doesn't render listings.)
3. **Given** an agent fetches `ezley.com/llms.txt`, **when** the response is parsed, **then** it follows the [llmstxt.org](https://llmstxt.org) format and contains pointers to (a) the marketplace's public docs site (post-M9), (b) the agent endpoints (MCP/ACP/UCP/AP2 base URLs), and (c) the live category front-doors.
4. **Given** an agent fetches `ezley.com/mcp.json`, **when** the response is parsed, **then** it returns a JSON document pointing at the marketplace API's MCP server endpoint (the marketing site itself does NOT host an MCP server — it only points).
5. **Given** an agent fetches `ezley.com/sitemap.xml`, **when** parsed, **then** the sitemap includes every public marketing route (root, /tractors, /cars, /cameras, /furniture, /how-it-works, /pricing, /trust, /agents) with valid `<lastmod>` timestamps.
6. **Given** an agent fetches `ezley.com/robots.txt`, **when** parsed, **then** it explicitly `Allow: /` and lists the sitemap. There is no `Disallow:` for any agent UA.

### User Story 6 — A concierge demos Ezley to a prospective seller in the field on a tablet (Priority: P2)

**As an** O-CONCIERGE on a tablet at a farm, demoing Ezley to a prospective S-FARMER,
**I want to** load the home page + How it works + Pricing on a flaky rural connection and walk the seller through the value prop without anything breaking or showing stale numbers,
**so that** the demo lands and I can sign them up on the spot.

**Why this priority**: Concierge demos are the primary supply-side acquisition path in W1 per [`ezley-docs/03-product-principles.md` P-04](../../../../ezley-docs/03-product-principles.md). P2 because the concierge can do this demo on the marketplace app itself if needed — but the marketing site is the preferred surface because it doesn't require login and is read-only.

**Independent test**: A concierge tablet (mid-tier Android, intermittent connectivity) loads `ezley.com/` once on Wi-Fi, then drops to cellular (or offline), then navigates to `/how-it-works` and `/pricing`. Both pages render from cache. The Pricing page shows values from the most recent successful config snapshot, not a stale literal, not an error.

**Acceptance scenarios**:

1. **Given** the home page, /how-it-works, and /pricing have been loaded once, **when** the device goes offline and the user navigates between them, **then** all three pages render from cache and the Pricing page displays the most recent config snapshot's values.
2. **Given** the cached config snapshot is older than 24 h AND the device is online, **when** the Pricing page is opened, **then** the site re-fetches `/api/config/public` and updates the displayed rates.
3. **Given** the cached config snapshot is older than 24 h AND the device is offline, **when** the Pricing page is opened, **then** the page renders the stale snapshot with no visible "stale" indicator to the visitor (the staleness is exposed in `/health` for ops, not in user-facing copy).

### User Story 7 — A visitor on a screen reader navigates the entire site (Priority: P2)

**As a** visitor using a screen reader (VoiceOver / NVDA / TalkBack) on a phone or laptop,
**I want to** navigate every page, read every heading hierarchy, understand every link's destination, and operate every CTA without sight,
**so that** Ezley meets its WCAG 2.1 AA commitment from day one (per P-08, not retrofit).

**Why this priority**: P-08 is non-negotiable. P2 because the user volume on screen readers is smaller than the volume of farmers on phones, but the principle commitment is just as strong. axe-core in CI is the automated gate; a manual screen-reader pass is the human gate.

**Independent test**: An axe-core scan on every public route returns ZERO critical violations. A manual VoiceOver pass through the home page → "Why Ezley" → How it works → Pricing → Trust & Safety completes successfully — the visitor can recite the value prop, the fee structure, and reach a CTA target.

**Acceptance scenarios**:

1. **Given** any public page, **when** axe-core scans it in CI, **then** there are zero critical or serious violations.
2. **Given** any interactive element, **when** focused via keyboard, **then** the focus indicator is visible with a contrast ratio ≥ 3:1 and the element is operable via Enter/Space.
3. **Given** any image, **when** rendered, **then** it has either an informative `alt` attribute or `alt=""` for decorative images. No image is missing the `alt` attribute entirely.
4. **Given** any page, **when** rendered, **then** the heading hierarchy starts at `<h1>` and does not skip levels.
5. **Given** any color-encoded information (e.g., a green "available" badge), **when** rendered, **then** the information is ALSO conveyed by text or an icon — color is never the only carrier.

### User Story 8 — A page is rendered fast enough on a slow rural connection (Priority: P2)

**As a** farmer in rural Iowa with a slow LTE connection on a mid-tier Android phone,
**I want to** load `ezley.com` in under 2.5 s and not have to wait for JS to interact,
**so that** I don't bounce before the hero copy renders.

**Why this priority**: P-08 (mobile-first) and the W1 acquisition channel (rural buyers/sellers on phones) make this a hard requirement, not an aspiration. P2 only because the failure mode is "user bounces" rather than "user is harmed" — but a slow site directly converts to lower W1 acquisition.

**Independent test**: A Lighthouse run on every public route on a simulated "Slow 4G" / "mid-tier mobile" profile yields LCP ≤ 2.5 s, CLS ≤ 0.1, TBT ≤ 200 ms. The home page, /how-it-works, /pricing, /trust are fully readable and the primary CTA is operable WITHOUT JavaScript executing.

**Acceptance scenarios**:

1. **Given** any public page on a simulated Slow 4G + mid-tier mobile profile, **when** Lighthouse measures it, **then** LCP ≤ 2.5 s.
2. **Given** any public page, **when** measured, **then** CLS ≤ 0.1.
3. **Given** the home page, /how-it-works, /pricing, /trust, **when** loaded with JavaScript disabled, **then** the page is fully readable, the heading hierarchy is intact, every link is operable, and the primary CTA navigates to its destination via a standard `<a href>`.
4. **Given** the category front-door pages (/tractors, /cars, /cameras, /furniture), **when** loaded with JavaScript disabled, **then** the page is still readable and the primary CTA is still operable (the JS is for personalization layered on top — never for baseline operability).

## Functional Requirements

Grouped by page. Each FR cites the principle(s) and decision(s) it enforces.

### Site-wide chrome (header, footer, navigation)

- **FR-MKT-001** — The site MUST render a consistent header on every page containing the **Ezley** wordmark (linking to root), primary navigation (How it works, Pricing, Trust & Safety, For agents), and a single primary CTA ("Start selling" or "Browse listings" — see FR-MKT-002 for selection logic). [P-01]
- **FR-MKT-002** — The primary CTA in the header MUST present a single button labeled either **"Start selling"** or **"Browse listings"**. On first visit, the default label is **"Browse listings"**. After the visitor interacts with any seller-oriented surface (Pricing page, /tractors, any CTA labeled "Start selling"), the header CTA label switches to **"Start selling"** for the remainder of the session. The change is stored in `sessionStorage` only — no cookie, no tracking, no cross-session memory. [P-08]
- **FR-MKT-003** — The site MUST render a consistent footer on every page containing: links to How it works, Pricing, Trust & Safety, For agents, the four category front-doors (/tractors, /cars, /cameras, /furniture), and legal links (Terms, Privacy, Contact). The footer is IDENTICAL across root and all category front-doors per [P-01]. [P-01]
- **FR-MKT-004** — Every CTA on every page MUST deep-link into the marketplace app at `app.ezley.com` (per [D-023](../../../../ezley-docs/09-decisions.md)) carrying `utm_source=marketing-site&utm_campaign=<page>&utm_content=<cta-id>` query-string parameters. The hostname MUST come from `marketing.config.json`, NEVER hard-coded. [P-09, D-003, D-023]
- **FR-MKT-005** — The marketing site MUST NOT render any authentication form, signup form, listing-creation form, or browse/search UI. These live in the marketplace app. [D-003, D-010]

### Home page (root: `ezley.com`)

- **FR-MKT-010** — The home page hero MUST display a public-marketing adaptation of the one-line vision: **"The open marketplace for high-trust, high-value, peer-to-peer commerce."** The internal vision in [`ezley-docs/01-vision.md` §"One-line vision"](../../../../ezley-docs/01-vision.md) reads "open, **agent-native** marketplace…". The "agent-native" qualifier is intentionally dropped from public hero copy because it reads as jargon to the primary personas (S-FARMER, S-RURAL-HOMEOWNER, B-FARMER, B-RURAL-HOMEOWNER) — a farmer on a phone has no reference for the term, and the closest available reading ("real-estate agent", "insurance agent") would actively confuse them. The agent-native strategic posture is preserved end-to-end: it surfaces on `/agents` (where the audience expects technical terms; FR-MKT-060..062), in the home-page "Why Ezley" row about AI shopping assistants (FR-MKT-013), and in the agent-discovery files (`/llms.txt`, `/mcp.json` — FR-MKT-070..074). The internal vision document is unchanged; this is a marketing-voice adaptation, not a strategy change. [Resolution of Q10, 2026-05-19]
- **FR-MKT-010a** — The home page hero MUST anchor the trust pitch on **a single mechanism: escrow / buyer-funds-held-until-receipt** (per [P-06](../../../../ezley-docs/03-product-principles.md)). Acceptable language patterns: "Your money stays in escrow until you confirm what you bought", "Escrow-held funds — released when you confirm receipt", "We hold your money until handover is confirmed." The hero MUST NOT bundle multiple trust mechanisms into the hero pitch (no compound "Escrow + identity verification + dispute resolution + concierge" hero). Other trust mechanisms (identity tiers, concierge, dispute SLA, wire-only above threshold) live on the /trust depth page (FR-MKT-050..055), not in the hero. Rationale: escrow is the mechanism that maps most directly to the visitor's actual high-AOV fear, is the cleanest differentiator vs Facebook Marketplace + Craigslist + category-specific catalog sites (FR-MKT-014..016), and is the most concrete provable claim (Stripe Connect, P-06). [P-02, P-06; resolution of Q7, 2026-05-19]
- **FR-MKT-011** — The home page hero MUST present exactly one primary CTA and at most one secondary CTA. No third CTA, no overlay, no modal at first visit. [P-08]
- **FR-MKT-012** — The "Why Ezley" section MUST contrast Ezley against the competitive landscape using a HYBRID voice (per Clarifications Q4, 2026-05-19): two named consumer marketplaces (**Facebook Marketplace**, **Craigslist**), one referenced-by-historical-fact incumbent (eBay's February 2026 third-party agent ban), and one or more anonymous category-specific catalog references ("category-specific catalog sites without escrow") covering the W1 vertical alternatives. Each row states ONE concrete differentiator (escrow / identity verification / agent-readiness / structured listings / transparent bundled fee). No vague language; no named-by-name claims against vertical catalog incumbents (TractorHouse, Machinery Pete). [P-02, P-03]
- **FR-MKT-013** — The "general auction marketplaces" row MUST reference eBay's 2026-02-20 third-party agent ban as a historical fact — phrased as "general auction marketplaces banned third-party buying agents in early 2026", with eBay named in supporting body copy or a footnote rather than the headline. The differentiator is Ezley's agent-native posture (P-03), not a price comparison. The row's framing MUST gracefully degrade if eBay reverses the ban (a stated Revisit trigger in [D-002](../../../../ezley-docs/09-decisions.md)): if the ban is reversed, this row updates to a structured-listings differentiator without restructuring the section. [Source: [`ezley-docs/01-vision.md` §"The problem we are solving"](../../../../ezley-docs/01-vision.md), [P-03]]
- **FR-MKT-014** — The Facebook Marketplace contrast row MUST cite the escrow gap ("Facebook Marketplace captures the conversation but provides no transaction layer" — verbatim from [`ezley-docs/01-vision.md`](../../../../ezley-docs/01-vision.md)) as the differentiator. The named Facebook Marketplace claim is retained because the escrow gap is well-established and Meta makes no claim to operate escrow. [P-02]
- **FR-MKT-015** — The Craigslist contrast row MUST cite the transaction-layer gap ("Craigslist captures the listing but provides no trust layer" — verbatim) as the differentiator. The named Craigslist claim is retained for the same reason as FR-MKT-014. [P-02]
- **FR-MKT-016** — The vertical-catalog contrast row MUST refer to the category anonymously — **"category-specific catalog sites without escrow"** — and MUST NOT name TractorHouse, Machinery Pete, or any other vertical-catalog incumbent by name in this row's body copy or headline. The differentiator is the escrow gap. (Vertical catalogs provide a legitimate listing function; the contrast is narrowly on the trust layer, not on the catalogs themselves. This framing also ages better as the W2/W3/W4 wedges launch — the same row generalizes to "general auction marketplaces", "horizontal classifieds", etc. without spec churn.) [P-02]
- **FR-MKT-017** — The home page MUST include a compressed "How it works" preview (buyer flow + seller flow as short visuals) using ONLY the glossary terms `Listing`, `Transaction`, `Offer`, `Escrow`, `Handover`, `Auto-release`. Synonyms ("order", "item", "hold funds", "charge") are FORBIDDEN. [Glossary §1, D-017]
- **FR-MKT-018** — The home page MUST include a "Free to list. Pay only when you sell." pricing teaser linking to /pricing. [D-016, P-11]
- **FR-MKT-019** — The home page MUST surface trust signals at M0 using **Ezley-owned, concrete, founder-controlled language ONLY** — not third-party brand names. Acceptable copy: "Wire payment supported. Escrow-held funds. Identity-verified sellers. Concierge-supported in your county." FORBIDDEN at M0: any text naming Machinery Pete, RFD-TV, Successful Farming, Farm Journal, county Farm Bureau, dealer brands, trade-press titles, or any other third party as "anchors we're partnering with", "coming soon", or any other formulation that implies a commercial or editorial relationship. Named third-party social proof is deferred to F-MKT-003 (later spec) and may ship ONLY when at least one partnership has signed consent on file. [P-02 — "We will not accept growth that erodes [trust]"; resolution of Q6, 2026-05-19]

### Category front-doors (`/tractors`, `/cars`, `/cameras`, `/furniture`)

- **FR-MKT-020** — The brand wordmark in the header MUST read **"Ezley"** on every page including every category front-door — never "Ezley Tractors", "Ezley Equipment", "Ezley Motors", "Ezley Gear", or "Ezley Home". Sub-brand names surface elsewhere on category front-doors per FR-MKT-026 — never in the header wordmark. [P-01, D-002]
- **FR-MKT-021** — Each category front-door MUST tailor the hero copy, social proof, and "Why Ezley" framing to the wedge's primary persona per [`ezley-docs/02-personas-and-jobs.md` §Persona × wedge matrix](../../../../ezley-docs/02-personas-and-jobs.md):
  - `/tractors` → S-FARMER, S-DEALER, B-FARMER
  - `/cars` → B-RURAL-HOMEOWNER, B-AGENT (W2 — interstitial at M0)
  - `/cameras` → B-RURAL-HOMEOWNER, B-AGENT (W3 — interstitial at M0)
  - `/furniture` → S-RURAL-HOMEOWNER, B-RURAL-HOMEOWNER, B-AGENT (W4 — interstitial at M0)
- **FR-MKT-022** — The header, primary navigation, and footer on every category front-door MUST be byte-identical to the root home page. Front-door = content customization, NOT codebase fork. [P-01]
- **FR-MKT-023** — At M0, the `/cars`, `/cameras`, and `/furniture` routes MUST render a "coming soon" interstitial with a static snippet from [`ezley-docs/01-vision.md §4`](../../../../ezley-docs/01-vision.md) explaining the wedge thesis. Email capture is OUT OF SCOPE in this spec (deferred to F-MKT-002). Site chrome (header, primary nav, footer) is unchanged from the rest of the site. [Resolution of Q3, 2026-05-19]
- **FR-MKT-024** — The `/tractors` route MUST present a category-tailored hero referencing wire payment, no shipping required, concierge availability — addressing S-FARMER's top "Hates / pain points" from the persona doc. [P-02]
- **FR-MKT-025** — Every category front-door's primary CTA MUST deep-link into the marketplace app carrying `utm_campaign=<category>&utm_content=<cta-id>` per FR-MKT-004.
- **FR-MKT-026** — Each category front-door MUST surface the wedge's sub-brand name (per [`ezley-docs/01-vision.md` §"Strategic frame"](../../../../ezley-docs/01-vision.md): **Ezley Equipment** for /tractors, **Ezley Motors** for /cars, **Ezley Gear** for /cameras, **Ezley Home** for /furniture) in exactly three places — and nowhere else:
  - (a) The HTML `<title>` element follows the pattern `"<Sub-brand> — <category-keywords> | Ezley"` (e.g., `"Ezley Equipment — Tractors, Combines, Implements | Ezley"`).
  - (b) A small section-tag pill or breadcrumb element renders ABOVE the H1 on the page, displaying the sub-brand name as a styled chip (visual treatment: muted, secondary — not a competing wordmark).
  - (c) The body copy describing the wedge uses the sub-brand name where natural (e.g., "Ezley Equipment covers tractors, combines, implements, and attachments — wire-payment friendly and concierge-supported in your county").

  The H1 itself stays brand-level "Ezley"-styled (per FR-MKT-020). The header wordmark stays "Ezley" (per FR-MKT-020). Sub-brand recognition is planted via title-tag, section-tag, and body copy — not via header replacement or H1 substitution. This follows the eBay Motors playbook the vision cites: "Motors" is a section, not a logo. [Resolution of Q5, 2026-05-19; [`ezley-docs/01-vision.md` §"Strategic frame" + §"End-state at 36 months"](../../../../ezley-docs/01-vision.md); P-01]

### How it works (`/how-it-works`)

- **FR-MKT-030** — The page MUST render the buyer flow as: **Browse listings → Make an Offer → Buyer funds Escrow (Captured) → Seller and buyer arrange Handover → Buyer confirms → Auto-release → Seller paid in ≤ 9 days from buyer confirmation**. [Glossary §1, [`ezley-docs/05-target-state.md`](../../../../ezley-docs/05-target-state.md)]
- **FR-MKT-031** — The page MUST render the seller flow as: **Create a Listing → Concierge auto-approves or reviews per tier → Listing goes Live → Buyer funds Escrow → Handover → Buyer confirms → Auto-release → Paid out**. [Glossary §1, D-018]
- **FR-MKT-032** — Copy describing the concierge-review step MUST say **"Concierge reviewing — usually under 2 hours"** and MUST NEVER use the phrase **"awaiting approval"**. [D-018 UI copy contract]
- **FR-MKT-033** — The Auto-release section MUST read its default window value (`Payments:AutoReleaseHoursDefault`, default 72 h) from `/api/config/public` at render time, or from `marketing.config.json` if the config endpoint is unreachable. The number MUST NOT be hard-coded inline. [P-09, [`ezley-docs/08-glossary.md` §6](../../../../ezley-docs/08-glossary.md)]
- **FR-MKT-034** — The identity-verification subsection MUST list the three tiers as **`BASIC` / `VERIFIED` / `TRUSTED_SELLER`** verbatim from [`ezley-docs/08-glossary.md §5.7`](../../../../ezley-docs/08-glossary.md) — invented tier names ("bronze/silver/gold") are FORBIDDEN. [Glossary §5.7, D-017]
- **FR-MKT-035** — The dispute section MUST describe the dispute **process** only — not the SLA target, not the resolution-rate target. Acceptable language describes the framework Ezley operates: a defined adjudication framework, a named human operator handling each case, documented steps, and the right of either party to escalate. The page MUST NOT publish a "< 5 business days" SLA, a "> 90 % resolution rate", or any other aspirational dispute number at M0. Measured dispute statistics are published only when meaningful volume exists, per F-MKT-003 (post-M9, separate spec). Aligns with [P-02](../../../../ezley-docs/03-product-principles.md) — "We will not accept growth that erodes trust" — by not making numeric promises before they are operational. [Resolution of Q8, 2026-05-19]

### Pricing (`/pricing`)

- **FR-MKT-040** — The page lede MUST read **"Free to list. Pay only when you sell."** [D-016, P-11]
- **FR-MKT-041** — The page MUST render exactly three rate cards in this order: **Wire (3.0 %)**, **ACH (3.5 %)**, **Card (5.5 %)**. [D-019]
- **FR-MKT-042** — Each rate card's percentage MUST be sourced at render time from `/api/config/public` (`Pricing:WireTakeRatePct`, `Pricing:AchTakeRatePct`, `Pricing:CardTakeRatePct`). If the endpoint is unreachable, the most recent successful `marketing.config.json` snapshot is used. NUMBERS MUST NOT be hard-coded inline. [P-09]
- **FR-MKT-043** — Each rate card's inclusion line MUST read verbatim **"Includes escrow protection, dispute resolution, payment processing, and concierge support — all included"**. [P-14]
- **FR-MKT-044** — Each rate card MUST NOT itemize Stripe / Connect / concierge / reserve as separate lines anywhere on the page. A contract test MUST grep the rendered HTML for `"Stripe"`, `"Connect"` (as a fee item, not as a name in passing), `"concierge fee"`, `"reserve"`, `"breakdown"` and return ZERO matches in seller-facing copy. [P-14]
- **FR-MKT-045** — The worked-examples section MUST render one example per Rail, with the fee amount and net amount computed at render time from the live config rates (not hard-coded). [P-09, P-14]
- **FR-MKT-046** — The "Fee details" / "What's included" expander MUST surface the same bundled rate plus a brief factual list of what's covered — never an itemized cost breakdown. [P-14]
- **FR-MKT-047** — *(Retired — resolution of Q9, 2026-05-19.)* No internal-ops debug panel ships in this spec. If a future ops or finance need surfaces for cost-itemization on a marketing-side page, it lands in a separate spec with a named consumer, a proper auth gate (not a query-string toggle), and an explicit copy template. P-14 does not require an internal escape hatch on the marketing page; ops itemization lives on internal admin tools, not on the public site.
- **FR-MKT-048** — The Pricing page MUST link to /how-it-works for context, and to the marketplace app's Start-selling deep-link for action. [P-08 — minimize friction]

### Trust & Safety (`/trust`)

- **FR-MKT-050** — The page MUST explain the three identity tiers `BASIC` / `VERIFIED` / `TRUSTED_SELLER` with the requirements per [`ezley-docs/05-target-state.md §Identity verification`](../../../../ezley-docs/05-target-state.md). [P-02, Glossary §5.7]
- **FR-MKT-051** — The page MUST describe the escrow flow in plain English: **Captured → Released → Paid out** (no internal state-machine jargon). [Glossary §5.3, P-02]
- **FR-MKT-052** — The page MUST describe the dispute **process** (defined adjudication framework, named human operator, documented steps, escalation rights) — but MUST NOT cite a numeric SLA target or numeric resolution-rate target at M0. Aspirational numbers from target-state.md are not marketing claims until they are operational. F-MKT-003 (post-M9) is the spec that publishes measured dispute statistics. [Resolution of Q8, 2026-05-19; P-02]
- **FR-MKT-053** — The page MUST explain the buyer-funds-held-until-receipt principle ([P-06](../../../../ezley-docs/03-product-principles.md)) in plain English AND MUST mention the post-release dispute window so a visitor understands the full safety envelope. Forbidden framing: "the seller never holds your money before you've received the item" or any equivalent over-promise — strictly false when the buyer takes no action and the auto-release timer fires. Required framing covers four points: (1) money is held in escrow until handover is confirmed and a review window passes; (2) the buyer can confirm receipt directly OR the agreed window passes after handover; (3) auto-release fires per the chosen window even without explicit buyer confirmation; (4) there is a post-release window during which the buyer can still file a dispute, and filing a dispute freezes the funds while it is worked out. The 14-day [`Escrow:DisputeWindowDays`](../../../../ezley-docs/08-glossary.md) value is NOT named numerically on the page (consistent with FR-MKT-054's wire-threshold framing). [P-06, glossary §6 (`Escrow:DisputeWindowDays`); resolution of Q11, 2026-05-19]
- **FR-MKT-054** — The page MUST explain the wire-or-ACH-above-threshold policy (P-07) in plain English: "Above a certain price (we'll show it at checkout), card payment isn't offered. Wire and ACH are the path because they're both safer and cheaper for transactions this size." The threshold number is NOT named on the marketing page — it lives in `Payments:WireOnlyMinAmount` (default $25,000) and is surfaced at checkout in the marketplace app, not here. [P-07, P-09]
- **FR-MKT-055** — The page MUST NOT publish, reference, or *acknowledge the absence of* dispute statistics, SLA target numbers, resolution-rate numbers, or other aspirational dispute metrics at M0. The narrower acceptable copy previously listed in this FR (e.g. "We'll publish dispute statistics once we have meaningful volume") was too generous — even disclaimer-style negative copy ("we don't publish X yet") draws attention to absence and reads as internal-stage commentary, not marketing voice. **Acceptable:** describe the dispute *process* (process steps, named operator, escalation rights). **Forbidden:** any number purporting to describe Ezley's dispute performance (target or actual), AND any meta-commentary about the platform's current data-volume state. Wait until F-MKT-003 (post-M9) ships measured numbers from real volume and publish them straight. This subsumes the SLA / resolution-rate text previously in FR-MKT-052. [P-02 — no fake/seeded metrics; resolution of Q8 2026-05-19; tightened 2026-05-19 after founder review]

### For agents (`/agents`)

- **FR-MKT-060** — The page MUST explain Ezley's agent-native posture and point at the marketplace API's MCP / ACP / UCP / AP2 endpoints (URLs TBC in `clarify` phase). The endpoints themselves are NOT hosted here — this is a pointer surface only. [P-03]
- **FR-MKT-061** — The page MUST explicitly state Ezley does not block agent traffic except for fraud or abuse, and never solely for being agent traffic. [P-03]
- **FR-MKT-062** — The page MUST link to `/llms.txt`, `/mcp.json`, `/sitemap.xml`, and `/robots.txt` as the agent-discovery surface. [P-03]

### Agent-discovery files (root-level)

- **FR-MKT-070** — The site MUST serve `/llms.txt` at the root, following the [llmstxt.org](https://llmstxt.org) format, pointing at (a) the marketplace's public docs site (post-M9 — at M0 this may be a placeholder), (b) the agent endpoint base URLs, and (c) the live category front-doors. [P-03]
- **FR-MKT-071** — The site MUST serve `/mcp.json` at the root, returning a JSON document pointing at the marketplace API's MCP server endpoint. The marketing site itself does NOT host an MCP server. [P-03]
- **FR-MKT-072** — The site MUST serve `/sitemap.xml` at the root, auto-generated, including every public route. [P-03]
- **FR-MKT-073** — The site MUST serve `/robots.txt` at the root, explicitly `Allow: /`, listing the sitemap, with no `Disallow:` for any agent UA. [P-03]
- **FR-MKT-074** — The site MUST NOT use Cloudflare Turnstile, hCaptcha, or any other generic bot-block on any public route. [P-03]

## Non-Functional Requirements

- **NFR-MKT-001** — **Performance budget.** LCP ≤ 2.5 s, CLS ≤ 0.1, TBT ≤ 200 ms on a simulated Slow 4G + mid-tier Android profile in Lighthouse CI, on every public route. [P-08]
- **NFR-MKT-002** — **Accessibility.** WCAG 2.1 AA on every public route. axe-core in CI as the automated gate; ZERO critical or serious violations. A manual screen-reader pass (VoiceOver or NVDA) before each release. Touch targets ≥ 48 px. [P-08]
- **NFR-MKT-003** — **Mobile-first.** Every page rendered at 320 px viewport is fully readable, every CTA fully operable, no horizontal scroll. [P-08]
- **NFR-MKT-004** — **No-JS baseline.** The home page, /how-it-works, /pricing, /trust are fully readable and the primary CTA is operable WITHOUT JavaScript executing. Category front-doors and any personalization features may layer JS on top — but the baseline must hold. [P-08]
- **NFR-MKT-005** — **SEO.** Sitemap auto-generated. Structured data (`schema.org/Organization` on root, `schema.org/FAQPage` on /how-it-works, `schema.org/Offer` on /pricing) validates. No JS-only rendering of critical copy (per NFR-MKT-004). [[`ezley-docs/05-target-state.md` §"Public discovery"](../../../../ezley-docs/05-target-state.md)]
- **NFR-MKT-006** — **Agent-discoverability.** `llms.txt`, `mcp.json`, `sitemap.xml`, `robots.txt` all served at root with 200. No bot-blocking. [P-03]
- **NFR-MKT-007** — **Uptime.** 99.9 % uptime — explicitly LOWER than the marketplace app's 99.95 % buyer-facing target ([`ezley-docs/05-target-state.md` §"Technical state"](../../../../ezley-docs/05-target-state.md)) because the marketing site is not on the transaction path. Downtime here costs acquisition, not revenue.
- **NFR-MKT-008** — **CDN caching.** All public routes cacheable at an edge CDN. Cache invalidation on every deploy. `marketing.config.json` snapshot has a max age of 24 h before re-fetching from `/api/config/public`.
- **NFR-MKT-009** — **Configurability.** Every fee, threshold, window number displayed on the site MUST come from `/api/config/public` or `marketing.config.json` — NEVER inline string literals. CI lint rule enforces this. [P-09]
- **NFR-MKT-010** — **Glossary discipline.** Copy MUST use the exact terms from [`ezley-docs/08-glossary.md`](../../../../ezley-docs/08-glossary.md) §1, §5. Forbidden words from §10 ("order", "item", "hold funds", "charge", "vendor", "customer", "platform fee") MUST NOT appear in user-facing copy. CI lint rule on built HTML enforces this. [D-017]
- **NFR-MKT-011** — **Localization-ready.** Every user-facing string MUST live in a string-catalog module (analogous to `Solution1/src/i18n/` in the marketplace UI) so Spanish localization at M18 is a translation pass, not a rewrite. No inline JSX/HTML strings. [P-08]
- **NFR-MKT-012** — **No PII collection at M0.** No analytics SDK that captures PII. No newsletter capture (deferred to F-MKT-002). UTM parameters on outbound CTAs are anonymous campaign attribution only.
- **NFR-MKT-013** — **Brand-asset parity with marketplace UI, plus marketing overlay.** The marketing site MUST reuse the MUI 5 color tokens, wordmark assets, and type-scale baseline that ship in `ezley-market-ui-react` (per [D-009](../../../../ezley-docs/09-decisions.md)), so a visitor moving from `ezley.com` to `app.ezley.com` does not perceive a brand handoff. The marketing site MAY add a marketing-only overlay layer on top: hero photography, a display headline font for hero/section heads, looser whitespace, and a small set of marketing-specific components (testimonial cards, competitor-comparison rows, hero photography). The overlay MUST NOT redefine or override the inherited color tokens or wordmark — overlay is additive only. The mechanism for sharing the tokens (npm package, git submodule, copy-on-publish — TBD in `plan`) is an implementation detail. [Resolution of Q2, 2026-05-19]

## Content Contract

For every page, copy strings traceable to `ezley-docs`. Values traceable to `/api/config/public`. CTAs traceable to the marketplace app with documented UTM params.

| Page | Verbatim copy from docs | Values from `/api/config/public` | CTA targets |
|---|---|---|---|
| Home | One-line vision (vision §"One-line vision"); escrow-anchored hero trust pitch (FR-MKT-010a, per P-06); hybrid-voice competitor rows: Facebook Marketplace + Craigslist named, general-auction-marketplaces row referencing eBay's early-2026 agent ban in body, anonymous category-specific catalog row (FR-MKT-012..016, vision §"The problem we are solving"); Ezley-owned trust signals (FR-MKT-019) | None (Pricing teaser links to /pricing; no inline numbers) | Primary: `https://app.ezley.com/?utm_source=marketing-site&utm_campaign=home&utm_content=hero-primary-cta` |
| /tractors | S-FARMER persona language (personas §S-FARMER); sub-brand "Ezley Equipment" in `<title>`, section-tag chip, and body copy (FR-MKT-026); Ezley-owned trust signals only (FR-MKT-019 + US2 scenario 2) | None | Primary: `…&utm_campaign=tractors&utm_content=hero-primary-cta` |
| /cars, /cameras, /furniture | Wedge thesis snippet (vision §4); "coming soon" interstitial | None at M0 | None (no CTA at M0 — "coming soon" interstitial) |
| /how-it-works | Glossary terms; D-018 concierge copy contract; tier names | `Payments:AutoReleaseHoursDefault` | Secondary link to /pricing; primary CTA to app |
| /pricing | "Free to list. Pay only when you sell."; P-14 inclusion line | `Pricing:CardTakeRatePct`, `Pricing:AchTakeRatePct`, `Pricing:WireTakeRatePct` | Primary CTA to app |
| /trust | Tier names; escrow plain-language flow; dispute *process* (NOT numeric SLA / resolution-rate — deferred per Q8 to F-MKT-003) | `Payments:WireOnlyMinAmount` (described, not numerically named — see FR-MKT-054) | Secondary link to /pricing |
| /agents | Agent posture statement; pointer copy | None | Pointers to /llms.txt, /mcp.json, /sitemap.xml, /robots.txt |

## Data Model

The marketing site is read-only and stateless except for client-side session state.

**Reads:**

```jsonc
GET /api/config/public  →  {
  "pricing": {
    "cardTakeRatePct": 5.5,
    "achTakeRatePct": 3.5,
    "wireTakeRatePct": 3.0
  },
  "payments": {
    "autoReleaseHoursDefault": 72,
    "wireOnlyMinAmount": 25000,
    "cardMaxAmount": 5000,
    "depositPercentDefault": 10
  },
  "listings": {
    "highAOVReviewThreshold": 25000,
    "newCategoryReviewWindowDays": 60,
    "minPhotos": 8,
    "maxPhotos": 30
  }
}
```

The marketing site consumes ONLY the keys it displays — at M0: `pricing.cardTakeRatePct`, `pricing.achTakeRatePct`, `pricing.wireTakeRatePct` (per FR-MKT-041, FR-MKT-042), `payments.autoReleaseHoursDefault` (per FR-MKT-033), and `payments.wireOnlyMinAmount` (described not numerically named, per FR-MKT-054). Other keys may be added as later pages ship.

**Writes:** None in this spec. (F-MKT-002 newsletter capture is the first write surface.)

**Caches:**
- `marketing.config.json` — most-recent successful snapshot of `/api/config/public`, refreshed every 24 h.
- Static assets — via CDN.
- Client-side `sessionStorage` — primary CTA label preference (per FR-MKT-002). Session-only, never persisted.

## State Transitions

N/A. The site is stateless except for the session-scoped CTA preference (FR-MKT-002), which has two states (`Browse listings` default, `Start selling` after seller-oriented interaction) with a one-way transition within a session.

## Open Questions

Capped at 3 per [`ezley-docs/10-speckit-handoff.md §8`](../../../../ezley-docs/10-speckit-handoff.md). See Clarifications §"Session 2026-05-19" above for the questions and the founder-recommended default for each.

1. ~~Marketplace-app hostname.~~ **RESOLVED 2026-05-19 by [D-023](../../../../ezley-docs/09-decisions.md).** Marketing = `ezley.com/`, marketplace = `app.ezley.com/`.
2. ~~Brand asset source.~~ **RESOLVED 2026-05-19.** Parity-plus-marketing-overlay — see NFR-MKT-013.
3. ~~Pre-launch front-door behavior for W2/W3/W4.~~ **RESOLVED 2026-05-19.** Static "coming soon" interstitial with wedge-thesis snippet, no email capture — see FR-MKT-023.

## Test Plan

### Unit / component
- Every page renders without runtime errors on a default `marketing.config.json` snapshot.
- The CTA-label switching logic (FR-MKT-002) is covered by component tests.

### Contract
- **Pricing contract test.** A test fetches `/api/config/public`, renders the /pricing page, and asserts the displayed Card / ACH / Wire percentages match `Pricing:*` config keys exactly. Fails if any rendered fee number is not traceable to the config payload. [FR-MKT-042, P-09]
- **P-14 grep test.** A test renders /pricing, grabs the rendered HTML, and asserts a zero-match grep for `"Stripe"`, `"Connect"` (in a fee-itemization context), `"concierge fee"`, `"reserve"`, `"breakdown"` in seller-facing copy. [FR-MKT-044, P-14]
- **Glossary-discipline grep test.** A test renders every public route and asserts a zero-match grep for forbidden synonyms (`"order"` in a transaction context, `"item"`, `"hold funds"`, `"charge"` as a verb for capture, `"vendor"`, `"customer"` in a buyer context, `"platform fee"`). [NFR-MKT-010, D-017]

### Integration
- **Synthetic agent crawl test.** A scripted crawl from each of `OpenAI-GPT/1.0`, `Anthropic-Claude/1.0`, `PerplexityBot/1.0` UA strings fetches every public route plus `/llms.txt`, `/mcp.json`, `/sitemap.xml`, `/robots.txt`. Asserts (a) every response is 200, (b) no Turnstile/hCaptcha/Cloudflare interstitial in the response body, (c) structured-data validators succeed, (d) sitemap parses. [FR-MKT-070..074, NFR-MKT-006]
- **Stale-config fallback test.** A test simulates `/api/config/public` returning 503, asserts the /pricing page renders the last-known-good `marketing.config.json` snapshot, asserts no error visible to the visitor. [User Story 6, FR-MKT-042]
- **No-JS baseline test.** A test loads the home page, /how-it-works, /pricing, /trust with JavaScript disabled and asserts every page is fully readable, every link is operable, and the primary CTA navigates to the correct deep-link via `<a href>`. [NFR-MKT-004]

### Accessibility
- **axe-core CI gate.** Every public route scanned on every PR. Zero critical or serious violations allowed.
- **Manual screen-reader pass.** Before each release, a manual VoiceOver pass through home → /tractors → /how-it-works → /pricing → /trust → /agents. Tester reports any heading-skip, missing-alt, or focus-trap issues; release is blocked until resolved.

### Performance
- **Lighthouse CI gate.** Every public route scanned on every PR on a simulated Slow 4G + mid-tier mobile profile. Budget: LCP ≤ 2.5 s, CLS ≤ 0.1, TBT ≤ 200 ms. [NFR-MKT-001]

### Visual regression
- Every public route screenshotted at 320 px and 1280 px viewports. Regressions surfaced for human review (not auto-blocked — visual diffs are noisy).

## Out of Scope

Verbatim from input prompt:

- Authentication / login / signup forms (live in marketplace app, per D-010)
- Listing creation forms (live in marketplace app, per D-003)
- Browse, search, and listing-detail pages (live in marketplace app — the marketing site only LINKS to a curated set, never lists)
- Buyer or seller dashboards (marketplace app)
- Checkout, escrow, payments, dispute UI (marketplace app)
- Concierge admin tooling (marketplace app)
- Native iOS / Android apps (per [`ezley-docs/06-roadmap.md §6`](../../../../ezley-docs/06-roadmap.md))
- Spanish localization v1 (per P-08 — Spanish localization plan begins M18; site must be localization-READY but does not need Spanish copy in this spec)
- Live chat or chatbot (later spec)
- Email-capture for newsletter (later spec — F-MKT-002)
- Blog / Ezley Auction Report content surface (later spec per [`ezley-docs/05-target-state.md` §"Content surfaces"](../../../../ezley-docs/05-target-state.md))
- Investor / press / careers pages (later specs, separate routes off the same site)
- Public dispute statistics page (later spec, per [`ezley-docs/05-target-state.md`](../../../../ezley-docs/05-target-state.md))
- Trust-score display (post-M9, later spec)
- MCP server itself, ACP endpoints, UCP endpoints (live in marketplace API repo; marketing site only POINTS at them via `llms.txt` and `mcp.json`)
