# Plan 11: Shared UI Components

**Date:** 2025-11-10
**Goal:** Create reusable UI components for consistent styling and better DX

---

## Overview

Shared components used across all cache demos:
1. CacheStatus badge
2. TimestampDisplay
3. LoadingCard skeleton
4. CodeBlock for examples
5. InfoCard for explanations

---

## Implementation Steps

### Step 1: Create CacheStatus Badge

**Location:** `my-app/app/components/CacheStatus.tsx`

```typescript
/**
 * Visual badge showing cache status (HIT, MISS, STALE, REVALIDATING)
 * Color-coded for quick identification
 */
export function CacheStatus({ status }: { status: 'HIT' | 'MISS' | 'STALE' | 'REVALIDATING' }) {
  const styles = {
    HIT: 'bg-green-100 dark:bg-green-900 text-green-800 dark:text-green-200 border-green-200 dark:border-green-800',
    MISS: 'bg-red-100 dark:bg-red-900 text-red-800 dark:text-red-200 border-red-200 dark:border-red-800',
    STALE: 'bg-yellow-100 dark:bg-yellow-900 text-yellow-800 dark:text-yellow-200 border-yellow-200 dark:border-yellow-800',
    REVALIDATING: 'bg-blue-100 dark:bg-blue-900 text-blue-800 dark:text-blue-200 border-blue-200 dark:border-blue-800',
  };

  const icons = {
    HIT: '✓',
    MISS: '✗',
    STALE: '⚠',
    REVALIDATING: '↻',
  };

  return (
    <span className={`inline-flex items-center gap-1 text-xs px-2 py-1 rounded border font-semibold ${styles[status]}`}>
      <span>{icons[status]}</span>
      <span>{status}</span>
    </span>
  );
}
```

### Step 2: Create TimestampDisplay

**Location:** `my-app/app/components/TimestampDisplay.tsx`

```typescript
/**
 * Displays formatted timestamp with label
 * Shows both full ISO and human-readable time
 */
export function TimestampDisplay({
  timestamp,
  label = 'Timestamp',
  showDate = false,
}: {
  timestamp: string;
  label?: string;
  showDate?: boolean;
}) {
  const date = new Date(timestamp);
  const timeOnly = date.toLocaleTimeString();
  const dateTime = date.toLocaleString();

  return (
    <div className="text-xs text-gray-600 dark:text-gray-400">
      <span className="font-semibold">{label}:</span>{' '}
      <code className="font-mono bg-gray-100 dark:bg-gray-800 px-1 py-0.5 rounded">
        {showDate ? dateTime : timeOnly}
      </code>
    </div>
  );
}

/**
 * Shows time elapsed between two timestamps
 */
export function TimestampDiff({
  start,
  end,
  label = 'Duration',
}: {
  start: string;
  end: string;
  label?: string;
}) {
  const startDate = new Date(start);
  const endDate = new Date(end);
  const diff = endDate.getTime() - startDate.getTime();

  const color = diff < 10 ? 'text-green-600 dark:text-green-400' : 'text-orange-600 dark:text-orange-400';

  return (
    <div className={`text-xs font-semibold ${color}`}>
      {label}: {diff}ms
      {diff < 10 && ' (likely cached)'}
      {diff > 100 && ' (network request)'}
    </div>
  );
}
```

### Step 3: Create LoadingCard Skeleton

**Location:** `my-app/app/components/LoadingCard.tsx`

```typescript
/**
 * Loading skeleton for Suspense fallbacks
 * Matches card dimensions for smooth loading
 */
export function LoadingCard({ height = 'h-32' }: { height?: string }) {
  return (
    <div className={`${height} p-4 border border-gray-200 dark:border-gray-800 rounded-lg animate-pulse`}>
      <div className="h-4 bg-gray-200 dark:bg-gray-700 rounded w-3/4 mb-3"></div>
      <div className="h-3 bg-gray-200 dark:bg-gray-700 rounded w-full mb-2"></div>
      <div className="h-3 bg-gray-200 dark:bg-gray-700 rounded w-5/6"></div>
    </div>
  );
}

/**
 * Inline loading spinner
 */
export function LoadingSpinner({ size = 'md' }: { size?: 'sm' | 'md' | 'lg' }) {
  const sizeClasses = {
    sm: 'w-4 h-4',
    md: 'w-6 h-6',
    lg: 'w-8 h-8',
  };

  return (
    <div className={`${sizeClasses[size]} animate-spin rounded-full border-2 border-gray-300 border-t-blue-600`}></div>
  );
}
```

### Step 4: Create CodeBlock

**Location:** `my-app/app/components/CodeBlock.tsx`

```typescript
/**
 * Syntax-highlighted code block
 * For showing examples throughout demos
 */
export function CodeBlock({
  code,
  language = 'typescript',
  title,
}: {
  code: string;
  language?: string;
  title?: string;
}) {
  return (
    <div className="rounded-lg overflow-hidden border border-gray-200 dark:border-gray-800">
      {title && (
        <div className="px-4 py-2 bg-gray-100 dark:bg-gray-900 border-b border-gray-200 dark:border-gray-800">
          <span className="text-sm font-semibold">{title}</span>
          <span className="ml-2 text-xs text-gray-500">{language}</span>
        </div>
      )}
      <pre className="p-4 bg-gray-900 text-gray-100 overflow-x-auto text-xs">
        <code>{code}</code>
      </pre>
    </div>
  );
}

/**
 * Inline code snippet
 */
export function InlineCode({ children }: { children: React.ReactNode }) {
  return (
    <code className="px-1.5 py-0.5 bg-gray-200 dark:bg-gray-800 rounded text-sm font-mono">
      {children}
    </code>
  );
}
```

