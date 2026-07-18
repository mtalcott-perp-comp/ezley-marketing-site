# DRAFT — Terms of Service §8 (Fees) rewrite to D-040

> **STATUS: DRAFT. DO NOT PUBLISH. BLOCKED ON COUNSEL.**
>
> This is task **T016** of `002-fee-model-reconciliation` (Track B, legal-gated). It is an
> attorney-review deliverable, **not** live site content. It intentionally lives under
> `.specify/` (excluded from the Jekyll build) so it cannot deploy to about.ezley.com.
> Nothing here is published until **T017 (counsel sign-off)**, **T019 (`effective_date` bump)**,
> and **T018 (§15 30-day-notice resolution)** are complete — then T021 publishes §8 and flips the
> buyer-fee config flag together.
>
> Prepared: 2026-07-18. Pins to **D-040** (Accepted 2026-06-28, `ezley-docs/09-decisions.md`).

---

## Why this rewrite is legally consequential (read first)

D-040 changes **who is charged**, not just a rate:

1. **Buyers now pay a fee.** The current §8 states *"Buyers do not pay Ezley directly."* D-040 adds a
   **2.5% buyer protection fee** on escrow sales. That sentence becomes false the moment the buyer fee
   ships — a **material change to the Buyer's contractual obligations**, not a rate tweak.
2. **The fee basis changes** from payment-path tiers (Card/ACH/Wire) to **ticket-size** tiers, plus the
   new buyer fee, plus a stated **$20 listing minimum** and an explicit **verification-price-neutral**
   promise.

Because a buyer-facing fee is almost certainly a **material change**, the **§15 30-day advance-notice
clock likely applies** (open question **Q3** — counsel must answer in writing). If it does, the buyer-fee
reveal and this §8 effective date must respect that window.

## Deliberate drafting choices for counsel to confirm

- **Numbers are stated explicitly, not pulled from config.** Unlike the marketing pages (which render
  every number from `_data/config.yml` per P-09), binding contract text should be self-contained and must
  **not** silently change when an operations config value flips — a fee change should route through §15.
  If counsel prefers the Terms to reference the Pricing page as the operative source instead, flag it.
- **Boundary convention (ratified):** bottom band *"under $200"* is exclusive; every higher boundary is
  inclusive. So **exactly $200 → 8.5%**, **$50,000 → 8.5%**, **$150,000 → 7%**. The draft states this
  explicitly to avoid boundary ambiguity in a binding document.
- **Values** trace to D-040 and the live API `Pricing:*` config: seller fee 12% (min $5) / 8.5% / 7% / 5%;
  buyer protection 2.5% capped $2,500 on escrow sales ($200+); Lite under $200 (no escrow, no buyer fee);
  $20 listing minimum; free to list; verification price-neutral.

---

## PROPOSED REPLACEMENT — §8 in full

> Replace the entirety of the current §8 (lines 88–100 of `_pages/terms.md`) with the following.

### 8. Fees

**Free to list.** Creating and publishing a Listing is free. There is no insertion fee and no
subscription. Ezley charges a fee only when a Transaction completes.

**Seller fee — by sale size.** When a Transaction completes, Ezley deducts a single fee from the
Seller's proceeds. The fee is a percentage of the gross sale price, determined solely by the size of the
sale — **not** by the payment method and **not** by the Seller's verification tier:

| Gross sale price | Seller fee |
|---|---|
| Under $200 | 12% (minimum fee of $5) |
| $200 to $50,000 | 8.5% |
| Over $50,000 to $150,000 | 7% |
| Over $150,000 | 5% |

These bands are non-overlapping and cover every sale. The lower boundary of each band above $200 is
inclusive: a sale of exactly **$200** is charged at **8.5%**, a sale of exactly **$50,000** at **8.5%**,
and a sale of exactly **$150,000** at **7%**. For a sale under $200, a **minimum Seller fee of $5**
applies. The Seller fee is deducted from the Seller's proceeds when the escrowed funds are released to
the Seller (or, for a Lite sale as described below, when the sale completes); the Seller's net payout is
the gross sale price less the Seller fee.

