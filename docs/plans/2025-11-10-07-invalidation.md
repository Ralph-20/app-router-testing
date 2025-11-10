# Plan 07: Cache Invalidation Dashboard

**Date:** 2025-11-10
**Goal:** Comprehensive dashboard demonstrating all cache invalidation methods

---

## Overview

Consolidates all cache invalidation strategies in one interactive dashboard.

**Invalidation Methods:**
1. `revalidatePath()` - Path-based invalidation (Data Cache + Full Route Cache)
2. `revalidateTag()` - Tag-based invalidation (stale-while-revalidate)
3. `updateTag()` - Immediate expiration (read-your-own-writes)
4. `router.refresh()` - Client-side cache clear
5. Time-based - Automatic revalidation after interval

---

## Implementation Steps

### Step 1: Create Invalidation Dashboard Page

**Location:** `my-app/app/(dashboard)/invalidation/page.tsx`

```typescript
import { Suspense } from 'react';
import { RevalidatePathButton } from './components/RevalidatePathButton';
import { RevalidateTagButton } from './components/RevalidateTagButton';
import { UpdateTagButton } from './components/UpdateTagButton';
import { RouterRefreshButton } from './components/RouterRefreshButton';

export default function InvalidationPage() {
  return (
    <div className="max-w-6xl mx-auto space-y-8">
      <section>
        <h2 className="text-3xl font-bold mb-4">Cache Invalidation Methods</h2>
        <p className="text-gray-600 dark:text-gray-400">
          Interactive dashboard showing all cache invalidation strategies in Next.js 16
        </p>
      </section>

      {/* revalidatePath() */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">1. revalidatePath()</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Invalidates Data Cache + Full Route Cache for specific path.
          Works in Server Actions and Route Handlers.
        </p>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <PathCachedCard path="/data-cache" />
          </Suspense>
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <PathCachedCard path="/full-route-cache" />
          </Suspense>
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <PathCachedCard path="/cache-components" />
          </Suspense>
        </div>

        <div className="flex flex-wrap gap-3 p-4 bg-gray-50 dark:bg-gray-900 rounded-md">
          <RevalidatePathButton path="/data-cache" label="Revalidate /data-cache" />
          <RevalidatePathButton path="/full-route-cache" label="Revalidate /full-route-cache" />
          <RevalidatePathButton path="/cache-components" label="Revalidate /cache-components" />
        </div>

        <div className="mt-4 text-xs text-gray-500 font-mono p-3 bg-gray-100 dark:bg-gray-900 rounded">
          revalidatePath('/data-cache') → Clears Data Cache + Full Route Cache for that path
        </div>
      </section>

      {/* revalidateTag() */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">2. revalidateTag()</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Invalidates all cache entries with specific tag. Uses stale-while-revalidate
          (serves old content while fetching fresh). Works in Server Actions and Route Handlers.
        </p>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <TagCachedCard tag="posts" postId={1} />
          </Suspense>
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <TagCachedCard tag="users" postId={2} />
          </Suspense>
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <TagCachedCard tag="comments" postId={3} />
          </Suspense>
        </div>

        <div className="flex flex-wrap gap-3 p-4 bg-gray-50 dark:bg-gray-900 rounded-md">
          <RevalidateTagButton tag="posts" label="Revalidate 'posts'" />
          <RevalidateTagButton tag="users" label="Revalidate 'users'" />
          <RevalidateTagButton tag="comments" label="Revalidate 'comments'" />
        </div>

        <div className="mt-4 text-xs text-gray-500 font-mono p-3 bg-gray-100 dark:bg-gray-900 rounded">
          revalidateTag('posts', 'max') → Marks stale, serves old while fetching new
        </div>
      </section>

      {/* updateTag() */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">3. updateTag()</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Immediately expires cache (no stale content served). For read-your-own-writes
          scenarios. <strong>Only works in Server Actions</strong> (not Route Handlers).
        </p>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <UpdateTagCard tag="user-profile" label="User Profile" />
          </Suspense>
          <Suspense fallback={<div className="h-24 bg-gray-100 dark:bg-gray-900 rounded animate-pulse" />}>
            <UpdateTagCard tag="user-settings" label="User Settings" />
          </Suspense>
        </div>

        <div className="flex flex-wrap gap-3 p-4 bg-gray-50 dark:bg-gray-900 rounded-md">
          <UpdateTagButton tag="user-profile" label="Update 'user-profile'" />
          <UpdateTagButton tag="user-settings" label="Update 'user-settings'" />
        </div>

        <div className="mt-4 text-xs text-gray-500 font-mono p-3 bg-gray-100 dark:bg-gray-900 rounded">
          updateTag('user-profile') → Immediate expiration, no stale content
        </div>
      </section>

      {/* router.refresh() */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">4. router.refresh()</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Client-side method that clears Router Cache and makes new server request.
          Does NOT clear Data Cache or Full Route Cache (server-side caches unaffected).
        </p>

        <RouterRefreshButton />

        <div className="mt-4 text-xs text-gray-500 font-mono p-3 bg-gray-100 dark:bg-gray-900 rounded">
          router.refresh() → Clears client Router Cache only
        </div>
      </section>

      {/* Comparison Table */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Method Comparison</h3>
        <div className="overflow-x-auto">
          <table className="w-full text-sm">
            <thead className="bg-gray-50 dark:bg-gray-900">
              <tr>
                <th className="p-3 text-left">Method</th>
                <th className="p-3 text-left">Scope</th>
                <th className="p-3 text-left">Behavior</th>
                <th className="p-3 text-left">Where</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-gray-200 dark:divide-gray-700">
              <tr>
                <td className="p-3 font-mono text-xs">revalidatePath()</td>
                <td className="p-3">Data + Full Route</td>
                <td className="p-3">Stale-while-revalidate</td>
                <td className="p-3">Server Actions, Route Handlers</td>
              </tr>
              <tr>
                <td className="p-3 font-mono text-xs">revalidateTag()</td>
                <td className="p-3">All tagged entries</td>
                <td className="p-3">Stale-while-revalidate</td>
                <td className="p-3">Server Actions, Route Handlers</td>
              </tr>
              <tr>
                <td className="p-3 font-mono text-xs">updateTag()</td>
                <td className="p-3">All tagged entries</td>
                <td className="p-3">Immediate expiration</td>
                <td className="p-3">Server Actions only</td>
              </tr>
              <tr>
                <td className="p-3 font-mono text-xs">router.refresh()</td>
                <td className="p-3">Router Cache</td>
                <td className="p-3">Immediate clear</td>
                <td className="p-3">Client Components</td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </div>
  );
}

// Cached component showing path-based caching
async function PathCachedCard({ path }: { path: string }) {
  const timestamp = new Date().toISOString();
  return (
    <div className="p-4 border rounded-lg bg-blue-50 dark:bg-blue-950">
      <div className="text-sm font-semibold mb-2">{path}</div>
      <div className="text-xs text-gray-600 dark:text-gray-400">
        Cached: {timestamp.split('T')[1].split('.')[0]}
      </div>
    </div>
  );
}

// Cached component with tag
async function TagCachedCard({ tag, postId }: { tag: string; postId: number }) {
  const timestamp = new Date().toISOString();
  const data = await fetch(`https://jsonplaceholder.typicode.com/posts/${postId}`, {
    cache: 'force-cache',
    next: { tags: [tag] },
  }).then((r) => r.json());

  return (
    <div className="p-4 border rounded-lg bg-green-50 dark:bg-green-950">
      <div className="text-xs px-2 py-1 bg-green-200 dark:bg-green-800 rounded w-fit mb-2">
        {tag}
      </div>
      <div className="text-sm font-semibold mb-1">{data.title.substring(0, 30)}...</div>
      <div className="text-xs text-gray-600 dark:text-gray-400">
        {timestamp.split('T')[1].split('.')[0]}
      </div>
    </div>
  );
}

