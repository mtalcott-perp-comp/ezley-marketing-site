# Plan — Fee-Model Reconciliation (rewrite public pricing story to D-040)

**Feature Branch**: `002-fee-model-reconciliation`
**Phase**: `plan`
**Created**: 2026-06-28
**Spec**: [`./spec.md`](./spec.md)
**Status**: Plan-phase deliverable. `tasks.md` follows.

## Stack decision

**Unchanged from `001`.** Jekyll 4.x static site, GitHub Pages hosting, GitHub Actions build, copy in `_data/copy/en-US.yml`, fee numbers in `_data/config.yml` (snapshot of `/api/config/public`), Liquid renders numbers server-side. This feature is a **content + data-contract change**, not an architecture change. No new dependencies, no new build steps beyond updating `scripts/fetch-config.sh` validation.

## Sequencing (two tracks, gated by legal)

```
Track A  (NOT legally gated — ship first)
  1. Config contract  → 2. fetch-config validation  → 3. /pricing seller section
  → 4. /pricing buyer section (behind config flag, dark)  → 5. home teaser
  → 6. how-it-works  → 7. llms.txt + README  → 8. is-it-safe consistency
  → 9. copy rewrite  → 10. P-09 grep guard

Track B  (LEGALLY GATED — counsel sign-off required)
  L1. terms.md §8 rewrite (draft)  → L2. counsel review  → L3. effective_date bump
  → L4. §15 30-day-notice resolution  → L5. flip buyer-fee config flag + publish §8 together
```

The buyer protection fee copy (Track A step 4) is **authored but hidden** behind a config flag so the seller-side rewrite ships immediately while the buyer fee waits for the §15 notice window.

## Config shape — hand-authored in dollars for display

