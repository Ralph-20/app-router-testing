# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Structure

This is a monorepo containing:

- **`my-app/`** - Next.js 16 application with React 19, TypeScript, and Tailwind CSS 4
- **`.code-captain/`** - Code Captain documentation and development standards
- **`.claude/`** - Claude.dev agent configurations (for VS Code extension)
- **`.cursor/`** - Cursor IDE custom commands
- **Root** - Contains Code Captain dependencies and shared configuration

**Note**: This repository supports multiple AI development tools (Claude Code, Claude.dev, Cursor). This CLAUDE.md file is specifically for Claude Code instances.

## Development Commands

### Next.js Application (my-app/)

Navigate to `my-app/` directory for all Next.js commands:

```bash
cd my-app

# Development
npm run dev              # Start development server (http://localhost:3000)

# Building
npm run build           # Build production bundle
npm run start           # Start production server

# Code Quality
npm run lint            # Run ESLint
```

### MCP Servers (Claude Code)

This project has MCP (Model Context Protocol) servers configured in `.claude/mcp.json`:

**next-devtools** - Next.js development tools integration
- Inspect Next.js app structure and routes
- Analyze build output and performance
- Debug server and client components

**playwright** - Browser automation and testing
- E2E testing capabilities
- Screenshot and visual testing
- Browser automation for http://localhost:3000

**Usage**: MCP servers are automatically available to Claude Code. Start your Next.js dev server (`cd my-app && npm run dev`) for full functionality.

### Code Captain Workflows (Claude Code)

This project uses Code Captain methodology for structured development. When working with this codebase:

**Project Initialization** (Already completed)
- Brownfield analysis has been performed
- Documentation exists in `.code-captain/docs/`:
  - `tech-stack.md` - Technology decisions
  - `code-style.md` - Coding standards
  - `architecture.md` - System architecture
  - `best-practices.md` - Development principles

**Available Workflows** (Reference `.code-captain/commands/` for detailed methodologies):
- **Product Planning** - Contract-first product discovery with structured clarification
- **Feature Specification** - Create detailed specs with user stories (max 5-7 tasks each)
- **Task Execution** - TDD workflow with mandatory 100% test pass rate
- **Research** - Systematic technical investigation
- **Architecture Decisions** - Document ADRs with alternatives analysis

**Note**: For Cursor IDE users, slash commands are available (see `.cursor/commands/`). For Claude.dev users, agents are available (see `.claude/agents/`).

## Architecture & Workflow

### Code Captain Development Workflow

This repository implements a structured development methodology with contract-first approaches:

1. **Product Planning Workflow**
   - Contract-first product discovery with structured clarification
   - Generates product mission, roadmap, and decision logs
   - Output: `.code-captain/product/` directory with mission.md, roadmap.md, decisions.md
   - One question at a time until 95% confidence, then present contract for approval

2. **Feature Specification Workflow**
   - Contract-based spec creation with clarification rounds
   - Challenge ideas that don't make technical/business sense
   - Breaks features into user stories (max 5-7 tasks per story)
   - Output: `.code-captain/specs/[DATE]-[feature-name]/` with spec.md, user-stories/, sub-specs/
   - User explicitly approves before any files are created

3. **Task Execution Workflow**
   - Test-Driven Development (TDD) workflow: Write tests → Implement → Verify
   - **CRITICAL**: 100% test pass rate mandatory before story completion
   - Zero tolerance for failing tests - stories cannot be marked complete with any failures
   - Updates story files and README.md progress tracking
   - Each story has 5-7 focused implementation tasks

### User Story Structure

User stories are organized in individual files within `user-stories/` folders:

- **`README.md`** - Overview, progress summary, and dependencies
- **`story-N-{name}.md`** - Individual stories with 5-7 focused implementation tasks
- Each story follows: Test → Implement → Verify acceptance criteria
- Stories trace back to user value and acceptance criteria

