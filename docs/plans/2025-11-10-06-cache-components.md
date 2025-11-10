# Plan 06: Cache Components Demo

**Date:** 2025-11-10
**Goal:** Demonstrate Next.js 16 Cache Components with 'use cache' directive

---

## Overview

Cache Components is **NEW in Next.js 16** - a more granular caching approach using `'use cache'` directive.

**Key Features:**
- Works with ANY server-side I/O (DB, APIs, files) not just fetch
- File/component/function-level caching (not just route-level)
- `cacheLife()` profiles for duration control
- `cacheTag()` for targeted invalidation
- `updateTag()` for immediate expiration (read-your-own-writes)

**Important:** With Cache Components enabled, routes are **dynamic by default** - must opt-in to caching.

---

## Implementation Steps

### Step 1: Create Cache Components Main Page

**Location:** `my-app/app/(dashboard)/cache-components/page.tsx`

```typescript
import { Suspense } from 'react';
import { InvalidateButton } from './components/InvalidateButton';

export default function CacheComponentsPage() {
  return (
    <div className="max-w-6xl mx-auto space-y-8">
      <section>
        <h2 className="text-3xl font-bold mb-4">Cache Components ('use cache')</h2>
        <div className="p-6 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
          <p className="text-sm mb-3">
            <strong>NEW in Next.js 16:</strong> Granular caching with 'use cache' directive.
            Cache individual components, functions, or pages with configurable lifetimes.
          </p>
          <ul className="text-sm space-y-1 text-gray-600 dark:text-gray-400">
            <li>• Works with ALL server I/O (not just fetch)</li>
            <li>• Component/function-level caching</li>
            <li>• Configurable via cacheLife() profiles</li>
            <li>• Tagged invalidation via cacheTag()</li>
          </ul>
        </div>
      </section>

      <section>
        <h3 className="text-xl font-semibold mb-4">cacheLife() Profiles</h3>
        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <Suspense fallback={<LoadingCard />}>
            <CachedComponentSeconds />
          </Suspense>
          <Suspense fallback={<LoadingCard />}>
            <CachedComponentMinutes />
          </Suspense>
          <Suspense fallback={<LoadingCard />}>
            <CachedComponentHours />
          </Suspense>
        </div>
      </section>

      <section>
        <h3 className="text-xl font-semibold mb-4">Tagged Components for Invalidation</h3>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Suspense fallback={<LoadingCard />}>
            <TaggedComponent tag="component-a" label="Component A" />
          </Suspense>
          <Suspense fallback={<LoadingCard />}>
            <TaggedComponent tag="component-b" label="Component B" />
          </Suspense>
        </div>
        <div className="flex gap-3">
          <InvalidateButton tag="component-a" label="Invalidate A" />
          <InvalidateButton tag="component-b" label="Invalidate B" />
        </div>
      </section>

      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">Code Example</h3>
        <pre className="text-xs bg-gray-900 text-gray-100 p-4 rounded overflow-x-auto">
{`// Component with 'use cache' directive
async function CachedComponent() {
  'use cache'; // Mark this component as cacheable
  cacheLife('minutes'); // Cache for 5 minutes
  cacheTag('my-tag'); // Tag for invalidation

  const data = await expensiveOperation();
  return <div>{data}</div>;
}

// Later: Invalidate via Server Action
revalidateTag('my-tag', 'max'); // Stale-while-revalidate
// OR
updateTag('my-tag'); // Immediate expiration`}
        </pre>
      </section>
    </div>
  );
}

function LoadingCard() {
  return <div className="p-4 border rounded-lg animate-pulse h-32 bg-gray-50 dark:bg-gray-900"></div>;
}

// Component cached for 30 seconds
async function CachedComponentSeconds() {
  'use cache';
  const { cacheLife, cacheTag } = await import('next/cache');

  // Cache for 30 seconds (stale: 30s, revalidate: 1s, expire: 1min)
  cacheLife('seconds');
  cacheTag('seconds-cache');

  const timestamp = new Date().toISOString();

  // Simulate expensive operation
  await new Promise((resolve) => setTimeout(resolve, 100));

  return (
    <div className="p-4 border border-purple-200 dark:border-purple-800 rounded-lg">
      <div className="flex justify-between items-center mb-3">
        <h4 className="font-semibold">cacheLife('seconds')</h4>
        <span className="text-xs px-2 py-1 bg-purple-100 dark:bg-purple-900 rounded">30s</span>
      </div>
      <p className="text-xs text-gray-600 dark:text-gray-400 mb-2">
        Cached at: {timestamp}
      </p>
      <p className="text-xs text-gray-500">
        Stale: 30s | Revalidate: 1s | Expire: 1min
      </p>
    </div>
  );
}

// Component cached for 5 minutes
async function CachedComponentMinutes() {
  'use cache';
  const { cacheLife, cacheTag } = await import('next/cache');

  // Cache for 5 minutes (stale: 5min, revalidate: 1min, expire: 1hr)
  cacheLife('minutes');
  cacheTag('minutes-cache');

  const timestamp = new Date().toISOString();
  await new Promise((resolve) => setTimeout(resolve, 100));

  return (
    <div className="p-4 border border-blue-200 dark:border-blue-800 rounded-lg">
      <div className="flex justify-between items-center mb-3">
        <h4 className="font-semibold">cacheLife('minutes')</h4>
        <span className="text-xs px-2 py-1 bg-blue-100 dark:bg-blue-900 rounded">5min</span>
      </div>
      <p className="text-xs text-gray-600 dark:text-gray-400 mb-2">
        Cached at: {timestamp}
      </p>
      <p className="text-xs text-gray-500">
        Stale: 5min | Revalidate: 1min | Expire: 1hr
      </p>
    </div>
  );
}

