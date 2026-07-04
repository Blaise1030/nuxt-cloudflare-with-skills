# appstarter-with-skills

A reusable Nuxt starter template: a pnpm monorepo with a Nuxt 4 app, a shared package, Cloudflare Workers deployment, and a curated set of agent skills for Claude Code baked in.

## What's in the box

- **`apps/web`** — Nuxt 4 app with Vue, Tailwind CSS 4, shadcn-vue/reka-ui components, and a small notes example (`server/api`, `app/pages/notes`) demonstrating an API route backed by a typed store.
- **`packages/shared`** — workspace package (`@appstarter/shared`) for code shared across apps.
- **`.agents/skills`** — a locked set of Claude Code skills (Cloudflare, Nuxt, TDD, domain modeling, etc.) pulled in via `skills-lock.json`, so agents working in this repo get consistent conventions out of the box.
- **Cloudflare Workers deployment** — `apps/web/wrangler.jsonc` defines `preview` (per-PR preview URLs) and `production` environments; `.github/workflows/cd.yml` wires these into CI.
- **Release automation** — `release-please` config for versioned releases.

## Getting started

```bash
pnpm install
pnpm dev          # starts apps/web on http://localhost:3000
```

Other useful scripts (run from the repo root, fan out to all packages):

```bash
pnpm build        # pnpm -r build
pnpm lint         # pnpm -r --if-present lint
pnpm typecheck    # pnpm -r --if-present typecheck
pnpm test         # pnpm -r --if-present test
```

## Deploying

```bash
pnpm --filter @appstarter/web deploy:preview
pnpm --filter @appstarter/web deploy:production
```

## CI/CD

Two GitHub Actions workflows run against this repo:

- **CI** (`.github/workflows/ci.yml`) — on every push/PR to `main`: install, lint, typecheck, test, and build the whole workspace.
- **CD** (`.github/workflows/cd.yml`) — deploys to Cloudflare Workers:
  - On each PR, uploads a Workers preview version (`wrangler versions upload --env preview`) and comments the preview URL on the PR.
  - On a published GitHub release, deploys the release tag to the production Worker (`wrangler deploy --env production`).

## Using this as a template

Rename `@appstarter/*` packages, swap out the notes example in `apps/web`, and adjust `apps/web/wrangler.jsonc` worker names for your project. The `.agents/skills` directory and `skills-lock.json` can be kept as-is or pruned to the skills relevant to your stack.
