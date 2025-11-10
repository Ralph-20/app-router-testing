# Plan 04: Full Route Cache Demo

**Date:** 2025-11-10
**Goal:** Demonstrate Next.js Full Route Cache - static vs dynamic rendering behavior

---

## Overview

Full Route Cache stores rendered HTML + RSC (React Server Components) payload on the server for static routes.

**Key Characteristics:**
- Only applies to **statically rendered routes**
- Caches at build time or after first request (with revalidation)
- Dynamic routes render per-request (NOT cached)
- Using dynamic APIs (`cookies()`, `headers()`, `searchParams`) opts route out of cache
- Revalidating Data Cache also revalidates Full Route Cache

**This demo will:**
1. Create static route (cached at build time)
2. Create dynamic route (renders per-request)
3. Show build timestamps vs request timestamps
4. Demonstrate how dynamic APIs opt out of caching
5. Compare performance (instant static vs per-request dynamic)

---

## Implementation Steps

### Step 1: Create Full Route Cache Page (Parent)

**Location:** `my-app/app/(dashboard)/full-route-cache/page.tsx`

**Action:** Main page with explanation and links to demos

```typescript
import Link from 'next/link';

// This page explains Full Route Cache
// Links to static and dynamic route examples
export default function FullRouteCachePage() {
  return (
    <div className="max-w-4xl mx-auto space-y-8">
      {/* Explanation */}
      <section>
        <h2 className="text-3xl font-bold mb-4">Full Route Cache</h2>
        <div className="p-6 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
          <h3 className="font-semibold mb-2">What is Full Route Cache?</h3>
          <p className="text-sm text-gray-700 dark:text-gray-300 mb-3">
            Caches the rendered HTML and RSC payload for <strong>static routes</strong>.
            Routes are cached at build time or after revalidation, making them
            instant to serve.
          </p>
          <ul className="text-sm text-gray-600 dark:text-gray-400 list-disc list-inside space-y-1">
            <li>Scope: Server-side (per-deployment)</li>
            <li>Duration: Until revalidated or redeployed</li>
            <li>Only for: Static routes (no dynamic APIs)</li>
            <li>Opt-out: Use cookies(), headers(), searchParams, or dynamic = 'force-dynamic'</li>
          </ul>
        </div>
      </section>

      {/* Static vs Dynamic */}
      <section className="grid grid-cols-1 md:grid-cols-2 gap-6">
        {/* Static Route */}
        <div className="p-6 border border-green-200 dark:border-green-800 rounded-lg">
          <div className="flex items-center gap-2 mb-3">
            <div className="w-3 h-3 rounded-full bg-green-500"></div>
            <h3 className="text-xl font-semibold">Static Route</h3>
          </div>

          <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
            Rendered at build time. Cached HTML + RSC payload served instantly
            to all users. No server-side computation per-request.
          </p>

          <ul className="text-sm space-y-2 mb-6">
            <li className="flex items-start gap-2">
              <span className="text-green-600">✓</span>
              <span>Blazing fast response</span>
            </li>
            <li className="flex items-start gap-2">
              <span className="text-green-600">✓</span>
              <span>Can be served from CDN edge</span>
            </li>
            <li className="flex items-start gap-2">
              <span className="text-green-600">✓</span>
              <span>Lower server load</span>
            </li>
          </ul>

          <Link
            href="/full-route-cache/static-example"
            className="block w-full px-4 py-2 bg-green-600 hover:bg-green-700 text-white text-center rounded-md transition-colors"
          >
            View Static Example
          </Link>
        </div>

        {/* Dynamic Route */}
        <div className="p-6 border border-orange-200 dark:border-orange-800 rounded-lg">
          <div className="flex items-center gap-2 mb-3">
            <div className="w-3 h-3 rounded-full bg-orange-500"></div>
            <h3 className="text-xl font-semibold">Dynamic Route</h3>
          </div>

          <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
            Rendered per-request. Uses dynamic APIs (cookies, headers, etc.)
            that require runtime information. Cannot be cached.
          </p>

          <ul className="text-sm space-y-2 mb-6">
            <li className="flex items-start gap-2">
              <span className="text-orange-600">•</span>
              <span>Personalized per-user</span>
            </li>
            <li className="flex items-start gap-2">
              <span className="text-orange-600">•</span>
              <span>Always fresh data</span>
            </li>
            <li className="flex items-start gap-2">
              <span className="text-orange-600">•</span>
              <span>Higher server computation</span>
            </li>
          </ul>

          <Link
            href="/full-route-cache/dynamic-example"
            className="block w-full px-4 py-2 bg-orange-600 hover:bg-orange-700 text-white text-center rounded-md transition-colors"
          >
            View Dynamic Example
          </Link>
        </div>
      </section>

      {/* How Routes Become Dynamic */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-3">What Makes a Route Dynamic?</h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
          Using any of these APIs opts the route out of Full Route Cache:
        </p>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 text-sm">
          <div className="p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <code className="text-orange-600">cookies()</code>
            <p className="text-gray-600 dark:text-gray-400 mt-1">
              Reading or setting cookies requires runtime access
            </p>
          </div>

          <div className="p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <code className="text-orange-600">headers()</code>
            <p className="text-gray-600 dark:text-gray-400 mt-1">
              Reading request headers is per-request data
            </p>
          </div>

          <div className="p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <code className="text-orange-600">searchParams</code>
            <p className="text-gray-600 dark:text-gray-400 mt-1">
              URL query params change per-request
            </p>
          </div>

          <div className="p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <code className="text-orange-600">{`dynamic = 'force-dynamic'`}</code>
            <p className="text-gray-600 dark:text-gray-400 mt-1">
              Explicit opt-out via route config
            </p>
          </div>
        </div>
      </section>

      {/* Performance Comparison */}
      <section className="p-6 bg-purple-50 dark:bg-purple-950 border border-purple-200 dark:border-purple-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-3">Performance Comparison</h3>
        <p className="text-sm text-gray-700 dark:text-gray-300 mb-4">
          Click both examples above and compare load times in DevTools Network tab:
        </p>
        <ul className="text-sm space-y-2">
          <li>
            <strong>Static:</strong> Typically &lt;10ms (instant from cache)
          </li>
          <li>
            <strong>Dynamic:</strong> 50-200ms+ (depends on server computation)
          </li>
        </ul>
      </section>
    </div>
  );
}
```