// Component cached for 1 hour
async function CachedComponentHours() {
  'use cache';
  const { cacheLife, cacheTag } = await import('next/cache');

  // Cache for 1 hour (stale: 5min, revalidate: 1hr, expire: 1day)
  cacheLife('hours');
  cacheTag('hours-cache');

  const timestamp = new Date().toISOString();
  await new Promise((resolve) => setTimeout(resolve, 100));

  return (
    <div className="p-4 border border-green-200 dark:border-green-800 rounded-lg">
      <div className="flex justify-between items-center mb-3">
        <h4 className="font-semibold">cacheLife('hours')</h4>
        <span className="text-xs px-2 py-1 bg-green-100 dark:bg-green-900 rounded">1hr</span>
      </div>
      <p className="text-xs text-gray-600 dark:text-gray-400 mb-2">
        Cached at: {timestamp}
      </p>
      <p className="text-xs text-gray-500">
        Stale: 5min | Revalidate: 1hr | Expire: 1day
      </p>
    </div>
  );
}

// Tagged component for invalidation testing
async function TaggedComponent({ tag, label }: { tag: string; label: string }) {
  'use cache';
  const { cacheLife, cacheTag } = await import('next/cache');

  cacheLife('hours');
  cacheTag(tag);

  const timestamp = new Date().toISOString();
  const data = await fetch('https://jsonplaceholder.typicode.com/posts/1').then((r) => r.json());

  return (
    <div className="p-4 border rounded-lg">
      <div className="flex justify-between items-center mb-3">
        <h4 className="font-semibold">{label}</h4>
        <span className="text-xs px-2 py-1 bg-gray-100 dark:bg-gray-800 rounded">tag: {tag}</span>
      </div>
      <p className="text-sm mb-2">{data.title}</p>
      <p className="text-xs text-gray-500">Cached: {timestamp}</p>
    </div>
  );
}
```

### Step 2: Create Invalidate Button

**Location:** `my-app/app/(dashboard)/cache-components/components/InvalidateButton.tsx`

```typescript
'use client';

import { useState } from 'react';
import { updateTagAction } from '@/app/actions/cache-actions';

export function InvalidateButton({ tag, label }: { tag: string; label: string }) {
  const [isInvalidating, setIsInvalidating] = useState(false);
  const [lastInvalidated, setLastInvalidated] = useState<string | null>(null);

  const handleInvalidate = async () => {
    setIsInvalidating(true);
    try {
      // Use updateTag for immediate expiration
      await updateTagAction(tag);
      setLastInvalidated(new Date().toLocaleTimeString());
    } catch (error) {
      console.error('Invalidation failed:', error);
    } finally {
      setIsInvalidating(false);
    }
  };

  return (
    <div className="flex flex-col gap-2">
      <button
        onClick={handleInvalidate}
        disabled={isInvalidating}
        className="px-4 py-2 bg-red-600 hover:bg-red-700 disabled:bg-gray-400 text-white rounded-md text-sm"
      >
        {isInvalidating ? 'Invalidating...' : label}
      </button>
      {lastInvalidated && (
        <p className="text-xs text-gray-500">Last: {lastInvalidated}</p>
      )}
    </div>
  );
}
```

### Step 3: Implement updateTag Server Action

**Location:** `my-app/app/actions/cache-actions.ts` (update)

```typescript
// Add to existing file

/**
 * Immediately expires cache (for read-your-own-writes scenarios)
 * Only works in Server Actions (not Route Handlers)
 *
 * @param tag - Cache tag to immediately expire
 */
export async function updateTagAction(tag: string) {
  // Note: updateTag is only available in Server Actions in Next.js canary
  // For stable Next.js 16.0.0, use revalidateTag instead
  console.log(`[Cache Action] Updating tag: ${tag} at ${new Date().toISOString()}`);

  try {
    // Import dynamically to handle Next.js version differences
    const { unstable_expireTag } = await import('next/cache');
    unstable_expireTag(tag);
    return { success: true, tag };
  } catch (error) {
    // Fallback to revalidateTag if updateTag/unstable_expireTag not available
    console.warn('updateTag not available, using revalidateTag as fallback');
    revalidateTag(tag, 'max');
    return { success: true, tag, fallback: true };
  }
}
```

---

## Expected Results

1. **cacheLife profiles:** Each component shows different cache duration
2. **Timestamps persist:** Refresh page - timestamps stay same (cached)
3. **Invalidation works:** Click button - that component's timestamp updates
4. **Granular control:** Can cache individual components, not whole routes

---

## Testing Steps

1. Visit `/cache-components`
2. Note timestamps on all components
3. Refresh page - timestamps stay same
4. Click "Invalidate A" - only Component A updates
5. Wait for cache duration - component revalidates automatically

---

## Key Concepts

- `'use cache'` - marks component/function as cacheable
- `cacheLife()` - sets cache duration profile
- `cacheTag()` - enables targeted invalidation
- `updateTag()` - immediate expiration (vs stale-while-revalidate)
- Granular caching - component-level, not route-level

---

**Status:** Ready for implementation
**Dependencies:** Plan 01 complete, cacheComponents: true in config
**Time:** 40-50 minutes
