# Plan 08: MCP Showcase

**Date:** 2025-11-10
**Goal:** Demonstrate Next.js MCP integration with admin panel, Server Actions, and educational docs

---

## Overview

**Important:** MCP (Model Context Protocol) is designed for AI coding agents (like Claude Code), not for direct consumption by app UI. This showcase demonstrates:

1. Admin panel with runtime diagnostics (simulates MCP-like data)
2. Server Actions as the "MCP-like" feature for app interactions
3. Educational documentation about MCP tools

---

## Implementation Steps

### Step 1: Create MCP Showcase Main Page

**Location:** `my-app/app/(dashboard)/mcp-showcase/page.tsx`

```typescript
import Link from 'next/link';

export default function MCPShowcasePage() {
  return (
    <div className="max-w-6xl mx-auto space-y-8">
      <section>
        <h2 className="text-3xl font-bold mb-4">MCP Integration Showcase</h2>
        <div className="p-6 bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-lg">
          <p className="text-sm mb-3">
            <strong>Model Context Protocol (MCP)</strong> is Next.js 16's interface for
            AI coding agents to access runtime information, errors, routes, and diagnostics.
          </p>
          <p className="text-xs text-gray-600 dark:text-gray-400">
            Note: MCP is for AI agents (Claude Code, Cursor, etc.), not for app UI.
            This showcase demonstrates related concepts.
          </p>
        </div>
      </section>

      <section className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <Link
          href="/mcp-showcase/admin-panel"
          className="p-6 border border-green-200 dark:border-green-800 rounded-lg hover:bg-green-50 dark:hover:bg-green-950 transition-colors"
        >
          <div className="text-2xl mb-3">🛠️</div>
          <h3 className="text-lg font-semibold mb-2">Admin Panel</h3>
          <p className="text-sm text-gray-600 dark:text-gray-400">
            Server-rendered dashboard showing runtime diagnostics, build info,
            routes, and environment data
          </p>
        </Link>

        <Link
          href="/mcp-showcase/server-actions"
          className="p-6 border border-blue-200 dark:border-blue-800 rounded-lg hover:bg-blue-50 dark:hover:bg-blue-950 transition-colors"
        >
          <div className="text-2xl mb-3">⚡</div>
          <h3 className="text-lg font-semibold mb-2">Server Actions</h3>
          <p className="text-sm text-gray-600 dark:text-gray-400">
            Interactive Server Actions demo - the "MCP-like" feature for runtime
            interactions from UI
          </p>
        </Link>

        <Link
          href="/mcp-showcase/about-mcp"
          className="p-6 border border-purple-200 dark:border-purple-800 rounded-lg hover:bg-purple-50 dark:hover:bg-purple-950 transition-colors"
        >
          <div className="text-2xl mb-3">📚</div>
          <h3 className="text-lg font-semibold mb-2">About MCP</h3>
          <p className="text-sm text-gray-600 dark:text-gray-400">
            Educational documentation about MCP tools, how Claude Code uses them,
            and code examples
          </p>
        </Link>
      </section>

      <section className="p-6 border rounded-lg">
        <h3 className="text-xl font-semibold mb-3">What is MCP?</h3>
        <div className="space-y-3 text-sm">
          <p className="text-gray-600 dark:text-gray-400">
            MCP exposes a <code className="px-1 py-0.5 bg-gray-200 dark:bg-gray-800 rounded">/_next/mcp</code> endpoint
            in Next.js 16+ dev servers, providing AI agents with:
          </p>
          <ul className="list-disc list-inside space-y-1 text-gray-600 dark:text-gray-400 ml-4">
            <li>Build and runtime errors</li>
            <li>Page metadata and route information</li>
            <li>Project structure and configuration</li>
            <li>Server logs and diagnostics</li>
            <li>Server Action lookup by ID</li>
          </ul>
          <p className="text-gray-600 dark:text-gray-400 mt-4">
            <strong>For app developers:</strong> Use Server Actions and admin panels
            for runtime interactions - MCP is specifically designed for AI tooling.
          </p>
        </div>
      </section>
    </div>
  );
}
```

### Step 2: Create Admin Panel

**Location:** `my-app/app/(dashboard)/mcp-showcase/admin-panel/page.tsx`

```typescript
export default async function AdminPanelPage() {
  // Gather runtime info (simulates MCP-like diagnostics)
  const buildInfo = {
    nodeVersion: process.version,
    nextVersion: '16.x.x', // Could dynamically get from package.json
    platform: process.platform,
    env: process.env.NODE_ENV,
  };

  const timestamp = new Date().toISOString();

  return (
    <div className="max-w-6xl mx-auto space-y-6">
      <h2 className="text-2xl font-bold">Admin Panel - Runtime Diagnostics</h2>

      {/* Build Info */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-lg font-semibold mb-4">Build Information</h3>
        <div className="grid grid-cols-2 gap-4 text-sm">
          <InfoRow label="Node Version" value={buildInfo.nodeVersion} />
          <InfoRow label="Next.js Version" value={buildInfo.nextVersion} />
          <InfoRow label="Platform" value={buildInfo.platform} />
          <InfoRow label="Environment" value={buildInfo.env} />
          <InfoRow label="Server Time" value={timestamp} />
        </div>
      </section>

      {/* Routes */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-lg font-semibold mb-4">Application Routes</h3>
        <div className="space-y-2 text-sm font-mono">
          {[
            '/',
            '/request-memoization',
            '/data-cache',
            '/full-route-cache',
            '/router-cache',
            '/cache-components',
            '/invalidation',
            '/mcp-showcase',
            '/v15-vs-v16',
          ].map((route) => (
            <div key={route} className="p-2 bg-gray-50 dark:bg-gray-900 rounded">
              {route}
            </div>
          ))}
        </div>
      </section>

      {/* Environment Variables (safe subset) */}
      <section className="p-6 border rounded-lg">
        <h3 className="text-lg font-semibold mb-4">Environment</h3>
        <p className="text-xs text-gray-500 mb-3">(Showing safe public vars only)</p>
        <div className="space-y-2 text-sm">
          <InfoRow label="NODE_ENV" value={process.env.NODE_ENV || 'N/A'} />
          <InfoRow label="NEXT_RUNTIME" value={process.env.NEXT_RUNTIME || 'nodejs'} />
        </div>
      </section>
    </div>
  );
}

function InfoRow({ label, value }: { label: string; value: string }) {
  return (
    <div className="flex justify-between p-3 bg-gray-50 dark:bg-gray-900 rounded">
      <span className="text-gray-600 dark:text-gray-400">{label}:</span>
      <code className="font-mono font-semibold">{value}</code>
    </div>
  );
}
```