### Step 5: Create InfoCard

**Location:** `my-app/app/components/InfoCard.tsx`

```typescript
/**
 * Colored info/warning/success cards
 * For explanations and important notes
 */
export function InfoCard({
  type = 'info',
  title,
  children,
}: {
  type?: 'info' | 'warning' | 'success' | 'error';
  title?: string;
  children: React.ReactNode;
}) {
  const styles = {
    info: 'bg-blue-50 dark:bg-blue-950 border-blue-200 dark:border-blue-800 text-blue-900 dark:text-blue-100',
    warning: 'bg-yellow-50 dark:bg-yellow-950 border-yellow-200 dark:border-yellow-800 text-yellow-900 dark:text-yellow-100',
    success: 'bg-green-50 dark:bg-green-950 border-green-200 dark:border-green-800 text-green-900 dark:text-green-100',
    error: 'bg-red-50 dark:bg-red-950 border-red-200 dark:border-red-800 text-red-900 dark:text-red-100',
  };

  const icons = {
    info: 'ℹ️',
    warning: '⚠️',
    success: '✅',
    error: '❌',
  };

  return (
    <div className={`p-4 border rounded-lg ${styles[type]}`}>
      {title && (
        <div className="flex items-center gap-2 font-semibold mb-2">
          <span>{icons[type]}</span>
          <span>{title}</span>
        </div>
      )}
      <div className="text-sm">{children}</div>
    </div>
  );
}
```

### Step 6: Create Section Container

**Location:** `my-app/app/components/Section.tsx`

```typescript
/**
 * Consistent section container with optional header
 */
export function Section({
  title,
  description,
  children,
  bordered = true,
}: {
  title?: string;
  description?: string;
  children: React.ReactNode;
  bordered?: boolean;
}) {
  return (
    <section className={bordered ? 'p-6 border border-gray-200 dark:border-gray-800 rounded-lg' : ''}>
      {title && (
        <div className="mb-4">
          <h3 className="text-xl font-semibold mb-2">{title}</h3>
          {description && (
            <p className="text-sm text-gray-600 dark:text-gray-400">{description}</p>
          )}
        </div>
      )}
      {children}
    </section>
  );
}
```

### Step 7: Create Button Components

**Location:** `my-app/app/components/Button.tsx`

```typescript
'use client';

/**
 * Reusable button with loading state
 */
export function Button({
  onClick,
  children,
  variant = 'primary',
  loading = false,
  disabled = false,
  size = 'md',
}: {
  onClick?: () => void | Promise<void>;
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'danger';
  loading?: boolean;
  disabled?: boolean;
  size?: 'sm' | 'md' | 'lg';
}) {
  const baseStyles = 'rounded-md font-medium transition-colors disabled:opacity-50 disabled:cursor-not-allowed';

  const variants = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white',
    secondary: 'bg-gray-200 hover:bg-gray-300 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-900 dark:text-gray-100',
    danger: 'bg-red-600 hover:bg-red-700 text-white',
  };

  const sizes = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-sm',
    lg: 'px-6 py-3 text-base',
  };

  return (
    <button
      onClick={onClick}
      disabled={disabled || loading}
      className={`${baseStyles} ${variants[variant]} ${sizes[size]}`}
    >
      {loading ? (
        <span className="flex items-center gap-2">
          <LoadingSpinner size="sm" />
          <span>Loading...</span>
        </span>
      ) : (
        children
      )}
    </button>
  );
}

import { LoadingSpinner } from './LoadingCard';
```

### Step 8: Create Component Index

**Location:** `my-app/app/components/index.ts`

```typescript
// Export all shared components for easy imports
export { CacheStatus } from './CacheStatus';
export { TimestampDisplay, TimestampDiff } from './TimestampDisplay';
export { LoadingCard, LoadingSpinner } from './LoadingCard';
export { CodeBlock, InlineCode } from './CodeBlock';
export { InfoCard } from './InfoCard';
export { Section } from './Section';
export { Button } from './Button';
```

---

## Usage Examples

**In any page:**

```typescript
import {
  CacheStatus,
  TimestampDisplay,
  CodeBlock,
  InfoCard,
  Section
} from '@/app/components';

export default function MyPage() {
  return (
    <Section title="Cache Demo" description="Example usage">
      <CacheStatus status="HIT" />
      <TimestampDisplay timestamp={new Date().toISOString()} />

      <InfoCard type="info" title="Note">
        This demonstrates cache behavior
      </InfoCard>

      <CodeBlock code="const x = 1;" language="typescript" />
    </Section>
  );
}
```

---

## Expected Results

- All components render consistently
- Dark mode support works
- Loading states smooth
- Code blocks readable
- Easy to import and use

---

## Testing

1. Import components in demo pages
2. Verify dark mode switching works
3. Test loading states
4. Check responsive design
5. Validate accessibility (color contrast)

---

**Status:** Ready
**Time:** 30-40 min
