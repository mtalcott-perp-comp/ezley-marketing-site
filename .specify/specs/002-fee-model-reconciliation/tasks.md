# Tasks — Fee-Model Reconciliation (rewrite public pricing story to D-040)

**Feature Branch**: `002-fee-model-reconciliation`
**Feature ID**: F-MKT-004 (proposed)
**Phase**: `tasks`
**Created**: 2026-06-28
**Spec**: [`./spec.md`](./spec.md) · **Plan**: [`./plan.md`](./plan.md)
**Source pinning**: D-040 (Accepted 2026-06-28), committed to `ezley-docs/09-decisions.md` on `origin/main`.

## How to read this file

Tasks are grouped into the **two tracks the plan defines** and ordered by dependency within each track. Each task carries:

- a stable **task id** (`T0xx`),
- the **FR / requirement(s)** it satisfies,
- the **file(s)** it touches,
- `[P]` if it may run in **parallel** with its siblings (no shared file, no ordering dependency).

**Canonical numbers (every task uses these, all from `_data/config.yml`, none inlined):** seller fee **12% (min $5) / 8.5% / 7% / 5%** by ticket size; buyer protection **2.5% capped $2,500** on escrow sales (**$200+**); **Lite** under $200 (no escrow, no buyer fee); **free to list**; **$20 listing minimum**; **verification price-neutral**.

**Boundary convention (ratified, Q2):** bottom band **"under $200" exclusive**; higher boundaries **inclusive** — exactly **$200 → 8.5%**, **$50,000 → 8.5%**, **$150,000 → 7%**. Worked examples MUST follow this (a **$50,000 sale = 8.5%**).

> **Track A ships immediately and is NOT legally gated.** The buyer-protection-fee copy authored in Track A is **hidden behind a config flag** (dark) and is not revealed until Track B's effective date.
>
> **⚠️ Track B is LEGAL-GATED.** Every Track B task is **BLOCKED ON COUNSEL** and must not publish until counsel signs off (R-LEGAL-1/2/3). Do not flip the buyer-fee flag or publish §8 outside Track B.

---

## TRACK A — Marketing rewrite (ships first, NOT legally gated)

### Phase A1 — Config + data contract (foundation; everything else renders from here)

- [ ] **T001** — **Restructure `_data/config.yml` to the D-040 display shape.** *(FR-FEE-020)* — File: `_data/config.yml`.
  - **Remove** the rail keys `pricing.cardTakeRatePct` (5.5), `pricing.achTakeRatePct` (3.5), `pricing.wireTakeRatePct` (3.0) and the rail thresholds `payments.cardMaxAmount` / `payments.wireOnlyMinAmount`.
  - **Add** the D-040 dollar shape: `pricing.sellerFeeBands` (12/min $5, 8.5, 7, 5 with inclusive `maxAmount` upper bounds per the ratified boundary), `pricing.buyerProtectionFeePct: 2.5`, `pricing.buyerProtectionFeeCapDollars: 2500`, `pricing.listingMinimumDollars: 20`, `pricing.escrowMinAmount: 200`.
  - **Add** the buyer-fee dark-flag key (e.g. `pricing.buyerProtectionFeePublished: false`) — Track B flips it (see T020).
  - **Keep** `payments.autoReleaseHoursDefault`, `payments.depositPercentDefault`, and the whole `listings.*` block.
  - Update the header comment: snapshot date + "D-040 — hand-authored in dollars for display."
  - Config is hand-authored in dollars; it does **not** mirror the API's cents keys (no `fetch-config.sh` exists — Q1 deferred).

- [ ] **T002** — **Add the D-040 copy keys to `_data/copy/en-US.yml`.** *(FR-FEE-035)* — File: `_data/copy/en-US.yml`.
  - Rewrite `pricing.hero_headline` / `pricing.intro` — drop "bundled by payment path"; D-040 framing.
  - Rewrite `pricing.rate_card_inclusion` and the `index.html`-fed teaser copy (line ~136 "One Ezley fee, bundled by payment path") to ticket-size language.
  - **Add** keys: ticket-size band labels/descriptions, `pricing.buyer_protection.*` (framed as escrow + dispute protection per P-02), Lite/escrow explainer, `pricing.worked_example.*` re-anchored to a ticket-size example.
  - Copy **frames** numbers; numbers come from config. Scan the whole file for any other "payment path" / "Wire/ACH/Card rate" copy and rewrite it.
  - *Depends on T001 (keys it references must exist).* 

