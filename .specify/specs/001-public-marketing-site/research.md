# Research — Public Marketing Site

**Phase**: `plan` supporting doc
**Created**: 2026-05-19

## Why Jekyll

| Criterion | Jekyll | Astro | Next.js (SSG mode) | Eleventy | Hugo |
|---|---|---|---|---|---|
| Free | ✅ | ✅ | ✅ | ✅ | ✅ |
| GitHub Pages first-class support | ✅ native | ⚠️ via Actions | ⚠️ via Actions | ⚠️ via Actions | ⚠️ via Actions |
| No-JS baseline natural | ✅ output is plain HTML | ⚠️ islands-by-default but configurable | ❌ requires SSG export + manual rehydration discipline | ✅ | ✅ |
| Component reuse from `ezley-market-ui-react` (React + MUI) | ❌ | ✅ React islands | ✅ | ❌ | ❌ |
| Learning curve for solo founder | low (Liquid + YAML) | medium | medium-high | low | medium |
| Founder preference | ✅ explicitly | — | — | — | — |

**Decision**: Jekyll. The founder asked for it, GitHub Pages support is native, and the no-JS baseline is the natural output (not a configuration toggle). The cost is losing React component reuse from the marketplace UI repo — but per NFR-MKT-013, the parity commitment is at the *token* level (colors, fonts, wordmark), not the component level, so this loss is acceptable.

## Why Minimal Mistakes (theme)

| Theme | License | Marketing-friendly | Accessibility | Maintenance | Stars (GitHub, May 2026) |
|---|---|---|---|---|---|
| **Minimal Mistakes** | MIT | ✅ hero/splash/feature rows | ✅ semantic HTML, AA-passing defaults | active | ~13K |
| Cayman (Pages default) | CC0 | ❌ docs-style | ⚠️ contrast issues | low | ~1K |
| Hyde / Lanyon | MIT | ❌ blog-style | ⚠️ | low | ~3K each |
| al-folio | MIT | ❌ academic | ✅ | active | ~10K |
| Tale | MIT | ❌ minimal blog | ⚠️ | dormant | ~700 |

**Decision**: Minimal Mistakes. Only candidate with marketing-page idioms (hero, splash, feature rows, comparison sections) built in, AND active maintenance, AND accessibility considered in the defaults. The Ezley brand will be applied via a custom skin (`_sass/skins/_ezley.scss`) and a marketing-overlay layer (display font, hero photography, marketing-specific Liquid includes).

## Why GitHub Pages (hosting)

| Host | Free tier suitable | Native redirect support | Custom domain + SSL | CI integration | DDoS posture |
|---|---|---|---|---|---|
| **GitHub Pages** | ✅ public repos free | ❌ (deferred per L1 in plan.md) | ✅ Let's Encrypt | ✅ Actions native | weak (no inherent DDoS layer) |
| Cloudflare Pages | ✅ generous | ✅ `_redirects` file | ✅ | ✅ via Cloudflare Pages CI | strong (Cloudflare DDoS) |
| Vercel | ⚠️ Hobby tier; commercial-use restriction | ✅ `vercel.json` | ✅ | ✅ | strong |
| Netlify | ⚠️ Starter tier has bandwidth caps | ✅ `_redirects` file | ✅ | ✅ | strong |
| Azure Static Web Apps | ✅ free tier | ✅ `staticwebapp.config.json` | ✅ | ✅ via Azure DevOps / GitHub Actions | medium |
| AWS S3 + CloudFront | ✅ within free-tier limits | ✅ Lambda@Edge | ✅ | medium | strong |

**Decision**: GitHub Pages. Founder preference. The free tier is generous, deploys are simple (push to `gh-pages`), custom domain works, SSL is free. The redirect limitation (L1 in plan.md) is the one real cost — accepted because no legacy URLs need protection at M0.

**Revisit triggers** documented in plan.md L1.

## Why a build-time config fetch (not runtime)

The Pricing page MUST display rate numbers sourced from the marketplace's `/api/config/public` endpoint per P-09. Two timing options:

1. **Runtime fetch (client-side JS)** — page renders with a placeholder, JS fetches `/api/config/public`, replaces the placeholder. Pros: always-fresh values. Cons: violates NFR-MKT-004 no-JS baseline; SEO-degrades the Pricing page (Google's crawler often doesn't execute JS); LCP impact.
2. **Build-time fetch (server-side)** — the GitHub Action curls the endpoint, writes to `_data/config.yml`, Jekyll's Liquid renders the values into the static HTML. Pros: no-JS baseline preserved, SEO-friendly, instant LCP. Cons: values can be up to 24h stale.

**Decision**: option 2. Aligns with the no-JS baseline, makes Lighthouse and axe-core auditing predictable, and 24h staleness on pricing display is more than acceptable for a marketplace that doesn't change rates daily. The 24h max staleness is encoded in the daily cron schedule (NFR-MKT-008).

## Why a CNAME at repo root (not via DNS-only)

GitHub Pages reads a `CNAME` file from the repo root to associate the custom domain. Without it, the site serves at `<username>.github.io/<repo>` only. The DNS side (A records at the domain registrar pointing to GitHub Pages' four IPs) is the founder's manual step; the `CNAME` file is the in-repo half.

**Decision**: ship `CNAME` containing `ezley.com` at repo root. Document the DNS setup (A records: 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153) in `quickstart.md`.

## Things not researched (and why)

- **CDN edge configuration** — GitHub Pages has its own CDN. Cloudflare-in-front is the future migration if perf/redirect needs grow (see plan.md L1 revisit triggers). Not researched now.
- **A/B testing infrastructure** — no A/B in M0 scope. Deferred to F-MKT-004 if/when it lands.
- **Analytics SDK choice** — NFR-MKT-012 says no PII collection at M0. UTM-based outbound attribution is the only acquisition signal, and it requires no SDK. Plausible / Fathom / Posthog / GA4 selection deferred to F-MKT-002+.
- **Newsletter / email-capture pipeline** — explicitly deferred to F-MKT-002 (separate spec).
- **Internationalization runtime** — Spanish localization at M18 per P-08. Site is localization-READY (every string in `_data/copy/en-US.yml`) but the Spanish copy and the runtime locale-switching mechanism are F-MKT-005's job.
