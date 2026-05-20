# 001-public-marketing-site — SpecKit Input Prompt

> **How this file is used.** Pass the body of this file (everything below the
> "BEGIN PROMPT" marker) to `speckit.specify` as the input. Per
> [ezley-docs/10-speckit-handoff.md §2-§4](../../../ezley-docs/10-speckit-handoff.md),
> editing this file and re-running `speckit.specify` is the canonical
> regeneration path. Do NOT hand-edit the generated `spec.md` —
> regenerate it from this input.
>
> **Source pinning.** All quoted material below is verbatim from
> `ezley-docs` `main` as of 2026-05-19. If those source documents change
> before this spec lands, regenerate from this file rather than hand-editing
> the spec.
>
> **Repo selection.** Per [ezley-docs/10-speckit-handoff.md §2](../../../ezley-docs/10-speckit-handoff.md),
> this is a UI-only surface (no new API endpoint, no event, no state
> transition in the marketplace event store). It lives in its own repo
> (`ezley-marketing-site`) because the marketing site is operationally and
> architecturally independent of the marketplace app — it has different
> uptime requirements, different SEO needs, different deployment cadence,
> and zero coupling to Auth0 / Stripe / the event store. The marketing
> site CONSUMES the marketplace's read-only public config endpoint
> (`GET /api/config/public`) at build/render time to source fee numbers, so
> the seller-facing rates here NEVER hard-code values from D-019 — they
> read `Pricing:CardTakeRatePct` / `AchTakeRatePct` / `WireTakeRatePct`.

---

> ## ⚠️ Resolutions baked in (2026-05-19) — read this before regenerating
>
> Nine clarification decisions have already been resolved against this spec
> via inline `grill-with-docs` and clarify-phase passes. **The authoritative
> resolved state lives in [`.specify/specs/001-public-marketing-site/spec.md`](../specs/001-public-marketing-site/spec.md),
> not in the BEGIN PROMPT body below.** If you re-run `speckit.specify`
> against this file, the resolutions listed here MUST be carried forward
> into the regenerated spec — do not allow regeneration to undo them.
>
> The original BEGIN PROMPT body below is preserved as history. The body
> still describes Q1–Q3 as `[NEEDS CLARIFICATION]`; those markers are now
> closed.
>
> | # | Topic | Resolution | Spec anchor |
> |---|---|---|---|
> | Q1 | Marketplace-app hostname | Marketing site canonically owns `ezley.com/`. Marketplace UI at `app.ezley.com/`. Listing URL form: `https://app.ezley.com/l/<ListingId>/<title-slug>`. Permanent 301 from `ezley.com/l/*` to `app.ezley.com/l/*`. | [D-023](../../../ezley-docs/09-decisions.md) (new ADR); [glossary §9](../../../ezley-docs/08-glossary.md) (updated); FR-MKT-004 |
> | Q2 | Brand asset source | Parity-plus-marketing-overlay: share MUI 5 color tokens + wordmark + type-scale baseline with `ezley-market-ui-react`; add marketing-only overlay (hero photography, display font, looser spacing, marketing-specific components). | NFR-MKT-013 |
> | Q3 | Pre-launch front-doors for W2/W3/W4 | Static "coming soon" interstitial with wedge-thesis snippet from vision §4. No email capture (deferred to F-MKT-002). | FR-MKT-023 |
> | Q4 | Competitor framing in "Why Ezley" | Hybrid voice. Facebook Marketplace + Craigslist named (escrow / trust-layer gap). eBay's Feb 2026 agent ban referenced in body, NOT as row headline (so it ages gracefully). TractorHouse, Machinery Pete, etc. referred to anonymously as "category-specific catalog sites without escrow". | FR-MKT-012..016, US1 acceptance 2 + 3 |
> | Q5 | Sub-brand naming and visibility | "Ezley Equipment" / "Motors" / "Gear" / "Home" surface in: (a) HTML `<title>` ("Ezley Equipment — Tractors, Combines, Implements \| Ezley"), (b) a small section-tag chip above the H1, (c) body copy. Header wordmark stays "Ezley" everywhere. | FR-MKT-020 (tightened), FR-MKT-026 (new) |
> | Q6 | Trust-anchor copy at M0 | Ezley-owned signals only ("wire payment, escrow-held funds, identity-verified sellers, concierge in your county"). NO named third-party trust anchors at M0 — no Machinery Pete, RFD-TV, Successful Farming, Farm Journal, county Farm Bureau, dealer brands, trade-press titles. Named third-party social proof deferred to F-MKT-003 and requires real signed partnerships first. | FR-MKT-019, US2 acceptance 2 |
> | Q7 | Hero trust anchor | Single mechanism: escrow / buyer-funds-held (P-06). NOT a compound bundle. Other trust mechanisms (identity tiers, concierge, dispute process) live on /trust depth page only. | FR-MKT-010a |
> | Q8 | Dispute SLA + resolution-rate claims at M0 | Defer numeric claims; describe the *process* only (defined adjudication framework, named human operator, documented steps, escalation rights). NO "< 5 business days" SLA target, NO "> 90 %" resolution-rate target, NO other aspirational dispute number until F-MKT-003 publishes measured volume. | FR-MKT-035, FR-MKT-052, FR-MKT-055 |
> | Q9 | Internal-ops debug panel on /pricing | Dropped from M0. FR-MKT-047 retired. No internal-ops debug panel ships in this spec. Future need lands in a separate spec with named consumer, proper auth gate (not query-string), explicit copy. | FR-MKT-047 retirement notice, US4 acceptance 6 retirement notice |
>
> Three additional decision documents now apply: [D-009](../../../ezley-docs/09-decisions.md) (MUI 5 — already in effect, now cited for Q2), [D-023](../../../ezley-docs/09-decisions.md) (hostname split — new in Q1), and [P-06](../../../ezley-docs/03-product-principles.md) (buyer-funds-held — now the hero trust anchor per Q7).

