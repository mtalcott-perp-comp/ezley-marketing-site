# ezley-marketing-site

The public consumer-facing marketing site for **Ezley.com** — the open, agent-native marketplace for high-trust, high-value, peer-to-peer commerce.

This repo is **not** the marketplace app itself. The marketplace app lives in:

- `ezley-market-net-api` — .NET 9 API (event-sourced)
- `ezley-market-ui-react` — React 18 + TS + MUI 5 buyer/seller/admin UI

This repo serves the **marketing surface** at `about.ezley.com`:

- The root home page — what Ezley is, who it's for, why it's different
- Category front doors — `about.ezley.com/tractors`, `/cars`, `/cameras`, `/furniture` (sub-brand landing pages per [01-vision.md](../../ezley-docs/01-vision.md))
- "How it works" — escrow, identity verification, dispute resolution, concierge
- "Why Ezley" — head-to-head against eBay / Facebook Marketplace / Craigslist / TractorHouse / Machinery Pete
- Pricing — ticket-size seller fee + buyer protection fee on escrow sales; free to list (per [D-040](../../ezley-docs/09-decisions.md), supersedes D-019)
- Trust & safety — public dispute statistics, identity tiers, escrow flow
- Agent-readiness — MCP / ACP / UCP / AP2 documentation surface

CTAs deep-link into the marketplace app (`ezley.com`) for actual signup, listing creation, and buying.

## Status

- 2026-05-19 — Folder scaffolded. Speckit `specify` phase in progress at [`.specify/specs/001-public-marketing-site/spec.md`](.specify/specs/001-public-marketing-site/spec.md).

## Workflow

This repo follows the same Speckit handoff workflow as the other two Ezley repos. See [`../../ezley-docs/10-speckit-handoff.md`](../../ezley-docs/10-speckit-handoff.md).

Order of phases: `specify → clarify → plan → tasks → analyze → implement`.

The product source-of-truth is `ezley-docs` (sibling of the `ezley-market/` workspace folder). All marketing copy, fee numbers, persona references, and competitive claims trace back to those documents — never invented here.