---

### Step 2: Create Static Route Example

**Location:** `my-app/app/(dashboard)/full-route-cache/static-example/page.tsx`

**Action:** Fully static route that gets cached

```typescript
// This is a STATIC route - will be cached in Full Route Cache
// No dynamic APIs used, so Next.js can render at build time

// Export revalidate to show ISR (Incremental Static Regeneration)
export const revalidate = 60; // Revalidate every 60 seconds

export default async function StaticExamplePage() {
  // Build/render timestamp - shows when this page was last generated
  const buildTime = new Date().toISOString();

  // Fetch data with caching (static data)
  // This fetch happens at build time, not per-request
  const post = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
    cache: 'force-cache', // Cache this data
  }).then((res) => res.json());

  // This component tree is rendered ONCE at build time
  // All users see the same cached HTML + RSC payload
  return (
    <div className="max-w-4xl mx-auto space-y-8">
      {/* Status Badge */}
      <div className="flex items-center gap-3">
        <div className="flex items-center gap-2">
          <div className="w-4 h-4 rounded-full bg-green-500 animate-pulse"></div>
          <span className="font-semibold text-green-700 dark:text-green-300">
            STATIC ROUTE
          </span>
        </div>
        <span className="text-sm text-gray-500">
          (Cached in Full Route Cache)
        </span>
      </div>

      {/* Explanation */}
      <section className="p-6 bg-green-50 dark:bg-green-950 border border-green-200 dark:border-green-800 rounded-lg">
        <h2 className="text-2xl font-bold mb-3">Static Route Example</h2>
        <p className="text-sm text-gray-700 dark:text-gray-300 mb-4">
          This page was rendered at <strong>build time</strong> (or after last revalidation).
          The timestamp below should stay the same across multiple visits until
          60 seconds pass + the page is accessed again.
        </p>
        <p className="text-xs text-gray-600 dark:text-gray-400">
          💡 Try refreshing this page multiple times - the timestamp won't change
          because you're seeing the cached version!
        </p>
      </section>

      {/* Build Info */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Build Information</h3>
        <div className="space-y-3 text-sm">
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Last rendered at:</span>
            <code className="font-mono font-semibold">{buildTime}</code>
          </div>
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Revalidation period:</span>
            <code className="font-mono font-semibold">60 seconds</code>
          </div>
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Rendering mode:</span>
            <code className="font-mono font-semibold">Static (SSG)</code>
          </div>
        </div>
      </section>

      {/* Fetched Content */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Cached Content</h3>
        <div className="p-4 bg-blue-50 dark:bg-blue-950 rounded-md">
          <h4 className="font-semibold mb-2">{post.title}</h4>
          <p className="text-sm text-gray-600 dark:text-gray-400">
            {post.body}
          </p>
          <p className="text-xs text-gray-500 mt-3">
            Post ID: {post.id} | Cached at build time
          </p>
        </div>
      </section>

      {/* How It Works */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-3">How This Works</h3>
        <ol className="list-decimal list-inside space-y-2 text-sm text-gray-600 dark:text-gray-400">
          <li>
            <strong>Build time:</strong> Next.js renders this page, fetches data, stores HTML + RSC
          </li>
          <li>
            <strong>First request:</strong> Serve cached HTML instantly (no computation)
          </li>
          <li>
            <strong>Subsequent requests:</strong> Continue serving cached version
          </li>
          <li>
            <strong>After 60s + request:</strong> Serve stale, revalidate in background
          </li>
          <li>
            <strong>Next request:</strong> Serve fresh cached version
          </li>
        </ol>
      </section>

      {/* Code Example */}
      <section className="p-6 bg-gray-50 dark:bg-gray-900 rounded-lg">
        <h3 className="text-lg font-semibold mb-3">Code for This Page</h3>
        <pre className="text-xs overflow-x-auto">
{`// page.tsx
export const revalidate = 60; // ISR - revalidate every 60s