### Phase A2 — `/pricing` rewrite (the core change)

- [ ] **T003** — **Rewrite `_pages/pricing.html`: seller section (3 rail cards → ticket-size schedule + Model A).** *(FR-FEE-001..006)* — File: `_pages/pricing.html`.
  - Delete the three rail cards (Wire 3.0 / ACH 3.5 / Card 5.5, lines ~56–155) and the `sr-only` "Ezley fee by payment path" heading.
  - Render the **four ticket-size bands** from `cfg.pricing.sellerFeeBands` (one clean number per band; $5 floor on band 1), "Free to list," and the **$20 minimum** from `cfg.pricing.listingMinimumDollars`.
  - **No** rail words as fee tiers; **no** Stripe/Connect/concierge/reserve itemization; **no** verification-affects-price claim.
  - *Depends on T001, T002.*

- [ ] **T004** — **Rewrite `/pricing` worked examples to the ratified boundaries.** *(FR-FEE-003)* — File: `_pages/pricing.html`.
  - Replace the rail worked-example block (lines ~25–27: "Wire $40,000 × 3.0% …") with ticket-size examples computed **live from config**: a **$50,000 sale = 8.5%**, a $150,000 sale = 7%, a low-value sale hitting the **$5 floor**. Arithmetic stays Liquid-from-config (no literals).
  - *Depends on T003.*

- [ ] **T005** — **Rewrite `/pricing` buyer-protection section (separate, behind the dark flag).** *(FR-FEE-010, FR-FEE-011, FR-FEE-013)* — File: `_pages/pricing.html`.
  - Add a **separate** buyer section: **2.5% capped $2,500** from config, "escrow sales **$200+** only / **Lite (under $200) has none**," framed as buying escrow + dispute protection (P-02).
  - **Wrap the entire buyer section in `{% if cfg.pricing.buyerProtectionFeePublished %}`** so it stays dark until Track B flips the flag. Track A must render correctly with the flag **off**.
  - *Depends on T001, T002, T003.* **Authored in Track A; revealed only by Track B.**

- [ ] **T006** — **Rewrite `/pricing` FAQ `<details>` blocks + front-matter `description` to D-040 (Model A).** *(FR-FEE-012, FR-FEE-031 alignment)* — File: `_pages/pricing.html`.
  - FAQ: "How is my fee calculated?" (by sale size), "Do buyers pay a fee?" (yes — protection fee on escrow sales; gate this answer behind the dark flag too), "Listing fees / subscriptions?" (still no), "$20 minimum?", Lite-vs-escrow at $200 (from `cfg.pricing.escrowMinAmount`).
  - Replace front-matter `description` (line 5, "One Ezley fee per payment path: 3.0% wire, 3.5% ACH, 5.5% card") with the D-040 story — **de-rail the meta description** (see also T012 grep).
  - Keep seller and buyer answers in **distinct blocks** — **never** an all-in number.
  - *Depends on T003, T005.*

### Phase A3 — Other marketing surfaces (parallelizable once config + copy land)

- [ ] **T007** [P] — **Rewrite the home pricing teaser + CTA in `index.html`.** *(FR-FEE-030)* — File: `index.html`.
  - Replace the rail teaser (line ~170: "One Ezley fee, bundled by payment path. Wire … ACH … Card …") with a D-040-true teaser ("Free to list. Fees scale with sale size. Buyers get escrow protection." — exact copy in `_data/copy/en-US.yml`, config-sourced if it shows a number; otherwise number-free per Q4).
  - Verify the "Start selling" / pricing CTA still routes correctly and carries no rail language.
  - *Depends on T001, T002.*

- [ ] **T008** [P] — **Reconcile `_pages/how-it-works.html` with Lite vs. escrow + check the CTA.** *(FR-FEE-031)* — File: `_pages/how-it-works.html`.
  - Surface the **$200** boundary from `cfg.pricing.escrowMinAmount`; escrow-funding + auto-release steps apply to **escrow sales ($200+)**; a **Lite** sale skips escrow. No rail-rate language. Keep the already-config-sourced auto-release section.
  - Verify the page CTA into the app is intact and rail-free.
  - *Depends on T001, T002.*

