# Plan 02: Request Memoization Demo

**Date:** 2025-11-10
**Goal:** Demonstrate React's automatic request memoization within a component tree

---

## Overview

Request memoization is a **React feature** (not Next.js-specific) that automatically deduplicates `fetch` requests with the same URL and options within a single render pass.

**Key Characteristics:**
- Only applies to `GET` method in `fetch` requests
- Scoped to React component tree (layouts, pages, Server Components)
- Does NOT work in Route Handlers (outside component tree)
- Automatic - no opt-in required
- Memory cleared after render completes

**This demo will:**
1. Create multiple components that fetch the same URL
2. Show timestamps proving only ONE actual fetch occurs
3. Display cache HIT/MISS status
4. Explain behavior with inline comments

---

## Implementation Steps

### Step 1: Create Request Memoization Page

**Location:** `my-app/app/(dashboard)/request-memoization/page.tsx`

**Action:** Create main page with component tree

```typescript
// This page demonstrates React's automatic request memoization
// Multiple components fetch the same URL - only ONE network request happens
export default async function RequestMemoizationPage() {
  return (
    <div className="max-w-6xl mx-auto space-y-8">
      {/* Explanation section */}
      <section>
        <h2 className="text-3xl font-bold mb-4">Request Memoization</h2>
        <div className="p-6 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
          <h3 className="font-semibold mb-2">What is Request Memoization?</h3>
          <p className="text-sm text-gray-700 dark:text-gray-300 mb-3">
            React automatically deduplicates <code className="px-1 py-0.5 bg-gray-200 dark:bg-gray-800 rounded">fetch()</code> requests
            with the same URL and options during a single render pass.
          </p>
          <ul className="text-sm text-gray-600 dark:text-gray-400 list-disc list-inside space-y-1">
            <li>Scope: Single React component tree render</li>
            <li>Duration: Memory cleared after render completes</li>
            <li>Method: Only GET requests</li>
            <li>Automatic: No configuration needed</li>
          </ul>
        </div>
      </section>

      {/* Demo section */}
      <section>
        <h3 className="text-xl font-semibold mb-4">
          Live Demo: 3 Components Fetching Same URL
        </h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-6">
          Watch the timestamps below. All three components call <code>fetchUser(1)</code>,
          but only the <strong>first one</strong> makes a real network request.
          The others get the memoized result instantly.
        </p>

        {/* Three components calling the same fetch */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <UserCard userId={1} label="Component A" />
          <UserCard userId={1} label="Component B" />
          <UserCard userId={1} label="Component C" />
        </div>
      </section>

      {/* Comparison: Different IDs */}
      <section>
        <h3 className="text-xl font-semibold mb-4">
          Comparison: Different User IDs (No Memoization)
        </h3>
        <p className="text-sm text-gray-600 dark:text-gray-400 mb-6">
          When URLs differ, each fetch is independent. No memoization occurs.
        </p>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <UserCard userId={1} label="User 1" />
          <UserCard userId={2} label="User 2" />
          <UserCard userId={3} label="User 3" />
        </div>
      </section>

      {/* How it Works */}
      <section className="p-6 border border-gray-200 dark:border-gray-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-3">How It Works</h3>
        <div className="space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <div className="flex gap-3">
            <span className="font-mono bg-green-100 dark:bg-green-900 px-2 py-1 rounded">1st call</span>
            <p>React sees <code>fetch('https://api.com/user/1')</code> → Makes network request → Stores in memory</p>
          </div>
          <div className="flex gap-3">
            <span className="font-mono bg-blue-100 dark:bg-blue-900 px-2 py-1 rounded">2nd call</span>
            <p>React sees same URL → Returns memoized result → No network request</p>
          </div>
          <div className="flex gap-3">
            <span className="font-mono bg-blue-100 dark:bg-blue-900 px-2 py-1 rounded">3rd call</span>
            <p>React sees same URL → Returns memoized result → No network request</p>
          </div>
          <div className="flex gap-3">
            <span className="font-mono bg-gray-100 dark:bg-gray-900 px-2 py-1 rounded">After render</span>
            <p>Memory cleared → Next page load starts fresh</p>
          </div>
        </div>
      </section>

      {/* Opt-out section */}
      <section className="p-6 bg-yellow-50 dark:bg-yellow-950 border border-yellow-200 dark:border-yellow-800 rounded-lg">
        <h3 className="text-xl font-semibold mb-3">How to Opt Out</h3>
        <p className="text-sm text-gray-700 dark:text-gray-300 mb-3">
          If you want each component to make its own request, use <code>AbortController</code> with a unique signal:
        </p>
        <pre className="bg-gray-900 text-gray-100 p-4 rounded-md overflow-x-auto text-xs">
{`// Each call gets unique signal → no memoization
const controller = new AbortController();
fetch(url, { signal: controller.signal });`}
        </pre>
      </section>
    </div>
  );
}

// Component that fetches user data
// This will be rendered 3 times with same userId to demonstrate memoization
async function UserCard({ userId, label }: { userId: number; label: string }) {
  // Start timestamp - when this component begins executing
  const startTime = Date.now();

  // Fetch user data from JSONPlaceholder
  // React will memoize this if same URL appears in this render tree
  const user = await fetchUser(userId);

  // End timestamp - when fetch completes (or returns from memo)
  const endTime = Date.now();
  const duration = endTime - startTime;

  // If duration < 10ms, likely a cache hit (instant)
  // If duration > 100ms, likely a cache miss (network request)
  const isCacheHit = duration < 10;

  return (
    <div className="p-4 border border-gray-200 dark:border-gray-800 rounded-lg">
      <div className="flex items-center justify-between mb-3">
        <h4 className="font-semibold">{label}</h4>
        <span
          className={`text-xs px-2 py-1 rounded ${
            isCacheHit
              ? 'bg-green-100 dark:bg-green-900 text-green-800 dark:text-green-200'
              : 'bg-red-100 dark:bg-red-900 text-red-800 dark:text-red-200'
          }`}
        >
          {isCacheHit ? 'HIT' : 'MISS'}
        </span>
      </div>

      <div className="space-y-2 text-sm">
        <p className="font-medium">{user.name}</p>
        <p className="text-gray-600 dark:text-gray-400">{user.email}</p>

        {/* Timestamp information */}
        <div className="mt-4 pt-3 border-t border-gray-200 dark:border-gray-700 space-y-1 text-xs text-gray-500">
          <p>User ID: {user.id}</p>
          <p>Fetch duration: {duration}ms</p>
          <p>
            {isCacheHit
              ? '✓ Returned from memoization'
              : '✗ Made network request'}
          </p>
        </div>
      </div>
    </div>
  );
}

// Fetch function - called by multiple components
// React automatically memoizes this within the render tree
async function fetchUser(userId: number) {
  // Log to server console for debugging
  console.log(`[Request Memoization] Fetching user ${userId} at ${new Date().toISOString()}`);

  // Fetch from JSONPlaceholder API
  // This is NOT cached by Next.js Data Cache (no-store is default in v16)
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`,
    {
      // Explicitly opt out of Data Cache to show pure request memoization
      cache: 'no-store',
    }
  );

  if (!res.ok) {
    throw new Error(`Failed to fetch user ${userId}`);
  }

  const data = await res.json();

  return {
    id: data.id,
    name: data.name,
    email: data.email,
    username: data.username,
  };
}
```

---

### Step 2: Add Request Counter to Show Actual Network Calls

**Location:** Create `my-app/app/(dashboard)/request-memoization/components/RequestCounter.tsx`

**Action:** Client component to monitor network requests

```typescript
'use client';