### Step 3: Create Server Actions Demo

**Location:** `my-app/app/(dashboard)/mcp-showcase/server-actions/page.tsx`

```typescript
import { ExampleServerAction } from './components/ExampleServerAction';

export default function ServerActionsPage() {
  return (
    <div className="max-w-4xl mx-auto space-y-8">
      <section>
        <h2 className="text-2xl font-bold mb-4">Server Actions Demo</h2>
        <p className="text-sm text-gray-600 dark:text-gray-400">
          Server Actions are Next.js's way to handle runtime interactions from UI.
          They're the "MCP-like" feature for app developers (vs AI agents).
        </p>
      </section>

      <ExampleServerAction />

      <section className="p-6 border rounded-lg">
        <h3 className="text-lg font-semibold mb-3">How It Works</h3>
        <pre className="text-xs bg-gray-900 text-gray-100 p-4 rounded overflow-x-auto">
{`// Server Action (server-side code)
'use server';
export async function myAction(data: FormData) {
  const value = data.get('field');
  // Access DB, APIs, files, etc.
  return { success: true };
}

// Client Component
'use client';
export function MyForm() {
  async function handleSubmit(formData: FormData) {
    await myAction(formData);
  }
  return <form action={handleSubmit}>...</form>;
}`}
        </pre>
      </section>
    </div>
  );
}
```

### Step 4: Create About MCP Educational Page

**Location:** `my-app/app/(dashboard)/mcp-showcase/about-mcp/page.tsx`

```typescript
export default function AboutMCPPage() {
  return (
    <div className="max-w-4xl mx-auto space-y-8 prose dark:prose-invert max-w-none">
      <h2 className="text-3xl font-bold">About MCP (Model Context Protocol)</h2>

      <section>
        <h3 className="text-xl font-semibold">What is MCP?</h3>
        <p className="text-gray-600 dark:text-gray-400">
          MCP is a protocol introduced in Next.js 16 that exposes runtime information
          to AI coding agents via a <code>/_next/mcp</code> endpoint.
        </p>
      </section>

      <section>
        <h3 className="text-xl font-semibold">Available MCP Tools</h3>
        <div className="space-y-4">
          <MCPTool
            name="get_errors"
            description="Retrieve build/runtime/type errors from dev server"
            example="Shows compilation errors, runtime exceptions, TypeScript errors"
          />
          <MCPTool
            name="get_logs"
            description="Get path to dev server log file with console output"
            example="Access console.log(), server logs, request logs"
          />
          <MCPTool
            name="get_page_metadata"
            description="Get route information, components, rendering mode"
            example="Inspect page structure, static vs dynamic, metadata"
          />
          <MCPTool
            name="get_project_metadata"
            description="Project structure, config, dev server URL"
            example="next.config.js settings, folder structure, dependencies"
          />
          <MCPTool
            name="get_server_action_by_id"
            description="Look up Server Action implementations by ID"
            example="Debug Server Actions, inspect their source code"
          />
        </div>
      </section>

      <section className="p-6 bg-yellow-50 dark:bg-yellow-950 border border-yellow-200 dark:border-yellow-800 rounded-lg">
        <h3 className="text-lg font-semibold mb-2">⚠️ For App Developers</h3>
        <p className="text-sm text-gray-700 dark:text-gray-300">
          MCP is designed for AI agents, not for direct app consumption. For your
          app's runtime needs, use:
        </p>
        <ul className="list-disc list-inside text-sm text-gray-600 dark:text-gray-400 mt-2 space-y-1">
          <li><strong>Server Actions:</strong> Runtime mutations from UI</li>
          <li><strong>Route Handlers:</strong> API endpoints</li>
          <li><strong>Admin Panels:</strong> Server Components with diagnostics</li>
          <li><strong>Error Boundaries:</strong> Error handling in UI</li>
        </ul>
      </section>
    </div>
  );
}

function MCPTool({ name, description, example }: { name: string; description: string; example: string }) {
  return (
    <div className="p-4 border rounded-lg">
      <code className="text-sm font-semibold text-blue-600 dark:text-blue-400">{name}</code>
      <p className="text-sm text-gray-600 dark:text-gray-400 mt-2">{description}</p>
      <p className="text-xs text-gray-500 mt-1">Example: {example}</p>
    </div>
  );
}
```

---

## Expected Results

- Admin panel shows runtime diagnostics
- Server Actions demo works interactively
- Educational docs explain MCP clearly
- Clear distinction between MCP (for AI) and Server Actions (for apps)

---

## Testing

1. Visit `/mcp-showcase`
2. Explore all 3 sections
3. Verify admin panel shows correct info
4. Test Server Actions work

---

**Status:** Ready
**Time:** 30-40 min