**Buyer protection fee — on escrow sales.** On an escrow sale — a sale with a gross price of **$200 or
more** — the Buyer pays a **buyer protection fee of 2.5% of the gross sale price, capped at $2,500**. This
fee is charged to the Buyer in addition to the sale price, and it is disclosed to the Buyer at checkout
before the Buyer commits to the purchase. The buyer protection fee is the consideration for the escrow
and dispute-resolution services described in §7 and §9.

**Lite sales.** A sale with a gross price **under $200** is a **Lite sale**. Lite sales do not use escrow
and carry **no buyer protection fee**. Only the Seller fee (12%, minimum $5) applies.

**Listing minimum.** The lowest sale price Ezley supports for a Listing is **$20**.

**Verification is price-neutral.** A Seller's verification tier (basic, verified, or trusted-seller) does
**not** affect the Seller fee or any other fee. Identity verification and tier upgrades are free.

**Changes to fees.** Ezley may change these fees prospectively. Any material change to this fee structure
will be made in accordance with §15 (Modifications).

---

## Redline summary (what changed vs. the live §8)

**Removed**

- The rail-based fee table — *"Card 5.5% / ACH 3.5% / Wire 3.0% of gross."*
- *"For mixed-rail Transactions … each capture event is priced at its own rail."* (rails no longer price anything)
- **"Buyers do not pay Ezley directly; the Seller's net payout is the gross less the bundled Ezley fee."**
  (The first clause is now false; the net-payout clause is preserved and relocated into the Seller-fee
  paragraph.)
- *"As of this draft:"* hedge language.

**Added / changed**

- Ticket-size Seller-fee schedule (12% / 8.5% / 7% / 5%) with the ratified boundary convention.
- $5 minimum Seller fee under $200.
- Buyer protection fee (2.5%, capped $2,500) on escrow sales ($200+), **disclosed at checkout**.
- Explicit Lite-sale definition (under $200, no escrow, no buyer fee).
- $20 listing minimum.
- Explicit verification-price-neutral promise (kept the "tier upgrades are free" line, generalized).
- A §15 cross-reference governing future fee changes.

---

## Open items counsel must resolve (gates + cross-references)

1. **R-LEGAL-3 / Q3 — §15 30-day notice (blocking).** Does introducing a buyer-facing fee trigger §15's
   *"at least 30 days before they take effect"* notice? Answer in writing. If yes, the buyer-fee reveal +
   this effective date must respect the window (two-phase publish; the seller-side marketing rewrite is
   already live and did not wait).
2. **R-LEGAL-2 — `effective_date` bump.** Front-matter `effective_date` is currently `2026-06-27`. Bump it
   to the date the new fee terms take effect, consistent with the §15 outcome above. (Not done in this
   draft.)
3. **Cross-reference — §7 vs. Lite sales (needs a conforming edit).** §7 currently says *"Every purchase on
   Ezley flows through Escrow."* That is no longer literally true: **Lite sales (under $200) do not use
   escrow.** §7 needs a carve-out (e.g., *"Every purchase of $200 or more flows through Escrow; sales under
   $200 are Lite sales settled without escrow"*). Recommend counsel approve a matching §7 tweak in the same
   review so §7, §8, and the Escrow Disclosures agree.
4. **Cross-reference — §2 (rails) is still accurate.** §2's description of payment mechanics (Stripe for
   card/ACH; a licensed escrow provider for wire) describes *how funds move*, not *how fees are priced*, so
   D-040 does not require a §2 change. Confirm.
5. **Incorporated agreements.** §1 incorporates a Seller Agreement, Buyer Terms, and Escrow Disclosures by
   reference. Confirm those documents' fee language (especially any buyer-fee and Lite-sale wording) is
   updated in lockstep so nothing contradicts this §8.
6. **Config vs. contract numbers.** Confirm the "state numbers explicitly in the Terms" choice above (vs.
   referencing the Pricing page as the operative source).

## After counsel signs off (do not do any of this before then)

- Apply the approved §8 text to `_pages/terms.md` (and the agreed §7 carve-out).
- Bump `effective_date` (T019).
- Flip `_data/config.yml` → `pricing.buyerProtectionFeePublished: true` (T020), which reveals the already-
  authored buyer-protection section and buyer FAQ on `/pricing`.
- Publish §8 and the flag flip **together** on the effective date (T021), respecting any §15 window.