import { useEffect, useState } from 'react';

/**
 * Client component that monitors Performance API for fetch requests
 * Shows how many actual network requests occurred vs component renders
 */
export function RequestCounter({ expectedCalls }: { expectedCalls: number }) {
  const [networkCalls, setNetworkCalls] = useState<number>(0);

  useEffect(() => {
    // Check Performance API for fetch entries to JSONPlaceholder
    if (typeof window !== 'undefined' && 'performance' in window) {
      const entries = performance.getEntriesByType('resource') as PerformanceResourceTiming[];
      const jsonPlaceholderCalls = entries.filter((entry) =>
        entry.name.includes('jsonplaceholder.typicode.com/users')
      );

      setNetworkCalls(jsonPlaceholderCalls.length);
    }
  }, []);

  const isMemoized = networkCalls < expectedCalls;

  return (
    <div className="p-4 bg-purple-50 dark:bg-purple-950 border border-purple-200 dark:border-purple-800 rounded-lg">
      <h4 className="font-semibold mb-2">Network Activity</h4>
      <div className="space-y-2 text-sm">
        <div className="flex justify-between">
          <span className="text-gray-600 dark:text-gray-400">Components rendered:</span>
          <span className="font-mono font-bold">{expectedCalls}</span>
        </div>
        <div className="flex justify-between">
          <span className="text-gray-600 dark:text-gray-400">Actual network calls:</span>
          <span className="font-mono font-bold text-green-600 dark:text-green-400">
            {networkCalls}
          </span>
        </div>
        {isMemoized && (
          <p className="text-xs text-green-700 dark:text-green-300 mt-2 p-2 bg-green-100 dark:bg-green-900 rounded">
            ✓ Request memoization working! {expectedCalls - networkCalls} duplicate requests avoided.
          </p>
        )}
      </div>
    </div>
  );
}
```

---

### Step 3: Update Page to Include Request Counter

**Location:** `my-app/app/(dashboard)/request-memoization/page.tsx` (update)

**Action:** Add Request Counter above the demo section

```typescript
import { RequestCounter } from './components/RequestCounter';