### Technical Documentation Structure

```
.code-captain/
├── docs/
│   ├── best-practices.md    # Development guidelines and principles
│   ├── code-style.md         # Coding standards (generated during /initialize)
│   └── tech-stack.md         # Technology decisions (generated during /initialize)
├── product/                  # Product planning documents (from /plan-product)
│   ├── mission.md
│   ├── mission-lite.md
│   ├── roadmap.md
│   └── decisions.md
└── specs/                    # Feature specifications (from /create-spec)
    └── [DATE]-[feature-name]/
        ├── spec.md
        ├── spec-lite.md
        ├── user-stories/
        └── sub-specs/
```

## Next.js Application Architecture

### Technology Stack

- **Framework**: Next.js 16 (App Router) - Server-first rendering with React Server Components
- **React**: v19.2.0 with automatic JSX runtime
- **TypeScript**: v5 with strict mode enabled
- **Styling**: Tailwind CSS v4 with CSS-first configuration via PostCSS
- **Fonts**: Geist Sans and Geist Mono (auto-optimized via next/font/google)
- **Linting**: ESLint v9 with Next.js config (core-web-vitals + TypeScript rules)

### Server vs Client Components Architecture

**Key Concept**: Next.js 16 uses Server Components by default for better performance.

- **Server Components (default)**: Render on server, no JavaScript sent to client, can access backend directly
- **Client Components (`'use client'`)**: Interactive components with state, event handlers, and browser APIs
- **Mixed Strategy**: Server Components can import Client Components, but not vice versa

**When to use Client Components:**
- Interactive UI with event handlers (onClick, onChange)
- State management (useState, useReducer)
- Browser-only APIs (localStorage, window)
- React hooks that depend on client-side state

### Project Structure

```
my-app/
├── app/                  # App Router directory (file-system routing)
│   ├── layout.tsx        # Root layout with Geist fonts and metadata
│   ├── page.tsx          # Home page (Server Component by default)
│   ├── globals.css       # Global styles with Tailwind + CSS variables
│   └── favicon.ico
├── public/               # Static assets (SVG logos)
├── next.config.ts        # Next.js configuration
├── tsconfig.json         # TypeScript config with path aliases (@/*)
├── eslint.config.mjs     # ESLint configuration
└── postcss.config.mjs    # PostCSS with Tailwind plugin
```

### TypeScript Configuration

- **Path Aliases**: `@/*` maps to root of my-app directory for cleaner imports
- **Target**: ES2017 for modern browser support
- **JSX**: react-jsx mode (React 19 automatic runtime - no need to import React)
- **Module Resolution**: bundler mode optimized for Next.js
- **Strict Mode**: Enabled for type safety

## Development Principles

### From best-practices.md

**Keep It Simple**
- Implement in fewest lines possible
- Avoid over-engineering
- Straightforward approaches over clever ones

**Critical Thinking**
- Challenge ideas and assumptions with evidence
- Question technical decisions with "what could go wrong?"
- Provide constructive pushback when appropriate
- Focus on data and measurable outcomes over agreement

**Code Quality**
- Optimize for readability over micro-optimizations
- Self-documenting code with clear variable names
- DRY principle: Extract repeated logic to reusable components/utilities
- Single responsibility per file

**Dependencies**
- Choose actively maintained libraries
- Check recent commits (within 6 months), active issue resolution
- Select most popular options with clear documentation

### TDD Workflow (execute-task)

1. **Write tests first** - Comprehensive test cases including edge cases
2. **Implement minimally** - Write just enough code to pass tests
3. **Refactor when green** - Improve code quality while tests pass
4. **100% pass rate required** - Stories cannot be marked complete with any failing tests
5. **Zero tolerance** - "Edge case" or "minor" failures block completion

### Contract-First Development

Both Product Planning and Feature Specification workflows use contract-first approaches:

