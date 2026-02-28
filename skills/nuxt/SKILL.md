# Nuxt 4 Skill

## Overview
Nuxt 4 application architecture, composables, and modern Vue.js full-stack patterns.

## Core Concepts

### 1. Application Structure
```
app/
├── assets/             # Static assets processed by Vite
├── components/         # Auto-imported Vue components
├── composables/        # Auto-imported composables
├── layouts/            # Page layouts
├── middleware/         # Route middleware
├── pages/              # File-based routing
├── plugins/            # Nuxt plugins
├── utils/              # Auto-imported utilities
└── app.vue             # Root component
server/
├── api/                # Server API routes
├── middleware/          # Server middleware
├── plugins/            # Server plugins
└── utils/              # Server utilities
public/                 # Static files (served as-is)
nuxt.config.ts          # Nuxt configuration
```

### 2. File-Based Routing
```
pages/
├── index.vue           → /
├── about.vue           → /about
├── blog/
│   ├── index.vue       → /blog
│   └── [slug].vue      → /blog/:slug
├── users/
│   └── [id]/
│       ├── index.vue   → /users/:id
│       └── posts.vue   → /users/:id/posts
└── [...slug].vue       → catch-all route
```

```vue
<!-- pages/blog/[slug].vue -->
<script setup lang="ts">
const route = useRoute()
const { data: post } = await useAsyncData('post', () =>
  $fetch(`/api/posts/${route.params.slug}`)
)
</script>

<template>
  <article>
    <h1>{{ post?.title }}</h1>
    <div v-html="post?.body" />
  </article>
</template>
```

### 3. Data Fetching
```vue
<script setup lang="ts">
// useFetch - SSR-friendly, deduplicates requests
const { data, status, error, refresh } = await useFetch('/api/posts', {
  query: { page: 1, limit: 10 },
  pick: ['id', 'title'],
})

// useAsyncData - for custom async logic
const { data: user } = await useAsyncData('user', async () => {
  const profile = await getProfile()
  const settings = await getSettings()
  return { ...profile, settings }
})

// $fetch - for client-side or server-side fetching without SSR hydration
async function submitForm(data: FormData) {
  await $fetch('/api/submit', { method: 'POST', body: data })
}
</script>
```

### 4. Server API Routes
```ts
// server/api/posts/index.get.ts
export default defineEventHandler(async (event) => {
  const query = getQuery(event)
  const posts = await db.posts.findMany({
    take: Number(query.limit) || 10,
    skip: Number(query.page - 1) * 10,
  })
  return posts
})

// server/api/posts/[id].ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const post = await db.posts.findUnique({ where: { id } })
  if (!post) throw createError({ statusCode: 404, message: 'Post not found' })
  return post
})

// POST with body validation
export default defineEventHandler(async (event) => {
  const body = await readValidatedBody(event, z.object({
    title: z.string().min(1),
    body: z.string(),
  }).parse)

  return db.posts.create({ data: body })
})
```

### 5. Composables
```ts
// composables/useAuth.ts
export function useAuth() {
  const user = useState<User | null>('auth.user', () => null)
  const loggedIn = computed(() => !!user.value)

  async function login(credentials: Credentials) {
    user.value = await $fetch('/api/auth/login', {
      method: 'POST',
      body: credentials,
    })
  }

  async function logout() {
    await $fetch('/api/auth/logout', { method: 'POST' })
    user.value = null
    await navigateTo('/login')
  }

  return { user, loggedIn, login, logout }
}

// composables/usePosts.ts
export function usePosts() {
  return useAsyncData('posts', () => $fetch('/api/posts'))
}
```

### 6. Layouts
```vue
<!-- layouts/default.vue -->
<template>
  <div>
    <AppHeader />
    <main>
      <slot />
    </main>
    <AppFooter />
  </div>
</template>

<!-- Using a layout in a page -->
<!-- pages/dashboard.vue -->
<script setup lang="ts">
definePageMeta({ layout: 'dashboard' })
</script>
```

### 7. Middleware
```ts
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to) => {
  const { loggedIn } = useAuth()
  if (!loggedIn.value) {
    return navigateTo(`/login?redirect=${to.fullPath}`)
  }
})

// Apply to a page
definePageMeta({ middleware: ['auth'] })

// Server-side middleware
// server/middleware/logger.ts
export default defineEventHandler((event) => {
  console.log(`[${new Date().toISOString()}] ${event.method} ${event.path}`)
})
```

### 8. State Management
```ts
// useState for SSR-safe shared state
const counter = useState('counter', () => 0)

// Pinia store (recommended for complex state)
// stores/cart.ts
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])
  const total = computed(() => items.value.reduce((sum, i) => sum + i.price, 0))

  function addItem(item: CartItem) {
    items.value.push(item)
  }

  function removeItem(id: string) {
    items.value = items.value.filter(i => i.id !== id)
  }

  return { items, total, addItem, removeItem }
})
```

## Nuxt 4 Features

### New `app/` Directory
Nuxt 4 consolidates source files under `app/`, separating application code from server and configuration:
```ts
// nuxt.config.ts
export default defineNuxtConfig({
  future: { compatibilityVersion: 4 },
})
```

### Improved Data Fetching
`useAsyncData` and `useFetch` now deduplicate requests by key automatically and support `getCachedData` for custom caching strategies:
```ts
const { data } = await useAsyncData('posts', () => $fetch('/api/posts'), {
  getCachedData: (key) => useNuxtApp().payload.data[key],
})
```

### Typed Route Params
```ts
// Fully typed params with typed-router
const route = useRoute('blog-slug')
console.log(route.params.slug) // string (typed)
```

### nuxt.config.ts Essentials
```ts
export default defineNuxtConfig({
  compatibilityDate: '2025-01-01',
  devtools: { enabled: true },

  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt',
    '@nuxt/image',
    '@nuxtjs/i18n',
  ],

  runtimeConfig: {
    // Server-only secrets
    databaseUrl: process.env.DATABASE_URL,
    // Exposed to client
    public: {
      apiBase: process.env.API_BASE_URL || '/api',
    },
  },

  nitro: {
    preset: 'node-server', // or 'vercel', 'cloudflare', etc.
  },
})
```

## Best Practices
- Use `useAsyncData` keys that match the route to avoid data collisions
- Prefer `useFetch` for simple API calls, `useAsyncData` for complex logic
- Use `useState` for SSR-safe shared state instead of `ref` at module level
- Apply `definePageMeta` for layout, middleware, and route meta in one place
- Leverage auto-imports — no need to manually import composables or components
- Use `server/api/` routes instead of a separate Express/Fastify backend for BFF patterns
- Enable `devtools` during development for component inspector and data visualization
- Use `$fetch` with `useRequestFetch()` on the server to forward cookies/headers
