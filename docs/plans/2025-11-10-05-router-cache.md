# Plan 05: Router Cache Demo

**Date:** 2025-11-10
**Goal:** Demonstrate client-side Router Cache behavior and invalidation

---

## Overview

Router Cache is an **in-memory client-side cache** of visited route segments' RSC payload.

**Key Characteristics:**
- Scope: Client browser (per-session, not shared between tabs)
- Duration: Session-based, cleared on page refresh
- Layouts: Cached and reused across navigation
- Pages: NOT cached by default (configurable via `staleTimes`)
- Purpose: Instant back/forward navigation, soft navigation

---

## Implementation Steps

### Step 1: Create Router Cache Main Page

**Location:** `my-app/app/(dashboard)/router-cache/page.tsx`

```typescript
import Link from 'next/link';

export default async function RouterCachePage() {
  const renderTime = new Date().toISOString();

  return (
    <div className="max-w-6xl mx-auto space-y-8">
      <section>
        <h2 className="text-3xl font-bold mb-4">Router Cache (Client-Side)</h2>
        <div className="p-6 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
          <p className="text-sm mb-3">
            In-memory client cache of RSC payloads for visited routes.
            Enables instant back/forward navigation.
          </p>
          <ul className="text-sm space-y-1 text-gray-600 dark:text-gray-400">
            <li>• Scope: Client browser (per-session)</li>
            <li>• Duration: Until page refresh or router.refresh()</li>
            <li>• Layouts cached, pages NOT cached by default</li>
          </ul>
        </div>
      </section>

      <section>
        <h3 className="text-xl font-semibold mb-4">Interactive Navigation Demo</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Rendered at: <code className="font-mono bg-gray-100 dark:bg-gray-800 px-2 py-1 rounded">{renderTime}</code>
        </p>

        <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
          <Link href="/router-cache/page-1" className="p-4 border rounded-lg hover:bg-gray-50 dark:hover:bg-gray-900 transition-colors">
            <div className="font-semibold mb-2">Page 1</div>
            <div className="text-sm text-gray-600 dark:text-gray-400">Navigate here →</div>
          </Link>
          <Link href="/router-cache/page-2" className="p-4 border rounded-lg hover:bg-gray-50 dark:hover:bg-gray-900 transition-colors">
            <div className="font-semibold mb-2">Page 2</div>
            <div className="text-sm text-gray-600 dark:text-gray-400">Navigate here →</div>
          </Link>
          <Link href="/router-cache/page-3" className="p-4 border rounded-lg hover:bg-gray-50 dark:hover:bg-gray-900 transition-colors">
            <div className="font-semibold mb-2">Page 3</div>
            <div className="text-sm text-gray-600 dark:text-gray-400">Navigate here →</div>
          </Link>
          <Link href="/router-cache/page-4" className="p-4 border rounded-lg hover:bg-gray-50 dark:hover:bg-gray-900 transition-colors">
            <div className="font-semibold mb-2">Page 4</div>
            <div className="text-sm text-gray-600 dark:text-gray-400">Navigate here →</div>
          </Link>
        </div>

        <div className="mt-6 p-4 bg-yellow-50 dark:bg-yellow-950 border border-yellow-200 dark:border-yellow-800 rounded-lg">
          <p className="text-sm font-semibold mb-2">Test Instructions:</p>
          <ol className="text-sm space-y-1 text-gray-700 dark:text-gray-300 list-decimal list-inside">
            <li>Click through all 4 pages (note their timestamps)</li>
            <li>Use browser back button to return here</li>
            <li>Notice instant navigation (cached RSC payload)</li>
            <li>Hard refresh (Cmd+R) - cache cleared</li>
          </ol>
        </div>
      </section>

      <RefreshButton />
    </div>
  );
}

// Import from components (created in Step 2)
import { RefreshButton } from './components/RefreshButton';
```

### Step 2: Create Client Refresh Button

**Location:** `my-app/app/(dashboard)/router-cache/components/RefreshButton.tsx`

