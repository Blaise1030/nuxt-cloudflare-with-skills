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
          <a href="https://github.com/Blaise1030/nuxt-cloudflare-with-skills" target="_blank" rel="noopener">
            GitHub
          </a>
        </Button>
      </div>

      <pre class="bg-muted w-full max-w-md rounded-lg p-4 text-left text-sm"><code>git clone https://github.com/Blaise1030/nuxt-cloudflare-with-skills
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
        href="https://github.com/Blaise1030/nuxt-cloudflare-with-skills"
        target="_blank"
        rel="noopener"
        class="text-muted-foreground text-sm hover:underline"
      >
        View on GitHub
      </a>
    </footer>
  </div>
</template>
