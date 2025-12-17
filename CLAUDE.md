# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DecoTV is a cross-platform video aggregator and streaming platform (影视聚合播放器) built with Next.js 16 (App Router + Turbopack), React 19, TypeScript, and Tailwind CSS. It supports PWA, multiple database backends, and TVBox integration.

## Common Commands

```bash
# Development
pnpm dev                 # Start dev server on 0.0.0.0
pnpm gen:manifest        # Generate PWA manifest

# Build
pnpm build               # Production build
pnpm docker:build        # Build Docker image
pnpm docker:run          # Run Docker container on port 3000

# Testing & Type Checking
pnpm test                # Run Jest tests
pnpm test:watch          # Jest watch mode
pnpm typecheck           # TypeScript type checking

# Linting & Formatting
pnpm lint                # Next.js lint
pnpm lint:fix            # ESLint fix + Prettier format
pnpm lint:strict         # ESLint with max-warnings=0
pnpm format              # Prettier write mode
```

## Architecture

### Directory Structure

- `src/app/` - Next.js App Router pages and API routes (55+ route handlers)
- `src/components/` - React components (35 total)
- `src/lib/` - Utility and library files (28 modules)
- `src/hooks/` - Custom React hooks
- `public/` - Static assets, PWA manifest, service worker

### Key Architectural Patterns

**Storage Abstraction Layer** (`src/lib/`):

- `IStorage` interface in `types.ts` defines the storage contract
- Implementations: `redis.db.ts`, `upstash.db.ts`, `kvrocks.db.ts`
- `DbManager` in `db.ts` uses factory pattern for storage selection
- Supports: LocalStorage (client), Redis, Upstash Redis, Kvrocks

**Configuration Management** (`src/lib/config.ts`):

- In-memory caching with DB persistence
- `getConfig()` loads/caches AdminConfig
- `configSelfCheck()` validates configuration

**Runtime Configuration Injection** (`src/app/layout.tsx`):

- Server-side config injected via `window.RUNTIME_CONFIG`
- Environment variables mapped to client-accessible config

**Context Providers** (root layout):

```
ThemeProvider → SiteProvider → {children}
```

### Component Hierarchy

- `PageLayout` - Standard page container
- `NavbarGate` → `TopNavbar` / `MobileHeader` / `MobileBottomNav`
- `VideoCard` - Primary content display (complex, 38KB)
- `DoubanSelector` / `DoubanCustomSelector` - Content discovery
- `UserMenu` - User account management (complex, 45KB)

### API Routes Structure (`src/app/api/`)

- `/api/search/` - Multi-source video search
- `/api/detail/` - Video details
- `/api/play/` - Playback URLs
- `/api/live/` - Live TV streaming
- `/api/douban/` - Douban integration
- `/api/admin/` - Admin configuration
- `/api/tvbox/` - TVBox client API
- `/api/user/` - Authentication and user management

## Code Conventions

### Import Order (enforced by ESLint)

1. External packages (`react`, `next`, etc.)
2. CSS files
3. `@/lib`, `@/hooks`
4. `@/data`
5. `@/components`, `@/container`
6. `@/store`
7. Relative imports

### Path Aliases

- `@/*` → `./src/*`
- `~/*` → `./public/*`

### Commit Message Format

Types: `feat`, `fix`, `docs`, `chore`, `style`, `refactor`, `ci`, `test`, `perf`, `revert`, `vercel`

## Key Features to Understand

**Multi-Source Search**: Aggregates results from multiple video APIs, with intelligent ranking and deduplication.

**Adult Content Filtering**: Multi-level system - URL params (`?adult=1`), global config (`DisableYellowFilter`), source flags (`is_adult`), keyword filtering.

**Authentication**: Cookie-based JWT-style auth with role-based access control (owner/admin/user). See `getAuthInfoFromCookie()`.

**Caching Strategy**:

- Server: In-memory config cache with DB persistence
- Client: LocalStorage for favorites/play records
- API: Redis-based caching (7200s default TTL)

## Environment Variables

Key variables (see README.md for full list):

- `NEXT_PUBLIC_STORAGE_TYPE` - Database backend selection
- `REDIS_URL` / `UPSTASH_REDIS_REST_URL` - Database connection
- `SITE_PASSWORD` - Site access password
- `ADMIN_PASSWORD` - Admin panel password

## Video Players

The project uses multiple video player libraries:

- ArtPlayer (primary)
- Vidstack
- HLS.js for streaming
