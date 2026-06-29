# Feature Specification: Fee-Model Reconciliation — Rewrite the Public Pricing Story to D-040

**Feature Branch**: `002-fee-model-reconciliation`
**Feature ID**: F-MKT-004 (proposed)
**Created**: 2026-06-28
**Status**: Draft — `analyze` fixups applied; `tasks.md` generated. `implement` to follow.
**Input**: Founder directive (2026-06-28) to rewrite the public pricing story to **Decision D-040** (ticket-size seller fee + buyer protection fee; rail-based pricing abolished; verification no longer affects price).
**Source pinning**: **D-040 (Accepted 2026-06-28), committed to `ezley-docs/09-decisions.md` on `origin/main`.** Its sibling decisions D-038 and D-039 are committed alongside it. The locked values below trace to the committed D-040 entry and are authoritative for this spec.

## Canonical location

This file is the canonical spec for the marketing-site fee-model rewrite. It is owned by the `ezley-marketing-site` repo. It has **no companion API spec in this repo**, but it has a hard **cross-repo data-contract dependency** on `ezley-market-net-api`'s `GET /api/config/public` (see FR-FEE-020). The site only CONSUMES that endpoint; it owns no server-side write surface.

This feature **supersedes the pricing presentation** established in `001-public-marketing-site` (which implemented the now-WRONG rail-tiered model per D-019). Wherever this spec and `001` conflict on fees, this spec wins. The `001` requirement IDs that are reversed are called out inline (e.g. "reverses FR-MKT-041").

## What changed and why

The site today tells a **rail-tiered, seller-only** fee story that is now wrong on two counts:

1. **OLD / live on the site (WRONG):** one bundled fee per **payment path** — *Wire 3.0%, ACH 3.5%, Card 5.5%* — and the claim that **"Buyers do not pay Ezley directly."** Both come from D-019 and are rendered on `/pricing`, the home pricing teaser, `terms.md §8`, and `llms.txt`.
2. **NEW (D-040):** the seller fee is tiered by **ticket size**, not rail; a **buyer protection fee** is added on escrow sales; and **verification tier no longer changes price**.

There is also a **pre-existing drift** the new model resolves: the site advertises **Card 5.5%** while the API's rail schedule had **Card Basic 8.5%**. D-040 abolishes rail rates entirely, so the drift disappears — but the new ticket-size numbers MUST match D-040 and the API exactly, key-for-key (FR-FEE-020).

## The locked model (D-040)

> These are the authoritative values for this spec. Every rendered number traces here and is sourced from config at build time (P-09) — never inlined into a template or copy string.

**Canonical numbers to publish (match D-040 exactly).** Seller fee **12%** (minimum **$5**) / **8.5%** / **7%** / **5%** by ticket size; **buyer protection fee 2.5%, capped at $2,500**, on escrow sales (**$200 and up**); **Lite** under $200 (no escrow, no buyer fee); **free to list**; **$20 listing minimum**; **verification price-neutral**. No public surface may display any number that differs from this set.

### Seller fee — by ticket size (replaces rail tiers)

| Ticket size (sale price) | Seller fee | Floor |
|---|---|---|
| Under $200 | **12%** | minimum **$5** |
| $200 – $50,000 | **8.5%** | — |
| Over $50,000 – $150,000 | **7%** | — |
| Over $150,000 | **5%** | — |

**Boundary convention (Q2 — ratified by Tech Lead).** The bottom band is **"under $200" (exclusive)**; every higher boundary is **inclusive**. The four bands are **non-overlapping and exhaustive**:

- **12%** — under $200
- **8.5%** — $200 to $50,000 (inclusive both ends)
- **7%** — over $50,000 to $150,000 (inclusive at $150,000)
- **5%** — over $150,000

So **exactly $200 → 8.5%**, **exactly $50,000 → 8.5%**, **exactly $150,000 → 7%**. Worked examples MUST follow this (e.g. a **$50,000 sale = 8.5%**).