- [ ] **T009** [P] — **Rewrite `llms.txt` pricing + config lines to D-040.** *(FR-FEE-032)* — File: `llms.txt`.
  - Line 16: "Bundled fee per payment path (Wire / ACH / Card). Free to list." → "Seller fee by sale size; buyer protection fee on escrow sales; free to list."
  - The `/api/config/public` description line → reflect D-040 keys.

- [ ] **T010** [P] — **Update `README.md` pricing line to cite D-040.** *(FR-FEE-033)* — File: `README.md`.
  - "Pricing — path-tiered, single bundled rate (per D-019)" → cite **D-040**, ticket-size + buyer-fee model.

- [ ] **T011** [P] — **`/is-it-safe/` consistency check.** *(FR-FEE-034)* — File: `_pages/is-it-safe.html`.
  - Review the comment-block claims ("wire/ACH above threshold, no chargeback") and the buyer Q&A ("pay, take the item, then reverse the charge?") for consistency with the **$200 escrow boundary** and the new buyer protection fee. Adjust framing so nothing implies buyers pay nothing. **No** new rate numbers unless config-sourced.
  - *Depends on T001 (escrow boundary key).*

### Phase A4 — Guardrails (run last; they verify the rewrite is clean)

- [ ] **T012** — **Build-grep: assert no stale rail strings survive.** *(FR-FEE-021, R4)* — File: `scripts/check-no-stale-fees.sh` (new) wired into the build/CI; verifies across `index.html`, `llms.txt`, `README.md`, `_pages/*`, `_data/*`.
  - Fail the build if any of these literals appear: **`5.5`**, **`3.5`**, **`3.0`** (as fee rates), **`payment path`**, **`buyers do not pay`**, and the dead config keys `cardTakeRatePct` / `achTakeRatePct` / `wireTakeRatePct`. (Allow `3.0`/`3.5`/`5.5` only where demonstrably non-fee, e.g. a version string — scope the grep to fee context.)
  - *Depends on all of A1–A3 (it must pass against the rewritten tree).* 

- [ ] **T013** — **P-09 grep: assert zero inline D-040 fee constants.** *(FR-FEE-021)* — Same script as T012 (or a sibling), part of the build.
  - Assert no D-040 number (`12`, `8.5`, `7`, `5`, `2.5`, `2500`, `200`, `20`, `50000`, `150000`) appears as a hard-coded fee in `_pages/*` or `_data/copy/*` — they live only in `_data/config.yml`. Retain/extend the existing P-14 grep (no Stripe/Connect/concierge/reserve itemization).
  - *Depends on A1–A3.*

- [ ] **T014** — **Model-A "no all-in number" grep/lint.** *(FR-FEE-012)* — Same build hook.
  - Assert no "all-in" / combined seller+buyer figure appears (anti-pattern check): the seller fee and buyer protection fee are never merged into one number on any page. Verify the seller and buyer sections remain structurally distinct (NFR-FEE-003).
  - *Depends on T003, T005, T006.*

- [ ] **T015** — **Track A accessibility + no-JS regression check.** *(NFR-FEE-001, NFR-FEE-002, NFR-FEE-003)* — Pages: `_pages/pricing.html`, `index.html`, `how-it-works.html`.
  - Confirm WCAG 2.1 AA, 48px targets, 320px-first; numbers render server-side via Liquid (no-JS baseline); seller and buyer sections individually legible on a 320px phone.
  - *Depends on A2–A3.*

---

## TRACK B — Legal (LEGAL-GATED — every task BLOCKED ON COUNSEL)

> **⚠️ BLOCKED ON COUNSEL. Do not merge or publish any Track B task until counsel signs off (R-LEGAL-1).** The buyer-fee dark flag (T001 / T020) stays **off** until the §8 effective date, and the buyer-fee copy must not be advertised on any surface ahead of whatever §15 notice window counsel confirms (FR-FEE-013).

- [ ] **T016** — **[BLOCKED ON COUNSEL] Draft the `terms.md §8 (Fees)` rewrite to D-040.** *(FR-FEE-040)* — File: `_pages/terms.md` (§8, lines ~88–100).
  - Replace the rail fee table (Card 5.5 / ACH 3.5 / Wire 3.0) with: ticket-size seller bands, **$20 listing minimum**, **buyer protection fee 2.5% capped $2,500 on escrow sales $200+**, verification-price-neutral.
  - **Delete** "Buyers do not pay Ezley directly…" and the mixed-rail "each capture event is priced at its own rail" sentence.
  - Author as a **draft** only — do not publish.