```typescript
'use client';

import { useRouter } from 'next/navigation';
import { useState } from 'react';

export function RefreshButton() {
  const router = useRouter();
  const [lastRefreshed, setLastRefreshed] = useState<string | null>(null);

  const handleRefresh = () => {
    // router.refresh() clears Router Cache and makes new server request
    router.refresh();
    setLastRefreshed(new Date().toLocaleTimeString());
  };

  return (
    <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
      <h3 className="text-xl font-semibold mb-3">Clear Router Cache</h3>
      <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
        Click button to call <code>router.refresh()</code> - clears client cache and fetches fresh data.
      </p>
      <button
        onClick={handleRefresh}
        className="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-md"
      >
        router.refresh()
      </button>
      {lastRefreshed && (
        <p className="text-sm text-gray-500 mt-2">Last refreshed: {lastRefreshed}</p>
      )}
    </section>
  );
}
```

### Step 3: Create Sub-Pages (4 pages)

**Locations:**
- `my-app/app/(dashboard)/router-cache/page-1/page.tsx`
- `my-app/app/(dashboard)/router-cache/page-2/page.tsx`
- `my-app/app/(dashboard)/router-cache/page-3/page.tsx`
- `my-app/app/(dashboard)/router-cache/page-4/page.tsx`

**Template for each page:**

```typescript
// page-1/page.tsx (repeat for page-2, page-3, page-4)
import Link from 'next/link';

export default async function RouterCacheSubPage1() {
  // Fetch data to show caching behavior
  const renderTime = new Date().toISOString();
  const data = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
    cache: 'no-store', // No Data Cache - shows Router Cache behavior only
  }).then((res) => res.json());

  return (
    <div className="max-w-4xl mx-auto space-y-6">
      <div className="flex items-center justify-between">
        <h2 className="text-2xl font-bold">Page 1</h2>
        <Link href="/router-cache" className="text-blue-600 hover:underline">
          ← Back to Router Cache Demo
        </Link>
      </div>

      <div className="p-6 border rounded-lg">
        <div className="mb-4">
          <span className="text-xs px-2 py-1 rounded bg-green-100 dark:bg-green-900">
            Rendered: {renderTime}
          </span>
        </div>

        <h3 className="font-semibold mb-2">{data.title}</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400">{data.body}</p>
      </div>

      <div className="grid grid-cols-3 gap-4">
        <Link href="/router-cache/page-2" className="p-3 border rounded-md hover:bg-gray-50 dark:hover:bg-gray-900">
          Next: Page 2 →
        </Link>
        <Link href="/router-cache/page-3" className="p-3 border rounded-md hover:bg-gray-50 dark:hover:bg-gray-900">
          Skip to Page 3 →
        </Link>
        <Link href="/router-cache/page-4" className="p-3 border rounded-md hover:bg-gray-50 dark:hover:bg-gray-900">
          Skip to Page 4 →
        </Link>
      </div>

      <div className="p-4 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
        <p className="text-sm">
          <strong>Try this:</strong> Navigate forward through all pages, then use browser
          back button. Notice instant navigation - that's Router Cache at work!
        </p>
      </div>
    </div>
  );
}
```

---

## Expected Results

1. **First navigation to page:** New server request, fresh timestamp
2. **Browser back button:** Instant navigation, cached RSC payload
3. **After router.refresh():** New server request, fresh data
4. **Hard refresh (Cmd+R):** Cache cleared, all pages fetch fresh

---

## Testing Steps

1. Navigate through all 4 sub-pages (note timestamps)
2. Use browser back button (should be instant)
3. Check Network tab - no new requests on back navigation
4. Click refresh button - timestamps update
5. Hard refresh page - cache cleared

---

## Key Concepts

- Router Cache = client-side, session-based
- Enables instant soft navigation
- Cleared by router.refresh() or page refresh
- Layouts cached, pages not cached by default
- Different from Data Cache (server-side)

---

**Status:** Ready for implementation
**Time:** 30-40 minutes