export default async function Page() {
  const buildTime = new Date().toISOString();
  const data = await fetch(url, { cache: 'force-cache' });

  return <div>Built at: {buildTime}</div>;
}

// ✓ No dynamic APIs (cookies, headers, searchParams)
// ✓ Static rendering → Full Route Cache applies`}
        </pre>
      </section>
    </div>
  );
}
```

---

### Step 3: Create Dynamic Route Example

**Location:** `my-app/app/(dashboard)/full-route-cache/dynamic-example/page.tsx`

**Action:** Dynamic route that renders per-request

```typescript
import { cookies, headers } from 'next/headers';

// This is a DYNAMIC route - will NOT be cached in Full Route Cache
// Uses cookies() which requires runtime information

export default async function DynamicExamplePage() {
  // Request timestamp - shows when THIS request was handled
  const requestTime = new Date().toISOString();

  // Using cookies() makes this route dynamic
  // Can't be prerendered at build time because cookies are per-request
  const cookieStore = await cookies();
  const visitCount = Number(cookieStore.get('visit_count')?.value || 0) + 1;

  // Using headers() also makes route dynamic
  const headersList = await headers();
  const userAgent = headersList.get('user-agent') || 'Unknown';

  // Fetch dynamic data (uncached)
  const post = await fetch('https://jsonplaceholder.typicode.com/posts/2', {
    cache: 'no-store', // Don't cache - always fresh
  }).then((res) => res.json());

  return (
    <div className="max-w-4xl mx-auto space-y-8">
      {/* Status Badge */}
      <div className="flex items-center gap-3">
        <div className="flex items-center gap-2">
          <div className="w-4 h-4 rounded-full bg-orange-500 animate-pulse"></div>
          <span className="font-semibold text-orange-700 dark:text-orange-300">
            DYNAMIC ROUTE
          </span>
        </div>
        <span className="text-sm text-gray-500">
          (Rendered per-request)
        </span>
      </div>

      {/* Explanation */}
      <section className="p-6 bg-orange-50 dark:bg-orange-950 border border-orange-200 dark:border-orange-800 rounded-lg">
        <h2 className="text-2xl font-bold mb-3">Dynamic Route Example</h2>
        <p className="text-sm text-gray-700 dark:text-gray-300 mb-4">
          This page is rendered <strong>per-request</strong> because it uses{' '}
          <code className="px-1 py-0.5 bg-gray-200 dark:bg-gray-800 rounded">cookies()</code>{' '}
          and{' '}
          <code className="px-1 py-0.5 bg-gray-200 dark:bg-gray-800 rounded">headers()</code>.
          It cannot be cached in Full Route Cache.
        </p>
        <p className="text-xs text-gray-600 dark:text-gray-400">
          💡 Refresh this page multiple times - the timestamp WILL update each time!
        </p>
      </section>

      {/* Request Info */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Request Information</h3>
        <div className="space-y-3 text-sm">
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Rendered at:</span>
            <code className="font-mono font-semibold">{requestTime}</code>
          </div>
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Visit count (cookie):</span>
            <code className="font-mono font-semibold">{visitCount}</code>
          </div>
          <div className="flex justify-between items-center p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
            <span className="text-gray-600 dark:text-gray-400">Rendering mode:</span>
            <code className="font-mono font-semibold">Dynamic (SSR)</code>
          </div>
        </div>
      </section>

      {/* User Agent (from headers) */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Your User Agent</h3>
        <p className="text-xs text-gray-600 dark:text-gray-400 mb-2">
          (Read from headers() - requires runtime)
        </p>
        <div className="p-3 bg-gray-50 dark:bg-gray-900 rounded-md">
          <code className="text-xs break-all">{userAgent}</code>
        </div>
      </section>

      {/* Dynamic Content */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-4">Fresh Content (Uncached)</h3>
        <div className="p-4 bg-orange-50 dark:bg-orange-950 rounded-md">
          <h4 className="font-semibold mb-2">{post.title}</h4>
          <p className="text-sm text-gray-600 dark:text-gray-400">
            {post.body}
          </p>
          <p className="text-xs text-gray-500 mt-3">
            Post ID: {post.id} | Fetched just now
          </p>
        </div>
      </section>

      {/* Code Example */}
      <section className="p-6 bg-gray-50 dark:bg-gray-900 rounded-lg">
        <h3 className="text-lg font-semibold mb-3">Code for This Page</h3>
        <pre className="text-xs overflow-x-auto">
{`// page.tsx
import { cookies, headers } from 'next/headers';

export default async function Page() {
  const requestTime = new Date().toISOString();

  // Using cookies() opts OUT of Full Route Cache
  const cookieStore = await cookies();
  const visitCount = cookieStore.get('visit_count');

  // Using headers() also opts OUT
  const headersList = await headers();
  const userAgent = headersList.get('user-agent');

  return <div>Rendered at: {requestTime}</div>;
}

// ✗ Dynamic APIs used → Dynamic rendering
// ✗ Full Route Cache does NOT apply`}
        </pre>
      </section>
    </div>
  );
}

// Set cookie for visit count tracking
// This Server Action would be called from a client component
// For demo purposes, we'll set it in a middleware or layout
```

---

### Step 4: Add Server Action to Set Visit Cookie

**Location:** `my-app/app/(dashboard)/full-route-cache/dynamic-example/actions.ts`

**Action:** Server Action to increment visit counter

```typescript
'use server';

import { cookies } from 'next/headers';

/**
 * Increments the visit count cookie
 * Called when user visits dynamic example page
 */
export async function incrementVisitCount() {
  const cookieStore = await cookies();
  const currentCount = Number(cookieStore.get('visit_count')?.value || 0);

  // Set cookie with new count (expires in 1 day)
  cookieStore.set('visit_count', String(currentCount + 1), {
    maxAge: 60 * 60 * 24, // 1 day
    httpOnly: true,
  });
}
```

---

## Expected Result After Completion

### Static Example (`/full-route-cache/static-example`)

**Behavior:**
- Timestamp stays same across refreshes (cached)
- After 60s + refresh, timestamp updates (revalidated)
- Instant page load (<10ms)
- Network tab shows 304 Not Modified or instant response

### Dynamic Example (`/full-route-cache/dynamic-example`)

**Behavior:**
- Timestamp updates on EVERY refresh (not cached)
- Visit count increments each visit
- User agent shows your browser info
- Slower load than static (50-200ms+ depending on server)
- Network tab shows 200 OK with server processing time

---

## Testing Steps

### 1. Test Static Route

1. Visit `/full-route-cache/static-example`
2. Note the "Last rendered at" timestamp
3. Refresh page 5 times (Cmd+R)
4. **Expected:** Timestamp stays the same (cached)

### 2. Test Static Revalidation

1. Wait 61+ seconds on static page
2. Refresh page
3. **Expected:** Timestamp updates (revalidated)

### 3. Test Dynamic Route

1. Visit `/full-route-cache/dynamic-example`
2. Note "Rendered at" timestamp
3. Refresh page
4. **Expected:** Timestamp changes every refresh

### 4. Test Visit Counter

1. Visit dynamic example
2. Note visit count
3. Refresh page
4. **Expected:** Visit count increments

### 5. Compare Performance

1. Open DevTools → Network tab
2. Visit static example → note timing
3. Visit dynamic example → note timing
4. **Expected:** Static is much faster

---

## Key Concepts Learned

1. **Static Routes:** No dynamic APIs = Full Route Cache applies
2. **Dynamic Routes:** Using cookies/headers/searchParams = per-request rendering
3. **ISR:** `export const revalidate = seconds` enables time-based revalidation
4. **Performance:** Static routes are significantly faster (instant from cache)
5. **Build vs Runtime:** Static rendered once, dynamic rendered per-request
6. **Opt-Out:** Easy to make route dynamic by adding one dynamic API call

---

## Next Steps

- ✅ Full Route Cache demo complete
- ⏭️ **Plan 05:** Implement Router Cache demo (client-side)

---

**Status:** Ready for implementation
**Dependencies:** Plan 01 (setup) must be completed
**Estimated Time:** 40-50 minutes