// Component for updateTag demo
async function UpdateTagCard({ tag, label }: { tag: string; label: string }) {
  const timestamp = new Date().toISOString();
  return (
    <div className="p-4 border rounded-lg bg-purple-50 dark:bg-purple-950">
      <div className="text-xs px-2 py-1 bg-purple-200 dark:bg-purple-800 rounded w-fit mb-2">
        {tag}
      </div>
      <div className="text-sm font-semibold mb-1">{label}</div>
      <div className="text-xs text-gray-600 dark:text-gray-400">
        {timestamp.split('T')[1].split('.')[0]}
      </div>
    </div>
  );
}
```

### Step 2: Create Button Components

**Location:** `my-app/app/(dashboard)/invalidation/components/` (create all)

Each button component follows similar pattern - calling appropriate Server Action.

**RevalidatePathButton.tsx:**
```typescript
'use client';

import { useState } from 'react';
import { revalidatePathAction } from '@/app/actions/cache-actions';

export function RevalidatePathButton({ path, label }: { path: string; label: string }) {
  const [loading, setLoading] = useState(false);
  const [last, setLast] = useState<string | null>(null);

  const handleClick = async () => {
    setLoading(true);
    await revalidatePathAction(path);
    setLast(new Date().toLocaleTimeString());
    setLoading(false);
  };

  return (
    <button
      onClick={handleClick}
      disabled={loading}
      className="px-3 py-2 bg-blue-600 hover:bg-blue-700 text-white text-sm rounded disabled:bg-gray-400"
    >
      {loading ? '...' : label}
      {last && <span className="ml-2 text-xs opacity-75">({last})</span>}
    </button>
  );
}
```

**RevalidateTagButton.tsx, UpdateTagButton.tsx, RouterRefreshButton.tsx** follow same pattern.

---

## Expected Results

- All invalidation methods work as expected
- Timestamps update after invalidation
- Clear visual distinction between stale-while-revalidate vs immediate
- Comparison table helps understand differences

---

## Testing Steps

1. Visit `/invalidation`
2. Note all timestamps
3. Click each invalidation button
4. Refresh page - see updated timestamps
5. Compare behaviors (stale vs immediate)

---

## Key Concepts

- Different methods for different use cases
- Path vs tag-based invalidation
- Server-side vs client-side clearing
- Stale-while-revalidate vs immediate expiration

---

**Status:** Ready for implementation
**Time:** 30-40 minutes
