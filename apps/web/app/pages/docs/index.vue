<script setup lang="ts">
const { data: page } = await useAsyncData('getting-started', () =>
  queryCollection('content').first(),
)
</script>

<template>
  <div class="mx-auto max-w-3xl p-6">
    <article class="prose dark:prose-invert max-w-none">
      <ContentRenderer v-if="page" :value="page" />
      <p v-else class="text-muted-foreground">
        Page not found.
      </p>
    </article>
  </div>
</template>