- [ ] **T017** — **[BLOCKED ON COUNSEL] Counsel review + sign-off on the §8 rewrite.** *(R-LEGAL-1, FR-FEE-041)* — Deliverable: written counsel approval recorded.
  - §8 MUST NOT be published until approved. Captures the change-of-who-is-charged (buyers now pay) as a material contract change.
  - *Depends on T016.*

- [ ] **T018** — **[BLOCKED ON COUNSEL] Resolve the §15 30-day-notice question (Q3).** *(R-LEGAL-3, FR-FEE-013)* — Deliverable: counsel's written answer on whether §15's 30-day material-change notice applies to introducing the buyer fee.
  - If it applies, the buyer-fee reveal + §8 effective date must respect the notice window (drives a two-phase publish; the seller-side Track A rewrite need not wait).
  - *Depends on T016; gates T019–T021.*

- [ ] **T019** — **[BLOCKED ON COUNSEL] Bump `terms.md` `effective_date`.** *(R-LEGAL-2, FR-FEE-041)* — File: `_pages/terms.md` (front-matter, line 6, currently `2026-06-27`).
  - Set to the date the new fee terms take effect, consistent with the §15 outcome (T018).
  - *Depends on T017, T018.*

- [ ] **T020** — **[BLOCKED ON COUNSEL] Flip the buyer-fee config flag.** *(FR-FEE-013)* — File: `_data/config.yml` (`pricing.buyerProtectionFeePublished: false → true`).
  - Reveals the Track A buyer-protection section (T005) and the buyer FAQ answer (T006). Done **only** on the effective date.
  - *Depends on T017, T018, T019.*

- [ ] **T021** — **[BLOCKED ON COUNSEL] Publish §8 + flip the flag together on the effective date.** *(FR-FEE-041, Release phasing)* — Files: `_pages/terms.md`, `_data/config.yml`.
  - The §8 publish (T016 approved text) and the buyer-fee reveal (T020) go live **together** at the end of any §15 notice window. This is the single coordinated Track B publish.
  - *Depends on T017, T018, T019, T020.*

- [ ] **T022** — **`privacy.md` light consistency check.** *(FR-FEE-014)* — File: `_pages/privacy.md`.
  - Confirm nothing contradicts a buyer-paid protection fee. `privacy.md` already describes buyer-funded escrow via licensed partners and is **not** on the §8 critical path — **likely no edit**. (Not strictly counsel-gated, but grouped with the legal surfaces; safe to run anytime.)

---

## Dependency summary

```
Track A (ships first):
  T001 ─┬─ T002 ─┬─ T003 ─┬─ T004
        │        │        ├─ T005 ─┐
        │        │        └─ T006 ─┤
        │        ├─ T007 [P]       │
        │        └─ T008 [P]       │
        ├─ T009 [P]                │
        ├─ T010 [P]                │
        └─ T011 [P]                │
                                   ├─ T014
   (A1–A3 complete) ── T012, T013, T015
                                   └─ T014

Track B (BLOCKED ON COUNSEL):
  T016 ─┬─ T017 ─┐
        └─ T018 ─┼─ T019 ─ T020 ─ T021
  T022  (independent light check)
```

## Counts

- **Track A (not gated):** 15 tasks — T001–T015. Config + copy (2), `/pricing` rewrite (4), other surfaces (5, four parallel), guardrails (4).
- **Track B (legal-gated):** 7 tasks — T016–T022. Six explicitly **BLOCKED ON COUNSEL** (T016–T021); T022 is an independent light privacy check.
- **Total:** 22 tasks.

## Still open (carried from spec)

- **Q1** — exact `/api/config/public` key names + units. **Does not block Track A** (config is hand-authored in dollars; no `fetch-config.sh` exists). Becomes load-bearing only if a fetch script is added later.
- **Q3** — §15 30-day-notice applicability — **genuinely open, counsel (T018).** Drives Track B phasing.
- **Q4** — whether the home teaser shows a representative number or goes number-free (T007 author's call; number-free is the safe default now that fees are size-banded).