- **Free to list.** No insertion fee, no subscription.
- **$20 listing minimum** (a listing's price floor; the lowest sale price Ezley supports).
- **Verification tier no longer changes price.** (Reverses D-024-style tier pricing on the seller side; the seller's rate is determined by ticket size alone.)

### Buyer protection fee — on escrow sales only

- **2.5%** of the sale price, **capped at $2,500**.
- Applies **only on escrow sales ($200 and up)**. **Lite** sales (under $200, no escrow) carry **no** buyer protection fee.

### Escrow vs. Lite split

- **Lite** (no escrow): under **$200**.
- **Escrow**: **$200 and up**.

### Presentation — MODEL A (the locked presentation choice)

- The **seller** sees **their ticket-size rate** (one clean number).
- The **buyer** sees the **protection fee** (one clean number).
- Each party sees **one clean number**, kept in **separate sections**.
- **Do NOT merge** the seller fee and buyer protection fee into a single "all-in" number anywhere on any page.

## ⚠️ LEGAL FLAG — `terms.md §8` is gated on legal review (highest-priority risk)

> **This is the single most consequential item in this spec. Read it before scoping any work.**

`terms.md §8 (Fees)` currently states, as binding contract language:

> *"Ezley charges a single bundled fee per Transaction, varying by payment path… Card 5.5% / ACH 3.5% / Wire 3.0%… **Buyers do not pay Ezley directly**; the Seller's net payout is the gross less the bundled Ezley fee. … Verification tier upgrades are free."*

D-040 **reverses two binding promises** in that clause:

1. **Buyers now pay a fee.** The "Buyers do not pay Ezley directly" sentence becomes false the moment the 2.5% buyer protection fee ships. This is a **change in who is charged**, not a rate tweak — it is a material change to the buyer's contractual obligations.
2. **The fee basis changes** from payment-path tiers to ticket-size tiers (plus the new buyer fee).

Because of this, the §8 rewrite is **gated and decoupled** from the rest of the marketing change:

- **R-LEGAL-1 — Attorney review required before publish.** The §8 rewrite text MUST be reviewed and approved by counsel before it goes live. It MUST NOT be published as part of the marketing-copy batch.
- **R-LEGAL-2 — `effective_date` bump.** `terms.md` front-matter `effective_date` (currently `2026-06-27`) MUST be bumped to the date the new fee terms take effect.
- **R-LEGAL-3 — §15 modification-notice check.** `terms.md §15 (Modifications)` states: *"Material changes will be announced **at least 30 days before they take effect**, by in-app notification and email."* Introducing a buyer-facing fee is almost certainly a **material change**, so the **30-day advance-notice clock** likely applies. Counsel MUST confirm whether the 30-day notice is required, and if so, the buyer protection fee's marketing go-live and the §8 effective date MUST respect that window. This may force a **two-phase publish** (see "Release phasing" below).
- The non-§8 marketing surfaces (pricing page, teaser, how-it-works, llms.txt, README, config) are **NOT** blocked on legal — but the **buyer protection fee** must not be advertised on any of them ahead of whatever notice window §15 requires (FR-FEE-013).

`privacy.md` is a **secondary legal surface**: it already describes buyers funding escrow via licensed partners and does not assert "buyers don't pay Ezley," so it needs only a light check that nothing there contradicts a buyer-paid protection fee (FR-FEE-014). It is **not** on the §8 critical path.

## Alignment with project rules

Follows the SpecKit handoff workflow in `ezley-docs/10-speckit-handoff.md`, the canonical vocabulary in `ezley-docs/08-glossary.md`, and the principles in `ezley-docs/03-product-principles.md`. Tech stack is unchanged from `001` (Jekyll + GitHub Pages); see `plan.md`. Per P-09, every fee/threshold renders from `site.data.config` (sourced from `/api/config/public`), never inline.

## Decision context (motivating documents)

| ID | Title | Effect on this spec |
|---|---|---|
| D-040 | Fee model: ticket-size seller fee + buyer protection fee; rails abolished; verification price-neutral | The entire spec. Locked values above. **Committed (Accepted 2026-06-28) to `ezley-docs/09-decisions.md` on `origin/main`.** |
| D-038 / D-039 | Premium-band take-rate; four-rate schedule + Lite tier + buyer-paid protection fee | Strategic parents of D-040; both committed alongside D-040. |
| D-019 | Take-rate by payment path, single bundled rate | **Superseded for pricing by D-040.** Rail tiers and the "one bundled fee per path" framing are removed. |
| D-016 / P-11 | Free venue, paid trust + paid distribution | Retained — "Free to list. Pay only when you sell." survives; only the *shape* of the fee changes. |
| D-024 | Tier card/ACH/wire by VerificationTier | **Reversed for pricing** — verification no longer changes price. |
| P-02 | Trust is the product | The buyer protection fee must be framed as *what the buyer gets* (escrow + protection), not an extraction. |
| P-09 | Configurable, not hardcoded | Every D-040 number comes from config; zero inline fee constants. |
| P-14 | Bundle the rails into the value, not the price | Retained in spirit: each party still sees ONE clean number (no Stripe/Connect/concierge itemization). MODEL A keeps seller and buyer numbers in **separate** sections — it does not bundle them together. |

## User Scenarios & Testing

### User Story 1 — Seller learns their fee by ticket size (P1)

**As** a prospective seller (S-RETIRING-FARMER, S-DEALER) pricing a high-value item,
**I want** to see exactly what Ezley's fee will be for *my* sale size,
**So that** I can decide whether to list and what to net.

**Acceptance**
- **Given** a visitor on `/pricing`, **when** the seller section renders, **then** it shows the four ticket-size bands (Under $200 → 12%/$5 min; $200–$50K → 8.5%; $50K–$150K → 7%; Over $150K → 5%) with every percentage and threshold sourced from config.
- **Given** the seller section, **then** it shows **one clean seller number** per band and **never** itemizes Stripe / Connect / concierge / reserve.
- **Given** the seller section, **then** it states "Free to list" and the **$20 listing minimum**.
- **Given** the page, **then** **no rail words** (Wire / ACH / Card) appear as *fee tiers* anywhere in seller-facing fee copy. (Rails may still be mentioned where they describe *payment mechanics* — e.g. "above $X, card isn't offered" — but never as a fee rate.)
- **Given** the page, **then** **no claim that price depends on verification tier** appears.

### User Story 2 — Buyer understands the protection fee (P1)

**As** a prospective buyer about to wire real money to a stranger,
**I want** to understand the protection fee I pay and what it buys,
**So that** I see the fee as the price of safety, not a surprise.

**Acceptance**
- **Given** `/pricing`, **when** the buyer section renders, **then** it shows the **2.5% buyer protection fee, capped at $2,500**, in a **section separate** from the seller fee, with the rate and cap from config.
- **Given** the buyer section, **then** it states the fee applies **only on escrow sales ($200+)** and that **Lite sales (under $200) have no buyer protection fee**.
- **Given** the buyer section, **then** the fee is framed as buying escrow + dispute protection (P-02), not as an extraction.
- **Given** the whole page, **then** the seller fee and the buyer protection fee are **NEVER combined into one "all-in" number** (MODEL A).

### User Story 3 — Escrow vs. Lite is legible (P2)

**As** a visitor selling or buying a low-value item,
**I want** to know when escrow applies and when it doesn't,
**So that** the $200 boundary and its consequences are clear.

**Acceptance**
- **Given** any page that explains the transaction (`/pricing`, `/how-it-works`), **then** the **$200** boundary is stated, sourced from config, with **Lite = under $200 (no escrow, no buyer protection fee)** and **Escrow = $200 and up**.
- **Given** `/how-it-works`, **then** the buyer/seller flow copy is consistent with the Lite/escrow split (a Lite sale has no escrow step; an escrow sale does).

### User Story 4 — Config and the API agree, key-for-key (P1)

**As** the operator,
**I want** the site's `_data/config.yml` to mirror the API's `/api/config/public` D-040 shape exactly,
**So that** the public fee story can never drift from what the system charges.

**Acceptance**
- **Given** `_data/config.yml`, **then** its **displayed** D-040 numbers (band rates, $5 floor, 2.5% buyer fee, $2,500 cap, $20 listing minimum, $200 escrow boundary) equal the canonical D-040 set value-for-value. The site config is **hand-authored for display and uses dollars** (not the API's cents keys); it does **not** need to mirror the API's exact key names unless/until a `scripts/fetch-config.sh` fetch script actually exists (Q1, FR-FEE-020).
- **Given** a future `scripts/fetch-config.sh`, **when** it exists and fetches, **then** the exact API key names (Q1) and unit conversion become load-bearing; until then the static dollar numbers do **not** block on the API contract.
- **Given** any rendered page, **then** **zero** D-040 fee numbers are hard-coded in a template or copy file (P-09 grep test, FR-FEE-021).

### User Story 5 — Legal-gated terms rewrite (P1, gated)

**As** the operator,
**I want** the `terms.md §8` rewrite handled as a legal deliverable separate from marketing,
**So that** we don't publish binding fee language that contradicts D-040 or violates the §15 notice clause.

**Acceptance**
- **Given** the §8 rewrite, **then** it is **not** merged/published until counsel approves (R-LEGAL-1).
- **Given** the §8 rewrite, **then** `effective_date` is bumped (R-LEGAL-2) and the §15 30-day-notice question is resolved in writing (R-LEGAL-3).
- **Given** the buyer protection fee, **then** it is **not** advertised on any public surface ahead of whatever notice window §15 requires (FR-FEE-013).

## Requirements

### Functional Requirements — `/pricing` (seller section)

- **FR-FEE-001** — `/pricing` renders **four ticket-size bands** (Under $200 / $200–$50K / $50K–$150K / Over $150K) with rates **12% / 8.5% / 7% / 5%** and the **$5 minimum** on the under-$200 band, all from config. *(Reverses FR-MKT-041..043 rail cards.)*
- **FR-FEE-002** — The seller section states **"Free to list"** and the **$20 listing minimum**, both from config where the minimum is a config value.
- **FR-FEE-003** — Worked examples (if retained) compute the seller fee **live from config** and use the **ratified boundary convention** (Q2): a **$50,000 sale = 8.5%** (not 7%), a sale of exactly $150,000 = 7%, a $100 sale hits the $5 floor. Amounts chosen for clean arithmetic. *(Adapts FR-MKT-045.)*
- **FR-FEE-004** — The seller section shows **one clean number** per band; **no** Stripe / Connect / "concierge fee" / reserve / itemized-breakdown language (P-14 grep test retained from FR-MKT-044).
- **FR-FEE-005** — **No rail (Wire/ACH/Card) appears as a fee tier** in seller fee copy. Rail mentions are permitted only as payment *mechanics*, never as a rate.
- **FR-FEE-006** — **No verification-tier-affects-price** statement appears anywhere.

### Functional Requirements — `/pricing` (buyer section)

- **FR-FEE-010** — `/pricing` renders a **buyer protection fee** section, **separate** from the seller section, showing **2.5% capped at $2,500**, from config.
- **FR-FEE-011** — The buyer section states the fee applies **only on escrow sales ($200+)** and that **Lite (under $200) has none**.
- **FR-FEE-012** — Seller and buyer numbers are **never merged** into one all-in figure (MODEL A enforcement; add a grep/lint check for an "all-in" anti-pattern if practical).
- **FR-FEE-013** — The buyer protection fee MUST NOT be advertised on any public surface ahead of the §15 notice window, if §15 applies (gated by R-LEGAL-3). A feature flag / config switch SHOULD gate the buyer-fee copy so marketing can ship the seller-side rewrite first and reveal the buyer fee on the effective date.

### Functional Requirements — config + cross-repo contract

- **FR-FEE-020** — `_data/config.yml` is **hand-authored for display in dollars**. Its **displayed D-040 numbers MUST equal the canonical set** (band rates 12/8.5/7/5, $5 floor, 2.5% buyer fee, $2,500 cap, $20 listing minimum, $200 escrow boundary). It does **not** need to literally mirror the API's cents-based key names; key-for-key parity with `/api/config/public` is required **only if and when** `scripts/fetch-config.sh` actually exists and fetches (Q1). The current rail keys (`pricing.cardTakeRatePct`, `pricing.achTakeRatePct`, `pricing.wireTakeRatePct`) and the `payments.cardMaxAmount` / `payments.wireOnlyMinAmount` rail thresholds are **removed or replaced** by the D-040 display shape regardless.
- **FR-FEE-021** — **Zero inline fee constants.** A build-time grep asserts no D-040 percentage or dollar threshold appears as a literal in any `_pages/*` or copy file (P-09); every rendered number resolves from `_data/config.yml`.
- **FR-FEE-022** — *(deferred — only if a fetch script exists)* If `scripts/fetch-config.sh` exists, it validates the D-040 keys it fetches and fails the build on a missing required key (so the site can never silently render a stale rail rate). The static dollar display config does **not** depend on this script; absent the script, the committed `_data/config.yml` is the source of the displayed numbers.

### Functional Requirements — other marketing surfaces

- **FR-FEE-030** — **Home pricing teaser** (`index.html`, FR-MKT-018): the rail line ("Wire X%. ACH Y%. Card Z%.") is **removed** and replaced with a D-040-true teaser (e.g. "Free to list. Fees scale with sale size. Buyers get escrow protection." — exact copy in `_data/copy/en-US.yml`, no inline numbers, or config-sourced if a number is shown).
- **FR-FEE-031** — **`/how-it-works`**: buyer/seller flow copy reconciled with Lite vs. escrow ($200 boundary from config). The auto-release / escrow steps apply to escrow sales; Lite sales skip the escrow step. No rail-rate language.
- **FR-FEE-032** — **`llms.txt`**: the line *"Pricing: Bundled fee per payment path (Wire / ACH / Card). Free to list."* is rewritten to the D-040 model ("seller fee by sale size + buyer protection fee on escrow sales; free to list"). The `/api/config/public` description line is updated to reflect D-040 keys.
- **FR-FEE-033** — **`README.md`**: the line *"Pricing — path-tiered, single bundled rate (per D-019)"* is updated to cite **D-040** and the ticket-size + buyer-fee model.
- **FR-FEE-034** — **`/is-it-safe/`** (secondary): review for consistency with the $200 escrow boundary and the buyer protection fee. Its "wire/ACH above threshold, no chargeback" and escrow-threshold framing must not contradict the Lite/escrow split or imply buyers pay nothing. No rate numbers are added unless config-sourced.
- **FR-FEE-035** — **`_data/copy/en-US.yml`**: the `pricing.*`, `home.pricing_teaser`, and any rail-referencing copy blocks are rewritten; new copy keys for the buyer protection section and ticket-size bands are added. Copy frames numbers; numbers come from config.

### Functional Requirements — legal (gated)

- **FR-FEE-040** — `terms.md §8 (Fees)` is rewritten to the D-040 model: ticket-size seller fee, $20 listing minimum, **buyer protection fee (2.5% capped $2,500) on escrow sales $200+**, verification-price-neutral. The "Buyers do not pay Ezley directly" sentence is **removed/replaced**. The mixed-rail sentence ("each capture event is priced at its own rail") is removed (rails gone).
- **FR-FEE-014** — `privacy.md` is checked for consistency with a buyer-paid protection fee — **light review only**, no edit required unless something there contradicts the buyer fee. `privacy.md` already describes buyer-funded escrow via licensed partners and is **not** on the §8 critical path. *(This is the single privacy-check requirement; the former duplicate FR-FEE-042 is collapsed into this lower-numbered id.)*
- **FR-FEE-041** — §8 is **not published** without counsel sign-off (R-LEGAL-1); `effective_date` bumped (R-LEGAL-2); §15 30-day-notice resolved (R-LEGAL-3).

### Non-Functional Requirements

- **NFR-FEE-001** — No accessibility regression: the pricing page keeps WCAG 2.1 AA, 48px targets, 320px-first (carry-forward of `001` NFRs).
- **NFR-FEE-002** — No-JS baseline preserved; numbers render server-side via Liquid from config.
- **NFR-FEE-003** — The seller and buyer sections are visually and structurally distinct (MODEL A) and individually legible on a 320px phone.

## Release phasing (driven by the legal gate)

Two tracks, sequenced by R-LEGAL-3:

1. **Track A — Marketing rewrite (not legally gated, ships first):** seller ticket-size bands, config swap, home teaser, how-it-works, llms.txt, README, is-it-safe consistency, copy. **The buyer protection fee copy is gated behind a config flag (FR-FEE-013) and stays dark** until the effective date.
2. **Track B — Legal (gated):** §8 rewrite + counsel review + `effective_date` bump + §15 notice resolution. The buyer protection fee reveal (config flag flip) and the §8 effective date are aligned to whatever advance-notice window counsel confirms.

If counsel confirms the 30-day notice applies, the buyer-fee copy and §8 go live together at the end of the notice window; the seller-side rewrite need not wait.

## Out of scope

- Any API change to `/api/config/public` (owned by `ezley-market-net-api`; this spec only consumes it and depends on its D-040 shape).
- The marketplace UI's checkout fee display (owned by `ezley-market-ui-react`).
- Publishing dispute SLA / resolution-rate numbers (still deferred per `001` Q8).

## Dependencies

- **`ezley-market-net-api`** — `/api/config/public` must expose the D-040 fee shape (ticket-size bands, buyer protection rate + cap, $20 listing minimum, $200 escrow boundary) before Track A's config swap can fetch real values. **Blocking** for FR-FEE-020/022.
- **`ezley-docs`** — D-040 is **recorded in `09-decisions.md` on `origin/main`** (Accepted 2026-06-28); this spec is pinned to it. Glossary update (ticket-size bands, buyer protection fee, Lite/escrow split) is a separate docs-repo task and does not block this spec.
- **Counsel** — Track B gate.

## Open questions / `[NEEDS CLARIFICATION]`

- **Q1** *(open, but does NOT block the static numbers)* — Exact `/api/config/public` **key names** and units (cents vs dollars) for the D-040 shape. Relevant **only to a future `scripts/fetch-config.sh`**; the hand-authored dollar display config publishes the canonical numbers without it. Confirm with net-api before any fetch-script work.
- **Q2** *(RESOLVED — ratified by Tech Lead)* — Band boundaries: bottom band **"under $200" (exclusive)**; higher boundaries **inclusive**. So exactly **$200 → 8.5%**, **$50,000 → 8.5%**, **$150,000 → 7%**. Bands are non-overlapping and exhaustive (see "The locked model"). Worked examples use this (a $50,000 sale = 8.5%).
- **Q3** *(genuinely open — counsel)* — Does §15's 30-day material-change notice apply to introducing a buyer fee? Drives Track B release phasing. Must be answered in writing by counsel.
- **Q4** — Should the home teaser show a representative number at all, or go number-free to avoid implying a single headline rate now that fees are size-banded?
