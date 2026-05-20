# Quickstart — ezley-marketing-site

How to set up locally, deploy to GitHub Pages, and operate the live site.

## Prerequisites

- Ruby 3.2.x (any minor) — `rbenv install 3.2.4 && rbenv local 3.2.4` recommended
- Bundler — `gem install bundler`
- A GitHub account with permission to push to the repo
- (One-time) The `ezley.com` domain registrar's DNS panel — to set A records pointing at GitHub Pages

## Local development

```bash
cd ezley-marketing-site
bundle install              # one-time, after pulling
bundle exec jekyll serve    # boots on http://127.0.0.1:4000
```

The local server watches files and rebuilds on change. The `_data/config.yml` snapshot in the repo is the last-known-good from CI; local builds use that snapshot rather than re-fetching `/api/config/public` (to avoid every dev needing the API to be reachable). To test with a fresh fetch:

```bash
bash scripts/fetch-config.sh    # writes _data/config.yml from api.ezley.com
bundle exec jekyll serve
```

## Initial GitHub setup (one-time)

The scaffold is in `/Users/marctalcott/Documents/Projects/ezley-market/ezley-marketing-site/` but is not yet a Git repo. To bring it up on GitHub:

```bash
cd ezley-marketing-site
git init
git add .
git commit -m "Initial Jekyll + Minimal Mistakes scaffold"

# Create the repo on GitHub (UI or gh CLI):
gh repo create <your-org>/ezley-marketing-site --public --source=. --remote=origin --push
```

In GitHub repo settings:

1. **Pages** → Source: GitHub Actions (not "Deploy from a branch" — the included Action handles deployment).
2. **Pages** → Custom domain: `ezley.com`. Click "Save", check "Enforce HTTPS" once the cert provisions (~10 min after DNS resolves).
3. **Actions** → Allow GitHub Actions to read and write contents (for the `gh-pages` push).

In your domain registrar's DNS panel for `ezley.com`:

```
Type  Host  Value                  TTL
A     @     185.199.108.153        3600
A     @     185.199.109.153        3600
A     @     185.199.110.153        3600
A     @     185.199.111.153        3600
AAAA  @     2606:50c0:8000::153    3600
AAAA  @     2606:50c0:8001::153    3600
AAAA  @     2606:50c0:8002::153    3600
AAAA  @     2606:50c0:8003::153    3600
```

DNS propagation can take 0–24 hours; the Let's Encrypt cert provisioning then takes ~10 min after DNS resolves.

## Deploying

After initial setup, deployment is automatic on every push to `main`:

```bash
git push origin main
```

The GitHub Action at `.github/workflows/deploy.yml`:
1. Checks out the source
2. Sets up Ruby and caches gems
3. Fetches `https://api.ezley.com/api/config/public` (falls back to existing snapshot on failure)
4. Builds with `jekyll build`
5. Deploys `_site/` to GitHub Pages

Monitor the deploy at `https://github.com/<your-org>/ezley-marketing-site/actions`.

A daily cron rebuild (`schedule: cron: "0 6 * * *"`) refreshes the config snapshot even when no commits land, honoring NFR-MKT-008's 24-hour staleness cap.

## Updating site content

| What to change | Where |
|---|---|
| Marketing copy on any page (hero, sections, CTAs) | `_data/copy/en-US.yml` |
| Pricing rates displayed on /pricing | Don't edit directly. Update `Pricing:*` in `ezley-market-net-api`'s appsettings; next build picks up the new values. |
| Auto-release window default | Same — comes from `/api/config/public` |
| Personas / principles / D-XXX wording | Update `ezley-docs/` first, then update the relevant `_data/content.yml` quotes here |
| Theme color | `_sass/ezley-tokens.scss` — only after a corresponding change in `ezley-market-ui-react`'s MUI theme |
| Add a new page | New `.md` in `_pages/` with `permalink:` front-matter, plus a sitemap entry (auto-generated) |

## Running CI checks locally

```bash
# Pricing contract test — asserts rendered HTML matches _data/config.yml rate values
bundle exec jekyll build
ruby scripts/pricing-contract-test.rb

# P-14 grep — forbidden cost-itemization terms in seller-facing copy
ruby scripts/p14-grep.rb _site/pricing/

# Glossary discipline — forbidden synonyms
ruby scripts/glossary-grep.rb _site/

# axe-core a11y scan (requires Node + axe-cli)
npx @axe-core/cli http://localhost:4000/ http://localhost:4000/tractors http://localhost:4000/how-it-works http://localhost:4000/pricing http://localhost:4000/trust http://localhost:4000/agents

# Lighthouse perf scan (requires Node + lhci)
npx @lhci/cli@latest autorun --config=lighthouserc.json
```

All of these run in CI automatically on every PR (see `.github/workflows/ci.yml`).

## Production smoke test

After a deploy completes, the workflow runs:

```bash
curl -sSf https://ezley.com/ > /dev/null && echo "OK"
```

If this fails, the workflow surfaces the error in the Action log.

## Common troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Site shows GitHub 404 page | DNS not propagated, or `CNAME` file missing/wrong | `dig ezley.com` should return 185.199.108.153 etc. Check `CNAME` file says `ezley.com` exactly. |
| Site loads but shows raw markdown | Build failed silently | Check Actions tab; rerun the workflow |
| Pricing page shows old rates | Daily cron hasn't run since the API change | Trigger `workflow_dispatch` manually from the Actions tab, OR push a no-op commit |
| /llms.txt 404 | Sitemap/robots/llms files weren't copied — Jekyll's `include` list | Verify `_config.yml`'s `include:` includes the dotfiles you want served |
| Accessibility CI fails | A theme update introduced a regression, OR new content has missing alt/heading-skip | Run `npx @axe-core/cli` locally to identify the page, then fix the source markdown |
