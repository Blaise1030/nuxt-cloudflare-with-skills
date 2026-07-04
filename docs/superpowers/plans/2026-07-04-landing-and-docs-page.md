# Landing + Docs Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the placeholder homepage with a T3-style marketing landing page, add a `/docs` page backed by `@nuxt/content`, and remove the now-unlinked `/notes` demo.

**Architecture:** Static landing page (`pages/index.vue`) with hero/stack-grid/CLI-snippet sections. `@nuxt/content` added to `apps/web` for a single markdown getting-started doc, rendered by a catch-all `pages/docs/[...slug].vue` via `<ContentRenderer>`. Notes demo (page, composable, component, server routes, and their tests) deleted since nothing links to it anymore.

**Tech Stack:** Nuxt 4, shadcn-vue (Button), Tailwind CSS v4, `@nuxt/content`.

Spec: `docs/superpowers/specs/2026-07-04-landing-and-docs-page-design.md`

---

### Task 1: Remove the notes demo

**Files:**
- Delete: `apps/web/app/pages/notes/index.vue`
- Delete: `apps/web/app/composables/useNotes.ts`
- Delete: `apps/web/app/composables/useNotes.test.ts`
- Delete: `apps/web/app/components/NoteCard.vue`
- Delete: `apps/web/app/components/NoteCard.test.ts`
- Delete: `apps/web/server/api/notes.get.ts`
- Delete: `apps/web/server/api/notes.post.ts`
- Delete: `apps/web/server/utils/notes-store.ts`
- Delete: `apps/web/server/utils/notes-store.test.ts`

- [ ] **Step 1: Delete the files**

```bash
cd apps/web
rm app/pages/notes/index.vue
rmdir app/pages/notes
rm app/composables/useNotes.ts app/composables/useNotes.test.ts
rm app/components/NoteCard.vue app/components/NoteCard.test.ts
rm server/api/notes.get.ts server/api/notes.post.ts
rm server/utils/notes-store.ts server/utils/notes-store.test.ts
```

- [ ] **Step 2: Run the test suite to confirm nothing else references the deleted files**

Run: `pnpm --filter @appstarter/web test`
Expected: PASS, 0 failures (no leftover imports of the deleted files)

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Remove notes demo page and its backing composable/server routes"
```

---

### Task 2: Add `@nuxt/content` and the getting-started doc

**Files:**
- Modify: `apps/web/package.json` (new dependency)
- Modify: `apps/web/nuxt.config.ts`
- Create: `apps/web/content.config.ts`
- Create: `apps/web/content/1.getting-started.md`

- [ ] **Step 1: Install the dependency**

```bash
cd apps/web
pnpm add @nuxt/content
```

- [ ] **Step 2: Register the module in `nuxt.config.ts`**

Edit `apps/web/nuxt.config.ts`, change:

```ts
  modules: ['shadcn-nuxt', '@nuxt/eslint'],
```

to:

```ts
  modules: ['shadcn-nuxt', '@nuxt/eslint', '@nuxt/content'],
```

- [ ] **Step 3: Define the content collection**

Create `apps/web/content.config.ts`:

```ts
import { defineCollection, defineContentConfig } from '@nuxt/content'

export default defineContentConfig({
  collections: {
    content: defineCollection({
      type: 'page',
      source: '**/*.md',
    }),
  },
})
```

- [ ] **Step 4: Write the getting-started doc**

Create `apps/web/content/1.getting-started.md`:

```markdown
---
title: Getting Started
---

# Getting Started

## Install

Clone the repo and install dependencies from the workspace root:

```bash
pnpm install
```

## Develop

Run the web app in dev mode:

```bash
pnpm --filter @appstarter/web dev
```

The app is served at `http://localhost:3000`.

## Project structure

- `apps/web` — the Nuxt app (pages, components, server routes)
- `packages/shared` — code shared across apps in the monorepo
- `.agents/skills` — Claude Code skills bundled with this starter

## Deploy

The app targets Cloudflare Workers via the `cloudflare_module` nitro preset:

```bash
pnpm --filter @appstarter/web deploy:preview
pnpm --filter @appstarter/web deploy:production
```

## Adding a feature

This starter ships with Claude Code skills to scaffold common needs. Ask Claude
to use the `bootstrap-services` skill when you need a database, auth, file
storage, or background jobs — it interviews you first, then wires up only
what you asked for.
```

- [ ] **Step 5: Run typecheck to confirm the module wires up cleanly**

Run: `pnpm --filter @appstarter/web typecheck`
Expected: PASS, no errors

- [ ] **Step 6: Commit**

```bash
git add apps/web/package.json apps/web/pnpm-lock.yaml apps/web/nuxt.config.ts apps/web/content.config.ts apps/web/content
git commit -m "Add @nuxt/content and a getting-started doc"
```

(If the lockfile changed at the repo root instead of `apps/web`, add `pnpm-lock.yaml` from the root instead.)

---

### Task 3: Build the `/docs` page

**Files:**
- Create: `apps/web/app/pages/docs/[...slug].vue`

- [ ] **Step 1: Write the catch-all docs page**

Create `apps/web/app/pages/docs/[...slug].vue`:

```vue
<script setup lang="ts">
const route = useRoute()
const path = computed(() => `/docs${route.path.replace(/^\/docs/, '') || '/getting-started'}`)