- **No presumptuous file creation** - establish clear contract first
- **Structured clarification** - one focused question at a time
- **Critical analysis** - challenge ideas that don't make technical/business sense early
- **User approval required** - explicit confirmation before any files are created
- **95% confidence threshold** - gather complete understanding before presenting contract
- **Flexible contracting** - user can edit contract or ask more questions before locking

## Working with Next.js in This Repo

### Development Flow

1. Navigate to `my-app/`: `cd my-app`
2. Install dependencies if needed: `npm install`
3. Start dev server: `npm run dev`
4. Access at http://localhost:3000
5. Edit files in `app/` directory - hot reload enabled

### Creating New Pages

Use App Router conventions:
- Create directories in `app/` for routes
- `page.tsx` exports default component for route
- `layout.tsx` for shared layouts
- Server Components by default (add 'use client' for client components)

### Styling Architecture

**Tailwind CSS v4 with CSS-First Configuration:**

- **CSS Custom Properties**: Theme defined in `globals.css` via CSS variables (`--background`, `--foreground`)
- **Dark Mode**: Automatic via `prefers-color-scheme` media query (no manual toggle needed)
- **Font Integration**: Geist fonts exposed as CSS variables (`--font-geist-sans`, `--font-geist-mono`)
- **@theme Directive**: Inline theme configuration in globals.css
- **Utility-First**: Compose styles via utility classes (e.g., `flex items-center dark:bg-black`)

**Example from codebase:**
```css
/* globals.css - Theme configuration */
:root {
  --background: #ffffff;
  --foreground: #171717;
}

@theme inline {
  --color-background: var(--background);
  --font-sans: var(--font-geist-sans);
}

@media (prefers-color-scheme: dark) {
  :root {
    --background: #0a0a0a;
    --foreground: #ededed;
  }
}
```

## Code Style Patterns

### Component Structure

**Server Component (default):**
```typescript
// app/page.tsx - No 'use client' needed
export default async function Page() {
  const data = await fetchData(); // Can fetch directly
  return <div>{data}</div>;
}
```

**Client Component (interactive):**
```typescript
// app/components/Counter.tsx
'use client'; // Required for interactivity

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Import Organization

Follow this order (from code-style.md):
1. React and Next.js imports
2. Third-party libraries
3. Internal imports (use `@/*` alias)
4. Types
5. Styles (if needed)

### Metadata Pattern

```typescript
// app/layout.tsx or page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Page Title',
  description: 'Page description for SEO',
};
```

## Current Gaps & Future Considerations

**Testing Framework**: Not yet configured
- When needed: Add Jest + React Testing Library for unit tests
- For E2E: Consider Playwright or Cypress
- Required before using `/execute-task` TDD workflow effectively

**State Management**: Not yet needed
- Add when complexity demands (Zustand, Jotai, or React Query)
- Current setup supports local state via useState

**Database**: Not configured
- Choose based on product requirements (PostgreSQL, MongoDB, Prisma ORM)
- Server Components make data fetching straightforward

**Authentication**: Not implemented
- Consider NextAuth.js when user accounts needed
- Integrates well with Server Components

## Important Notes

- **Monorepo Navigation**: Always specify which directory when running commands (`cd my-app` for Next.js commands)
- **Test Requirements**: Task execution workflow requires 100% test pass rate - zero tolerance for failing tests
- **Specification Structure**: User stories are individual files (5-7 tasks each) for better organization and parallel work
- **Path Aliases**: Use `@/*` imports within my-app for cleaner, portable code
- **Contract-First**: Product Planning and Feature Specification workflows require explicit user approval before generating files
- **Server-First**: Default to Server Components unless interactivity is needed
- **No React Imports**: React 19 automatic JSX runtime means no need to `import React` in components
- **Critical Thinking**: Challenge assumptions and provide evidence-based pushback when needed (from best-practices.md)
- **Documentation**: All workflows generate structured documentation in `.code-captain/` directories