---

# BEGIN PROMPT

You are generating a SpecKit spec for the **Ezley public marketing site** — `ezley.com` (root + sub-brand front-door routes). This is a brand-new repo (`ezley-marketing-site`) and a brand-new spec (`001-public-marketing-site`). There is no prior 002, no existing components to extend, and no in-flight UI to preserve.

# Feature

## Feature ID — F-MKT-001 (new)

**Title**: Public marketing site for Ezley.com — what Ezley is, why it beats the alternative, and how to start

**Wedge**: All four wedges from launch. The site is horizontal by D-002 and must not be category-locked, even though W1 (farm equipment) is the only live category at M0. Category front-door routes (`/tractors`, `/cars`, `/cameras`, `/furniture`) exist from day one; non-live wedges show a "Coming soon — get notified" interstitial rather than a 404.

**Personas served (primary)**: S-FARMER, S-DEALER, S-RURAL-HOMEOWNER, S-RETIRING-FARMER, B-FARMER, B-RURAL-HOMEOWNER, B-AGENT, O-CONCIERGE (concierge uses the public site to demo Ezley to prospective sellers on a tablet in the field)

**Principles applied**: P-01 (horizontal infra, vertical GTM — copy must be category-agnostic at the brand level, category-specific at the front-door level), P-02 (trust is the product — every page surfaces the trust-first value prop), P-03 (agent-native, not agent-tolerant — site is crawlable, MCP-discoverable, no bot-gating), P-08 (mobile-first, accessible, 320 px first, WCAG 2.1 AA), P-09 (configurable, not hardcoded — fee numbers, threshold numbers, deposit windows on the pricing page MUST come from the marketplace's `/api/config/public` endpoint, never inlined), P-11 (free venue, paid trust — copy bundles this story), P-14 (bundle the rails into the value, not the price — the pricing page shows ONE bundled fee per Rail, never itemized as "Stripe + Connect + concierge + reserve")

**User story (founder framing)**: As a working farmer / rural homeowner / dealer / AI buying agent who lands on `ezley.com` for the first time, I want to understand within 30 seconds (a) what Ezley is, (b) what category of mine it serves, (c) how it protects my money on a high-value transaction, and (d) why I would choose Ezley over Facebook Marketplace, Craigslist, eBay, TractorHouse, or Machinery Pete, so that I can decide in one session whether to sign up to sell or to browse listings.

**Acceptance criteria (founder-level — SpecKit will expand to Given/When/Then)**:

1. Given a first-time visitor lands on `ezley.com`, when the home page renders, then the hero copy names Ezley as "**the open, agent-native marketplace for high-trust, high-value, peer-to-peer commerce**" (verbatim from [01-vision.md](../../../ezley-docs/01-vision.md) §"One-line vision"), with a single primary CTA ("Start selling" or "Browse listings", picked by visitor's stated role on the first interaction) and at most one secondary CTA.
2. Given the visitor scrolls past the hero, when the "Why Ezley" section renders, then it contrasts Ezley head-to-head against **Facebook Marketplace** (trustless), **Craigslist** (no transaction layer), **eBay** (banned third-party agents 2026-02-20; fee-heavy on most categories), **TractorHouse** (catalog without escrow), and **Machinery Pete** (catalog without escrow) — each contrast cites a single concrete differentiator (escrow, identity verification, agent-readiness, structured listings, or transparent bundled fee), not vague marketing language.
3. Given the visitor lands directly on a category front-door route (e.g., `ezley.com/tractors`), when the page renders, then the hero, social proof, and "Why Ezley" copy are TAILORED to that category's primary persona (S-FARMER for `/tractors`, B-RURAL-HOMEOWNER for `/cars` etc.) per [02-personas-and-jobs.md §Persona × wedge matrix](../../../ezley-docs/02-personas-and-jobs.md) — but the brand wordmark, primary navigation, and footer are IDENTICAL to the root home page (per P-01 horizontal-infra: front-door, not fork).
4. Given the visitor opens the "How it works" page, when it renders, then it walks the buyer flow (browse → offer → escrow-funded → handover → confirm-receipt → seller paid) and the seller flow (list → publish → buyer-pays-into-escrow → handover → seller-paid in ≤9 days) using EXACTLY the domain terms from [08-glossary.md](../../../ezley-docs/08-glossary.md) §1 — `Listing`, `Transaction`, `Offer`, `Escrow`, `Handover`, `Auto-release` — never synonyms ("order", "item", "hold funds").
5. Given the visitor opens the "Pricing" page, when it renders, then it shows ONE bundled Ezley fee per Rail (Card / ACH / Wire) with the values read from `GET /api/config/public` (`Pricing:CardTakeRatePct`, `Pricing:AchTakeRatePct`, `Pricing:WireTakeRatePct`) — never an itemized breakdown of Stripe + Connect + concierge + reserve. Copy explicitly says "Listing on Ezley is free. Trust services are included in one bundled fee" (per P-11 + P-14).
6. Given the visitor opens the "Trust & safety" page, when it renders, then it explains the three identity tiers (`BASIC`, `VERIFIED`, `TRUSTED_SELLER` — verbatim from [08-glossary.md §5.7](../../../ezley-docs/08-glossary.md)), the escrow state machine in plain English (`Captured → Released → Paid out`), the dispute SLA (< 5 business days from open to ruling, per [05-target-state.md](../../../ezley-docs/05-target-state.md)), and the resolution rate target (> 90 %). All numeric values that appear here also come from `/api/config/public` or from a marketing-site-local `marketing.config.json` whose source-of-truth is `ezley-docs` — never hard-coded inline.
7. Given an AI buying agent (B-AGENT persona) crawls `ezley.com`, when it does so, then (a) every page returns 200 without any Cloudflare Turnstile, hCaptcha, or generic bot-block (per P-03), (b) every page includes structured data (`schema.org/Organization` on root, `schema.org/Product` only after F-AGT-001 listing surface ships in a later spec), (c) the site exposes a discoverable `llms.txt` and `mcp.json` describing the marketplace's agent endpoints (which live in the API repo — the marketing site only POINTS at them), and (d) the sitemap is auto-generated and includes every public route.
8. Given any visitor on any device, when they load any page, then (a) the page is mobile-first at 320 px (per P-08), (b) touch targets are ≥ 48 px, (c) the page meets WCAG 2.1 AA (verifiable via automated axe-core check + manual screen-reader pass), (d) Largest Contentful Paint is ≤ 2.5 s on a simulated 3G connection on a mid-tier Android, and (e) the page is fully readable and operable without JavaScript for the home page, "How it works", "Pricing", and "Trust & safety" routes (category front-doors and CTAs may require JS for personalization).
9. Given the visitor clicks a primary CTA ("Start selling" / "Browse listings" / "Get notified when [category] launches"), when the click resolves, then it deep-links into the marketplace app (`app.ezley.com` or the equivalent host) carrying a `utm_source=marketing-site&utm_campaign=<page>&utm_content=<cta-id>` query string. The marketing site itself NEVER renders an authentication form, signup form, or listing-creation form — those live in the marketplace app per [D-003](../../../ezley-docs/09-decisions.md) (separation of repos) and [D-010](../../../ezley-docs/09-decisions.md) (Auth0 is the only auth path).
10. Given a concierge (O-CONCIERGE) demos Ezley to a prospective seller in the field on a tablet, when they open `ezley.com` offline (cached) and walk the prospective seller through the home page + "How it works" + "Pricing", then nothing on those three pages fails or shows stale data older than the most recent `/api/config/public` cache (24 h is acceptable for the marketing site; live freshness is the marketplace app's job).

**Out of scope (verbatim — SpecKit must preserve this list)**:

- Authentication / login / signup forms (live in marketplace app, per D-010)
- Listing creation forms (live in marketplace app, per D-003)
- Browse, search, and listing-detail pages (live in marketplace app — the marketing site only LINKS to a curated set, never lists)
- Buyer or seller dashboards (marketplace app)
- Checkout, escrow, payments, dispute UI (marketplace app)
- Concierge admin tooling (marketplace app)
- Native iOS / Android apps (per [06-roadmap.md §6](../../../ezley-docs/06-roadmap.md))
- Spanish localization v1 (per P-08 — Spanish localization plan begins M18; site must be localization-READY but does not need Spanish copy in this spec)
- Live chat or chatbot (later spec)
- Email-capture for newsletter (later spec — F-MKT-002)
- Blog / Ezley Auction Report content surface (later spec per [05-target-state.md §Content surfaces](../../../ezley-docs/05-target-state.md))
- Investor / press / careers pages (later specs, separate routes off the same site)
- Public dispute statistics page (later spec, per [05-target-state.md](../../../ezley-docs/05-target-state.md))
- Trust-score display (post-M9, later spec)
- MCP server itself, ACP endpoints, UCP endpoints (live in marketplace API repo; marketing site only POINTS at them via `llms.txt` and `mcp.json`)

**Dependencies**:

- READ dependency: `GET /api/config/public` from `ezley-market-net-api` for pricing values + threshold values. This endpoint ships in `004-pricing-and-gating` API spec — at M0 it returns Card/ACH/Wire rates and the `Listings:HighAOVReviewThreshold`. If the endpoint is unreachable at build/render time, the marketing site falls back to a `marketing.config.json` snapshot in the repo (snapshotted at last successful fetch). Snapshot staleness is surfaced in `/health` for ops, not in user-facing copy.
- READ dependency: `app.ezley.com` host (or whatever the marketplace UI's production hostname is — to be confirmed during `clarify` phase if it's not `app.ezley.com`).
- Brand assets (logo, color tokens, type system) — to be confirmed during `clarify` phase; assume MUI 5 token parity with `ezley-market-ui-react` until specified otherwise.

# Product principles to honor

The following principles are pasted **verbatim** from [03-product-principles.md](../../../ezley-docs/03-product-principles.md). SpecKit MUST embed them as functional or non-functional requirements where applicable, and MUST cite the principle ID inline.

## P-01 — Horizontal infrastructure, vertical go-to-market

We build like eBay; we sell like Reverb.

**What this means in practice**
- Listings are polymorphic from day one: a universal core (title, description, price, location, photos, seller, status) plus a category-specific attribute bag.
- Trust, identity, escrow, payments, dispute resolution, messaging, reviews, and search are category-agnostic.
- Brand allows category-specific front doors (`ezley.com/tractors`, `/cars`, `/cameras`) but never category-locked architecture.
- A new category is a configuration change plus a schema, not a new codebase.

**What this rules out**
- A "farm-equipment-only" branded product, even if 100 % of revenue is farm equipment in year one.
- Forking the codebase per category.
- Hard-coding category names in business logic.

## P-02 — Trust is the product

The marketplace is the trust layer; the listings are inventory the trust layer makes possible.

**What this means in practice**
- Every transaction passes through escrow by default. Cash side-deals are not blocked but are not promoted.
- Identity verification is mandatory for sellers above per-category thresholds. Verification tier is visible to buyers.
- Disputes have a defined SLA, a defined adjudication framework, and a public resolution rate.
- Reviews and ratings are real, structured, and tied to completed transactions only.
- We will accept slower growth to maintain trust scores. We will not accept growth that erodes them.

**What this rules out**
- Anonymous high-value listings.
- "Buy it now" with no escrow on transactions above a defined floor.
- Reviews from non-buyers.
- Fake or seeded transactions to inflate volume metrics.

## P-03 — Agent-native, not agent-tolerant

Agents (AI software acting on behalf of buyers or sellers) are first-class citizens of the marketplace.

**What this means in practice**
- Every endpoint is exposed via at least one of MCP, ACP, UCP, AP2 (or their successors) before being considered done.
- Listings are machine-readable in structured form, not just rendered HTML.
- Authentication supports agent delegation (a user authorizing an agent to act on their behalf).
- Agent traffic is rate-limited but never blocked except for fraud or abuse, and never solely for being agent traffic.
- Public catalog is accessible without authentication for crawl and discovery.

**What this rules out**
- Cloudflare Turnstile or generic bot-blocking on the listing browse experience.
- Authentication flows that cannot be completed by an agent on behalf of a user.
- Hidden or proprietary APIs reserved for our own UI.

## P-08 — Mobile-first, accessible, and language-aware

The buyer in a tractor cab uses their phone. The farmer in a hayfield uses their phone. The hobby buyer in their kitchen uses their phone.

**What this means in practice**
- Every screen is designed for 320 px-wide first; desktop is enhancement.
- Touch targets ≥ 48 px.
- WCAG 2.1 AA from day one, not retrofit.
- Spanish localization plan begins at month 18.

**What this rules out**
- Desktop-only admin consoles (operators work in the field).
- Inaccessible MUI overrides that pass design review but fail screen readers.

## P-09 — Configurable, not hardcoded

Numeric thresholds, percentages, windows, and fee structures live in configuration, not in code.

**What this means in practice**
- All payment thresholds, deposit percentages, auto-release windows, fee percentages, photo minimums, and category-specific caps are read from `appsettings` (server) or a config endpoint (client).
- A new transaction snapshots the configuration values that applied to it; in-flight transactions do not change behavior when configuration is reloaded.
- Configuration changes are logged.

**What this rules out**
- Hard-coded "$10,000 wire threshold" in the wire-flow logic.
- "We'll just deploy a new build" as a way to change a fee.

## P-11 — Free venue, paid trust

Listing on Ezley is free. Trust services (escrow, identity verification, dispute adjudication, concierge support, agent-readiness) are paid.

**What this means in practice**
- No listing fees in any wedge. Ever.
- Take rate is a percentage of completed transactions, paid out of seller proceeds at payout time.
- Premium services (concierge, white-glove dispute resolution, dealer bulk tools, identity tier upgrades) are paid add-ons.
- Take rate varies by category but stays within a published range (2.5–3.5 %).

**What this rules out**
- Per-listing fees, even small ones.
- Subscription fees for sellers as a primary revenue model.
- Hidden fees that are not surfaced before transaction commit.

## P-14 — Bundle the rails into the value, not the price

Sellers see one Ezley fee. Stripe processing, Stripe Connect, concierge support, escrow, dispute protection, and reserve allocations are **costs of being Ezley** — not line items on a seller's invoice. The bundled rate is the lever; the costs underneath flex as Stripe rates change, concierge automates, and reserve actuals settle. Itemized fee breakdowns are corrosive to long-term pricing power: every line becomes a target for negotiation, sellers describe Ezley to peers using the worst-sounding number, and competitors get free ammunition to brand themselves as "flat 3 % — no extras" even when their all-in cost matches.

**What this means in practice**
- Seller-facing surfaces (invoices, dashboards, marketing pages, FAQ, support replies) display a single "Ezley fee" line at the bundled rate (3.0 % wire / 3.5 % ACH / 5.5 % card per D-019).
- Stripe and Stripe Connect fees are never broken out as separate seller-visible line items, even though Stripe Managed Payments would technically allow it.
- Concierge cost is not a separate fee. It is part of being on Ezley.
- Buyer-facing copy at checkout shows a single total, with optional "includes Ezley protection" disclosure.
- A "Fee details" expansion is available on demand for sellers who ask. It is factual and brief. It is never the default view and never volunteered.
- Internal financial reporting, investor decks, unit-economics work, and tax/regulatory disclosures itemize fully — different audiences, different framing.
- Marketing copy describes what the fee buys ("escrow protection, dispute resolution, payment processing, and concierge support — all included"), not the cost composition.

**What this rules out**
- Invoices that show "Stripe fee: $X | Connect fee: $Y | Ezley fee: $Z | Concierge fee: $W."
- Marketing pages that brag about "low platform fee" while burying processing cost as a separate disclosure.
- Support copy that explains a deduction as "that's Stripe's fee, not ours."
- A future cost increase under the hood (e.g., Stripe rate hike) being passed through as a *new* line item rather than absorbed in margin or rolled into the bundled rate.

# Personas referenced

Pasted **verbatim** from [02-personas-and-jobs.md](../../../ezley-docs/02-personas-and-jobs.md). The marketing site must speak in these personas' language, never invent new ones.

## S-FARMER — Working farmer

**Who**: Owns or operates a farm of 200–5,000 acres. Likely 45–70 years old. Typically owns equipment outright, replaces every 7–15 years. Knows their machines intimately.

**Tech profile**: Smartphone, mostly text and voice. Uses Facebook regularly, YouTube weekly. Skeptical of new platforms but willing to try when a trusted neighbor or magazine vouches for one. Will _not_ download a new app for a one-time transaction; will use a mobile web browser.

**Where they sell today**: Local newspaper classifieds, regional auctioneers (Steffes, Sullivan, Big Iron), Machinery Pete listings, Facebook groups, word of mouth at the co-op or feed store.

**Top jobs (in order of frequency)**
1. Sell a piece of equipment they have outgrown or are upgrading away from.
2. Find a buyer who will pay fair market value without excessive haggling.
3. Avoid wasting time on tire-kickers who never show up.
4. Get paid in a way that doesn't expose them to fraud.

**Hates / pain points**
- Being asked to ship heavy equipment.
- Getting "is this still available?" texts at 11 pm.
- Out-of-state buyers who don't show up after agreeing to come.
- Getting low-balled by dealers who know they're motivated.
- Filling out long forms.

**Trust anchors**
- Machinery Pete, RFD-TV, Successful Farming, Farm Journal
- Their county Farm Bureau chapter
- Their local equipment dealer
- Other farmers in their county

**Triggers to use Ezley**
- A trusted neighbor used Ezley successfully.
- Their dealer suggested it (concierge channel).
- A printed flyer in the dealer parts department, the feed store, or the co-op.
- A YouTube video from a creator they follow.

## S-DEALER — Independent equipment dealer

**Who**: Small to mid-size dealer (1–4 locations). Carries used equipment as inventory.

**Top jobs**
1. Move stale inventory at a price floor.
2. Reach buyers outside their immediate geography without spending on AdWords.
3. Offload trade-ins quickly.

**Hates / pain points**
- Listing fees that don't convert.
- Platforms that put consumer listings ahead of dealer listings.
- Photo upload friction for 50+ pieces of inventory.

## S-RURAL-HOMEOWNER — Hobby farmer / rural homeowner / property maintainer

**Who**: Owns 5–50 acres, primarily for residential or recreational use. 35–65 years old. Online-native. iPhone or Android. Active on Facebook, Instagram, YouTube, and possibly TikTok.

**Top jobs**
1. Sell equipment they outgrew or upgraded.
2. Avoid the time and risk of in-person strangers.
3. Get paid promptly and securely.

## B-FARMER — Working farmer (buyer side)

Mirror of S-FARMER. The vast majority of working farmers are both buyers and sellers in the same calendar year (upgrade cycle).

**Top jobs**
1. Find a specific piece of equipment that fits their operation (model + year + condition + price).
2. Verify the item is what the seller claims before committing.
3. Inspect in person or via a trusted intermediary before paying.
4. Pay safely on a high-value transaction.

## B-RURAL-HOMEOWNER — Buying side of rural homeowner

May be a first-time equipment buyer who just bought property. Often does not know what they need beyond "a tractor."

**Top jobs**
1. Find a starter piece of equipment in their budget.
2. Learn enough to know they're not getting ripped off.
3. Pay safely.
4. Arrange transport / pickup.

**Triggers to buy on Ezley**
- AI-assisted "help me buy a tractor" experience.
- Recommended listings inside YouTube videos they're already watching.
- Ezley appears in agentic queries ("find me a 30 hp tractor near 50047 under $15 K").

## B-AGENT — AI agent acting on behalf of a buyer

Not a person. A software agent (ChatGPT shopping, Perplexity, Claude, Gemini, a bespoke buying agent) operating on behalf of one of the human buyer personas above.

**Tech profile**: Speaks MCP, ACP, UCP, AP2. Calls the API directly without rendering UI.

**Top jobs**
1. Find listings that match a structured query.
2. Get enough machine-readable detail to evaluate match without a human in the loop.
3. Initiate purchase / hold / counter-offer in one of the standard protocols.
4. Pay through a standard agent-payment protocol.

**Hates / pain points**
- Listings without structured specs.
- Marketplaces that block agent traffic.
- Inconsistent or missing protocol implementations.

## O-CONCIERGE — Ezley concierge operator

Uses the marketing site to demo Ezley to a prospective seller on a tablet in the field. The marketing site is not their primary tool — that lives in the marketplace app — but they are a regular reader and must never be embarrassed by stale or marketing-vague claims.

# Glossary terms in scope

Pasted **verbatim** from [08-glossary.md](../../../ezley-docs/08-glossary.md). The marketing site MUST use these terms exactly and MUST NOT introduce synonyms.

## Core domain terms

- **Listing** — A single item or lot offered for sale by a Seller. Has a category, polymorphic spec, photos, price, and status.
- **Transaction** — The lifecycle of a single buy event from offer-acceptance to escrow release.
- **Offer** — A buyer's proposed price for a listing.
- **Escrow** — The holding of buyer funds until handover is confirmed and the dispute window closes. Ezley is the escrow operator (via Stripe Connect).
- **Handover** — The physical transfer of the item from seller to buyer.
- **Wedge** — A category Ezley intentionally focuses on for a defined window (W1, W2, W3, W4).
- **Concierge** — An Ezley operator who shepherds transactions in early markets where automation is not yet sufficient.
- **Auto-release** — The automated transition of a Transaction's escrow from `EscrowState = Captured` to `EscrowState = Released` after the handover confirmation + the configured window has elapsed without a dispute being opened.
- **Trust seal** — UI badge(s) indicating a listing or seller meets a verification bar. The marketing site explains the seal; it does NOT render it (that's the marketplace app's component).

## Verification tiers (`VerificationTier`)

- `BASIC` — email-verified only
- `VERIFIED` — email + phone + government ID
- `TRUSTED_SELLER` — all of the above + address + transaction history

## Rails (`Rail`)

- `Card` — credit/debit card (Stripe)
- `ACH` — ACH bank transfer
- `Wire` — wire transfer

## Names not to use (per glossary §10)

The marketing site MUST NOT use any of:
- "Order" (use "Transaction")
- "Seller account" (use "Connect account" — though this term is rarely surfaced publicly)
- "Hold funds" (use "Escrow")
- "Charge" (use "Capture" — though the public site rarely surfaces this verb)
- "Vendor" (use "Seller")
- "Item" (use "Listing")
- "Customer" (use "Buyer")
- "Platform fee" or any phrasing implying an itemized rails breakdown (use "Ezley fee" — bundled per P-14)

# Decisions in effect

Pasted **verbatim** from [09-decisions.md](../../../ezley-docs/09-decisions.md). SpecKit must cite the D-XXX ID anywhere a constraint enforces a decision.

## D-001 — Domain: Ezley.com

**Decision**: Use **Ezley.com** as the consumer-facing brand. Hold .ai/.app for product-internal subdomains if useful.

**Consequences for this spec**: The marketing site is hosted at `ezley.com` (root). Category front-doors are sub-paths (`ezley.com/tractors`, `/cars`, `/cameras`, `/furniture`), not sub-domains. The marketplace app is at a sub-domain (`app.ezley.com` — to be confirmed during `clarify`).

## D-002 — Marketplace shape: broad horizontal, wedge-launched

**Decision**: Build polymorphic listing schema, generic escrow, generic agent platform. Launch with farm equipment as W1.

**Consequences for this spec**: Marketing copy is BRAND-LEVEL horizontal — Ezley is "a marketplace for high-value used goods", not "a marketplace for farm equipment". The W1 emphasis at M0 is expressed THROUGH the `/tractors` front-door and through homepage social proof (which initially features farm-equipment transactions because that's what's live), NOT through the root brand statement.

## D-016 — Free venue, paid trust + paid distribution

**Decision**: Listings are free. Buyer pays escrow fee (% of transaction). Verification, escrow, and agent placement are paid. Premier Agent (paid placement) post-M15.

**Consequences for this spec**: The pricing page leads with "Free to list. Pay only when you sell." Premier Agent and dealer bulk tools are NOT marketed in this spec (they're post-M15).

## D-019 — Take-rate structure: tiered by payment path, single bundled rate

**Decision**: Card 5.5 %, ACH 3.5 %, Wire 3.0 %, all bundled (never itemized), all configurable via `Pricing:CardTakeRatePct` / `AchTakeRatePct` / `WireTakeRatePct`.

**Consequences for this spec**: The pricing page renders THREE rate cards (Card / ACH / Wire), each showing one bundled rate sourced from the public config endpoint, NEVER hard-coded inline. Copy explicitly enumerates what's included ("escrow protection, dispute resolution, payment processing, and concierge support — all included") and never lists Stripe / Connect / concierge / reserve as separate lines.

## D-014 — Spec depth: PM-style (what + why), SpecKit fills how

**Decision**: Specs describe behavior and rationale; implementation choices (framework, library, build tool, hosting) are SpecKit's job during the `plan` phase, not the `specify` phase.

**Consequences for this spec**: SpecKit should NOT pick a tech stack in the spec output. The spec describes WHAT the marketing site does and WHY; the `plan.md` phase picks WHETHER it's Next.js / Astro / Gatsby / a static-site generator / a server-rendered .NET site. (Founder's working hypothesis is Astro or Next.js for SSG-with-islands, but this is NOT a spec constraint.)

# Roadmap context

This feature targets milestone **M0** (per [06-roadmap.md §3](../../../ezley-docs/06-roadmap.md)) — same window as the W1 closed-beta. The site must be live before the first concierge-curated W1 transaction so the concierge has a working `ezley.com` URL to point sellers and buyers at.

**Sprint context**: This is a Sprint-7-equivalent deliverable (after Sprints 0–6 close out the marketplace's `002` + `004` + `005` work — see [06-roadmap.md §2](../../../ezley-docs/06-roadmap.md)). It does not need to ship before Sprint 6 closed-beta launch, but it must ship before M3 (the first 10 W1 transactions) so the marketing surface exists when public outreach begins.

It also unblocks a roadmap item already on the books: per [05-target-state.md §Public discovery](../../../ezley-docs/05-target-state.md) the platform must have "agent surface: documented MCP/ACP/UCP/AP2 endpoints with public docs site". This marketing site is the host for the public-facing pointer at those endpoints (the endpoints themselves live in the API repo).

# Output requirements

Produce a SpecKit spec with the following sections:
1. Overview
2. User stories (Given/When/Then for each acceptance criterion, prioritized P1 / P2 / P3)
3. Functional requirements (FR-MKT-001..N), grouped by page (Home, Why Ezley, How it works, Pricing, Trust & safety, Category front-doors, Agent-readiness surface, Site-wide chrome)
4. Non-functional requirements (NFR-MKT-001..N) — performance budget (LCP ≤ 2.5 s on 3G mid-tier Android), accessibility (WCAG 2.1 AA, axe-core in CI), SEO (sitemap, structured data, no bot-blocking), agent-readiness (`llms.txt`, `mcp.json` pointer at marketplace API), uptime target (99.9 % — lower than the marketplace's 99.95 % buyer-facing target because the marketing site is not on the transaction path), CDN caching strategy
5. Content contract — for every page, list the copy strings that come from this spec (verbatim quotes from `ezley-docs`), the values that come from `/api/config/public` at render time, and the CTA targets (deep-links into `app.ezley.com` with UTM params)
6. Data model — what the marketing site reads (config endpoint shape — just the keys it consumes), what it writes (nothing in this spec — F-MKT-002 newsletter capture is out of scope), what it caches (config snapshot, image assets via CDN)
7. State transitions — N/A; the marketing site is stateless except for theme preference (light/dark, if applicable — to be clarified) and language preference (English-only in this spec; localization-ready)
8. Open questions — listed as `[NEEDS CLARIFICATION]` markers, capped at 3 per [10-speckit-handoff.md §8](../../../ezley-docs/10-speckit-handoff.md). Founder seed for `clarify` phase: (a) marketplace-app hostname (`app.ezley.com` vs other), (b) brand asset source (token parity with `ezley-market-ui-react` vs new design), (c) whether the `/cars`, `/cameras`, `/furniture` front-doors should render a "coming soon" interstitial or a 410 Gone, given W2/W3/W4 are post-M0 — recommended default: interstitial with email capture deferred to F-MKT-002.
9. Test plan — visual regression on every page at 320 px and 1280 px viewports, axe-core a11y CI gate, Lighthouse perf gate (LCP / CLS / TBT thresholds), a synthetic crawl that asserts (a) no Cloudflare gating returns to a known agent UA string, (b) structured data validates, (c) sitemap parses, and (d) every CTA target returns a 200 (or a documented expected redirect) when fetched. Contract test on the pricing page that asserts the rendered fee numbers match the `/api/config/public` payload at render time.
10. Out of scope (verbatim from above)

Use exactly the names and enums from the glossary. Do not invent synonyms.
Where a principle constrains an implementation choice, cite the principle ID.
Where a decision constrains a choice, cite the D-XXX ID.
Do NOT pick a tech stack — that is `plan.md`'s job per D-014.

# END PROMPT