const { data: page } = await useAsyncData(path.value, () =>
  queryCollection('content').path(path.value).first(),
)

const { data: allPages } = await useAsyncData('docs-nav', () =>
  queryCollection('content').all(),
)
</script>

<template>
  <div class="mx-auto flex max-w-4xl gap-8 p-6">
    <nav class="w-40 shrink-0">
      <ul class="flex flex-col gap-2 text-sm">
        <li v-for="item in allPages" :key="item.path">
          <NuxtLink
            :to="`/docs${item.path.replace(/^\/[0-9]+\./, '/')}`"
            class="text-muted-foreground hover:text-foreground hover:underline"
          >
            {{ item.title }}
          </NuxtLink>
        </li>
      </ul>
    </nav>
    <article class="prose dark:prose-invert max-w-none">
      <ContentRenderer v-if="page" :value="page" />
      <p v-else class="text-muted-foreground">
        Page not found.
      </p>
    </article>
  </div>
</template>
```

- [ ] **Step 2: Confirm the doc renders**

Run: `pnpm --filter @appstarter/web dev`
Visit `http://localhost:3000/docs` in a browser — expect the getting-started content rendered with a one-item sidebar nav. Stop the dev server after checking.

- [ ] **Step 3: Commit**

```bash
git add apps/web/app/pages/docs
git commit -m "Add /docs page rendering nuxt/content pages"
```

---

### Task 4: Build the landing page

**Files:**
- Modify: `apps/web/app/pages/index.vue`

- [ ] **Step 1: Replace the placeholder homepage**

Replace the full contents of `apps/web/app/pages/index.vue` with:

```vue
<script setup lang="ts">
import { appName } from '@appstarter/shared'

const stack = [
  { name: 'Nuxt', blurb: 'Vue meta-framework: file-based routing, SSR, and server routes out of the box.' },
  { name: 'shadcn-vue', blurb: 'Unstyled, ownable UI primitives — no design system lock-in.' },
  { name: 'Cloudflare Workers', blurb: 'Deploys via nitro\'s cloudflare_module preset, no separate infra to provision.' },
  { name: 'pnpm monorepo', blurb: 'Share code between apps via workspace packages.' },
  { name: 'Vitest', blurb: 'Fast, Nuxt-aware unit tests, wired up from the start.' },
  { name: 'Claude Code skills', blurb: 'Bundled skills scaffold databases, auth, storage, and queues on request.' },
]
</script>

<template>
  <div class="flex min-h-svh flex-col">
    <main class="flex flex-1 flex-col items-center justify-center gap-10 p-6 text-center">
      <div class="flex flex-col items-center gap-3">
        <h1 class="text-3xl font-semibold tracking-tight">
          {{ appName }}
        </h1>
        <p class="text-muted-foreground max-w-md text-balance">
          Ship business apps at AI speed. A Nuxt + Cloudflare starter with the stack and skills already wired up.
        </p>
      </div>

      <div class="flex gap-3">
        <Button as-child>
          <NuxtLink to="/docs">
            Read the docs
          </NuxtLink>
        </Button>
        <Button as-child variant="outline">
          <a href="https://github.com/Blaise1030/appstarter-with-skills" target="_blank" rel="noopener">
            GitHub
          </a>
        </Button>
      </div>

      <pre class="bg-muted w-full max-w-md rounded-lg p-4 text-left text-sm"><code>git clone https://github.com/Blaise1030/appstarter-with-skills
pnpm install
pnpm --filter @appstarter/web dev</code></pre>

      <div class="grid w-full max-w-3xl grid-cols-1 gap-4 text-left sm:grid-cols-2 lg:grid-cols-3">
        <div v-for="item in stack" :key="item.name" class="rounded-lg border p-4">
          <p class="font-medium">
            {{ item.name }}
          </p>
          <p class="text-muted-foreground text-sm">
            {{ item.blurb }}
          </p>
        </div>
      </div>
    </main>

    <footer class="border-t p-6 text-center">
      <a
        href="https://github.com/Blaise1030/appstarter-with-skills"
        target="_blank"
        rel="noopener"
        class="text-muted-foreground text-sm hover:underline"
      >
        View on GitHub
      </a>
    </footer>
  </div>
</template>
```

- [ ] **Step 2: Confirm it renders and lints clean**

Run: `pnpm --filter @appstarter/web lint`
Expected: PASS, no errors

Run: `pnpm --filter @appstarter/web dev`
Visit `http://localhost:3000` — expect hero, CTA buttons, code snippet, and stack grid. Stop the dev server after checking.

- [ ] **Step 3: Commit**

```bash
git add apps/web/app/pages/index.vue
git commit -m "Replace placeholder homepage with T3-style landing page"
```

---

### Task 5: Full verification pass

**Files:** none (verification only)

- [ ] **Step 1: Run the full web app test suite**

Run: `pnpm --filter @appstarter/web test`
Expected: PASS

- [ ] **Step 2: Run typecheck**

Run: `pnpm --filter @appstarter/web typecheck`
Expected: PASS

- [ ] **Step 3: Run a production build**

Run: `pnpm --filter @appstarter/web build`
Expected: PASS, no build errors

- [ ] **Step 4: Commit if the build step touched any generated/lockfile output**

```bash
git add -A
git status
```

If clean, no commit needed — this task is verification-only.