// Add this after the explanation section, before the demo section:

{/* Network monitoring */}
<RequestCounter expectedCalls={3} />
```

---

### Step 4: Create Comparison Page (Opt-Out Example)

**Location:** `my-app/app/(dashboard)/request-memoization/without-memo/page.tsx`

**Action:** Show how to disable memoization

```typescript
// Demonstrates opting out of request memoization using AbortController
export default async function WithoutMemoizationPage() {
  return (
    <div className="max-w-4xl mx-auto space-y-8">
      <section>
        <h2 className="text-2xl font-bold mb-4">Without Memoization (Opt-Out)</h2>
        <p className="text-gray-600 dark:text-gray-400 mb-6">
          Using <code>AbortController</code> with unique signals prevents memoization.
          All three components will make independent network requests.
        </p>
      </section>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
        <UserCardNoMemo userId={1} label="Component A" />
        <UserCardNoMemo userId={1} label="Component B" />
        <UserCardNoMemo userId={1} label="Component C" />
      </div>

      <section className="p-6 bg-red-50 dark:bg-red-950 border border-red-200 dark:border-red-800 rounded-lg">
        <h3 className="font-semibold mb-2">⚠️ Performance Impact</h3>
        <p className="text-sm text-gray-700 dark:text-gray-300">
          Notice all three cards show "MISS". Each made its own network request,
          tripling the load time. Use this pattern only when you specifically need
          non-memoized data.
        </p>
      </section>
    </div>
  );
}

// Component that opts out of memoization
async function UserCardNoMemo({ userId, label }: { userId: number; label: string }) {
  const startTime = Date.now();

  // Create unique AbortController for this fetch
  // Different signal = no memoization
  const controller = new AbortController();

  const user = await fetchUserNoMemo(userId, controller.signal);

  const endTime = Date.now();
  const duration = endTime - startTime;

  return (
    <div className="p-4 border border-red-200 dark:border-red-800 rounded-lg">
      <div className="flex items-center justify-between mb-3">
        <h4 className="font-semibold">{label}</h4>
        <span className="text-xs px-2 py-1 rounded bg-red-100 dark:bg-red-900 text-red-800 dark:text-red-200">
          MISS
        </span>
      </div>

      <div className="space-y-2 text-sm">
        <p className="font-medium">{user.name}</p>
        <p className="text-gray-600 dark:text-gray-400">{user.email}</p>

        <div className="mt-4 pt-3 border-t border-gray-200 dark:border-gray-700 space-y-1 text-xs text-gray-500">
          <p>Fetch duration: {duration}ms</p>
          <p>✗ Unique signal - no memoization</p>
        </div>
      </div>
    </div>
  );
}