`_data/config.yml` is **hand-authored for display and uses dollars** (not the API's cents keys). Its **displayed D-040 numbers MUST equal the canonical set** (FR-FEE-020); key-for-key parity with `/api/config/public` is required **only if and when** `scripts/fetch-config.sh` actually exists and fetches (spec Q1). The shape below is the display shape the site renders from.

```yaml
# _data/config.yml  (D-040 — replaces the rail block; dollars, hand-authored for display)
pricing:
  # Seller fee by ticket size. Ordered bands; each band has an upper bound
  # in DOLLARS and a rate. Floor only on band 1.
  # Boundary convention (Q2, ratified): bottom band exclusive ("under $200");
  # higher boundaries inclusive. maxAmount = inclusive upper bound of the band.
  sellerFeeBands:
    - { maxAmount: 200,    ratePct: 12,  minFee: 5 }   # under $200, $5 floor (exclusive upper)
    - { maxAmount: 50000,  ratePct: 8.5 }              # $200–$50,000 inclusive  → $200 & $50,000 = 8.5%
    - { maxAmount: 150000, ratePct: 7 }                # over $50,000–$150,000 incl → $150,000 = 7%
    - { maxAmount: null,   ratePct: 5 }                # over $150,000
  buyerProtectionFeePct: 2.5
  buyerProtectionFeeCapDollars: 2500
  listingMinimumDollars: 20
  escrowMinAmount: 200      # $200 and up = escrow; below = Lite

# REMOVED from the old snapshot:
#   pricing.cardTakeRatePct / achTakeRatePct / wireTakeRatePct
#   payments.cardMaxAmount / payments.wireOnlyMinAmount   (rail thresholds)
# RETAINED (not fee-model):
#   payments.autoReleaseHoursDefault, payments.depositPercentDefault
#   listings.*  (review thresholds, photo counts)
```

> **Boundary convention is ratified (Q2):** bottom band "under $200" exclusive; higher boundaries inclusive — exactly $200 → 8.5%, $50,000 → 8.5%, $150,000 → 7%. Worked examples MUST use this (a **$50,000 sale = 8.5%**). The API's exact comparator/key names (Q1) only become load-bearing once a fetch script exists.

## File-by-file change plan

### Track A — config + data contract

**`_data/config.yml`** — Replace the `pricing:` rail block (`cardTakeRatePct` / `achTakeRatePct` / `wireTakeRatePct`) and the rail thresholds in `payments:` (`cardMaxAmount`, `wireOnlyMinAmount`) with the D-040 shape above. Update the header comment (snapshot date, "D-040" source note). `autoReleaseHoursDefault`, `depositPercentDefault`, and the `listings:` block stay. *(FR-FEE-020)*

**`scripts/fetch-config.sh`** *(deferred — only if it exists)* — Verify whether this script exists. The static dollar display config does **not** depend on it. If it exists, update its key allow-list / validation so the build **fails** if any required D-040 key is missing, preventing a silent stale-rail render. If it does **not** exist, the committed `_data/config.yml` is the source of the displayed numbers and no script work is in scope. *(FR-FEE-022)*

### Track A — `/pricing` (the core rewrite)

**`_pages/pricing.html`** — Largest change. Replace the three rail cards (Wire/ACH/Card) with:
- **Seller section** — four ticket-size bands from `cfg.pricing.sellerFeeBands` (12% / 8.5% / 7% / 5%, $5 floor on band 1), "Free to list", $20 minimum from `cfg.pricing.listingMinimumDollars`. One clean number per band; no itemization. *(FR-FEE-001..006)*
- **Buyer section** (separate, gated behind the buyer-fee config flag) — 2.5% capped $2,500 from config, "escrow sales $200+ only / Lite has none". *(FR-FEE-010..013)*
- Remove the "Above $X, card payment is not offered" rail paragraph (replace with the Lite/escrow $200 framing, escrow boundary from `cfg.pricing.escrowMinAmount`). *(FR-FEE-031 alignment)*
- Update the FAQ `<details>` blocks (currently rail-flavored) to D-040 language: "How is my fee calculated?" (by sale size), "Do buyers pay a fee?" (yes — protection fee on escrow sales), "Listing fees / subscriptions?" (still no), "$20 minimum?". Remove the per-rail framing. Keep MODEL A: seller and buyer answers in distinct blocks, never an all-in number. *(FR-FEE-012)*
- Front-matter `description` (currently "One Ezley fee per payment path: 3.0% wire, 3.5% ACH, 5.5% card") rewritten to the D-040 story.

### Track A — other surfaces

**`index.html`** — Pricing teaser (~line 170). Remove the rail line *"Wire {wireTakeRatePct}%. ACH {achTakeRatePct}%. Card {cardTakeRatePct}%."* Replace with a D-040-true teaser (config-sourced if it shows a number; otherwise number-free per spec Q4). Update front-matter `description` if it implies rail pricing (it currently says "Free to list" only — likely fine). *(FR-FEE-030)*

**`_pages/how-it-works.html`** — Reconcile buyer/seller flow copy with Lite vs. escrow. The escrow-funding and auto-release steps apply to **escrow sales ($200+)**; a Lite sale skips escrow. Surface the $200 boundary from `cfg.pricing.escrowMinAmount`. No rail-rate language. Auto-release section already config-sourced — keep. *(FR-FEE-031)*

**`llms.txt`** — Line 16 ("Pricing: Bundled fee per payment path (Wire / ACH / Card). Free to list.") → D-040 ("Seller fee by sale size; buyer protection fee on escrow sales; free to list."). Line 36 (`/api/config/public` description: "pricing rates, payment thresholds") → reflect D-040 keys. *(FR-FEE-032)*

**`README.md`** — Line 16 ("Pricing — path-tiered, single bundled rate (per D-019)") → cite **D-040**, ticket-size + buyer-fee model. *(FR-FEE-033)*

**`_pages/is-it-safe.html`** — Secondary. Review the comment-block claims (line ~17 "wire/ACH above threshold, no chargeback") and the buyer Q&A (~line 221 "Can a buyer pay, take the item, then reverse the charge?") for consistency with the $200 escrow boundary and the new buyer protection fee. Adjust framing so nothing implies buyers pay nothing. No new rate numbers unless config-sourced. *(FR-FEE-034)*

**`_data/copy/en-US.yml`** — Rewrite:
- `pricing.hero_headline` / `pricing.intro` — drop "bundled by payment path"; D-040 framing.
- `pricing.rate_card_inclusion` — keep the "what's included, all bundled, one number" spirit; ensure it reads for ticket-size bands, not rails.
- New keys: ticket-size band labels/descriptions, buyer-protection section copy (`pricing.buyer_protection.*`), Lite/escrow explainer.
- `pricing.worked_example.*` — re-anchor to a ticket-size example.
- `home.pricing_teaser` — already "Free to list. Pay only when you sell." (likely survives); verify no rail language elsewhere in `home.*`.
- Scan the whole file for any other "payment path" / "Wire/ACH/Card rate" copy. *(FR-FEE-035)*

### Track A — guardrail

**P-09 grep guard** (CI or `scripts/`) — Assert no literal D-040 fee number (`12`, `8.5`, `7`, `5`, `2.5`, `2500`, `200`, `20`, `50000`, `150000`, `5` floor) appears as a hard-coded fee in `_pages/*` or `_data/copy/*` (allow them only in `_data/config.yml`). Retain/extend the existing P-14 grep (no Stripe/Connect/concierge/reserve itemization). Add an "all-in fee" anti-pattern check for MODEL A. *(FR-FEE-021, FR-FEE-012)*

### Track B — legal (gated, do NOT publish without counsel)

**`_pages/terms.md`** — §8 (Fees, lines ~88–100) rewritten to D-040: ticket-size seller bands, $20 listing minimum, **buyer protection fee 2.5% capped $2,500 on escrow sales $200+**, verification-price-neutral. **Delete** "Buyers do not pay Ezley directly…" and the mixed-rail "each capture event is priced at its own rail" sentence. Bump front-matter `effective_date` (line 6, currently `2026-06-27`). Cross-check §15 (line ~137) 30-day-notice clause. **Gated on R-LEGAL-1/2/3.** *(FR-FEE-040, FR-FEE-041)*

**`_pages/privacy.md`** — Light check only. It already describes buyer-funded escrow via licensed partners (no "buyers don't pay Ezley" claim), so likely no edit. Confirm nothing contradicts a buyer-paid protection fee. *(FR-FEE-014)*

## Risks

- **R1 (highest) — Legal.** `terms.md §8` reverses a binding "buyers don't pay" promise; the §15 30-day notice may delay the buyer-fee reveal. Mitigation: two-track phasing + config-flagged buyer copy (ships dark).
- **R2 — Config drift (de-risked).** The site config is hand-authored in dollars for display; the displayed numbers equal the canonical D-040 set and do **not** block on the API contract. Cross-repo key-for-key parity only matters once a `fetch-config.sh` exists (Q1, FR-FEE-022). Mitigation: publish the canonical dollar numbers now; defer API-key alignment to the fetch script.
- **R3 — Boundary semantics (Q2, RESOLVED).** Ratified by the Tech Lead: bottom band "under $200" exclusive; higher boundaries inclusive — exactly $200 → 8.5%, $50,000 → 8.5%, $150,000 → 7%. Worked examples and config `maxAmount` follow this; no longer an open risk.
- **R4 — Stale-rail leakage.** Old "5.5% card" strings hide in meta descriptions and the existing Card-5.5%-vs-API-8.5% drift. Mitigation: the P-09 grep guard plus a full-repo grep for `5.5`, `3.5`, `3.0`, "payment path", "Wire/ACH/Card" before merge.
- **R5 — Boundary semantics now ratified (was: provenance).** D-040 is **committed (Accepted 2026-06-28) to `ezley-docs/09-decisions.md` on `origin/main`**, and the band-boundary convention (Q2) is **ratified by the Tech Lead**: bottom band "under $200" (exclusive); higher boundaries inclusive — exactly $200 → 8.5%, $50,000 → 8.5%, $150,000 → 7%. Worked examples and the config `maxAmount` comparators MUST follow this; the API's comparator confirmation (Q1) only matters once a fetch script exists.

## Definition of done (Track A)

- `/pricing`, home teaser, how-it-works, llms.txt, README, is-it-safe, and copy carry the D-040 seller story; zero rail-rate language; zero inline fee constants (grep clean).
- `_data/config.yml` mirrors the confirmed `/api/config/public` D-040 shape key-for-key; `fetch-config.sh` validates it.
- Buyer protection fee copy authored and gated dark behind the config flag.
- MODEL A respected — seller and buyer numbers in separate sections, never an all-in figure.

## Definition of done (Track B)

- §8 rewritten, counsel-approved, `effective_date` bumped, §15 notice question resolved in writing; buyer-fee flag flipped on the effective date.