// Fetch with AbortController signal - prevents memoization
async function fetchUserNoMemo(userId: number, signal: AbortSignal) {
  console.log(`[No Memo] Fetching user ${userId} at ${new Date().toISOString()}`);

  const res = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`,
    {
      cache: 'no-store',
      signal, // Unique signal prevents React memoization
    }
  );

  if (!res.ok) {
    throw new Error(`Failed to fetch user ${userId}`);
  }

  return res.json();
}
```

---

## Expected Result After Completion

### What You Should See

1. **Three user cards render** with the same user data (userId=1)
2. **First card shows "MISS"** (red badge) - made network request
3. **Second & third cards show "HIT"** (green badges) - used memoized result
4. **Fetch durations differ:** First card ~200-500ms, others <10ms
5. **Request counter shows:** 3 components rendered, but only 1 network call
6. **Server console logs** show only ONE fetch log for userId=1

### When Refreshing the Page

- Memoization resets (memory cleared)
- First component in new render tree makes network request
- Others memoize within that render

### Network Tab Verification

Open browser DevTools → Network tab → Filter by "users"
- Should see **only 1 request** to `jsonplaceholder.typicode.com/users/1`
- Despite 3 components calling the same fetch

---

## Testing Steps

### 1. Navigate to Request Memoization Tab

Visit http://localhost:3000/request-memoization

**Expected:** Page renders with 3 user cards (same user)

### 2. Check Cache Status Badges

Look at the badge on each card

**Expected:**
- First card: RED "MISS" badge
- Second card: GREEN "HIT" badge
- Third card: GREEN "HIT" badge

### 3. Compare Fetch Durations

Look at "Fetch duration" in each card

**Expected:**
- First card: >100ms (network latency)
- Second card: <10ms (instant from memory)
- Third card: <10ms (instant from memory)

### 4. Check Network Tab

Open DevTools → Network → Filter by "users" → Refresh page

**Expected:**
- Only **1 network request** to `/users/1`
- No duplicate requests

### 5. Check Server Console

Look at terminal running `npm run dev`

**Expected:**
```
[Request Memoization] Fetching user 1 at 2025-11-10T...
```
Only ONE log entry per page load (not 3)

### 6. Test Different User IDs Section

Scroll to "Comparison: Different User IDs"

**Expected:**
- All 3 cards show "MISS" (different URLs = no memoization)
- 3 network requests in Network tab
- 3 fetch logs in server console

### 7. Test Opt-Out Example

Navigate to `/request-memoization/without-memo`

**Expected:**
- All 3 cards show "MISS" (AbortController prevents memo)
- 3 network requests for same user

---

## Common Issues & Troubleshooting

### Issue: All cards show "MISS"

**Cause:** Memoization might not be working
**Debug:**
1. Check fetch duration - if all >100ms, memoization failed
2. Verify all components use same URL exactly
3. Ensure using default fetch (no custom AbortController)
4. Check React version (need React 18+)

### Issue: Network tab shows 3 requests

**Cause:** Server Components not rendering in same tree
**Fix:** Ensure all UserCard components are children of same parent

### Issue: TypeScript errors on fetch

**Cause:** Missing types for User
**Fix:** Import from `@/app/types/cache`

### Issue: RequestCounter shows 0 calls

**Cause:** Performance API timing
**Fix:** Add small delay in useEffect or check after full page load

---

## Key Concepts Learned

1. **Request Memoization Scope:** Only within React component tree, single render
2. **Automatic Behavior:** No opt-in needed - React does it automatically
3. **Deduplication Logic:** Same URL + same options = same memoized result
4. **Memory Lifecycle:** Cleared after render completes
5. **Opt-Out Strategy:** Use AbortController with unique signals
6. **Performance Benefit:** Reduces duplicate API calls in complex component trees

---

## Next Steps

- ✅ Request memoization demo complete
- ⏭️ **Plan 03:** Implement Data Cache demo (persistent server-side cache)

---

**Status:** Ready for implementation
**Dependencies:** Plan 01 (setup) must be completed first
**Estimated Time:** 30-40 minutes
