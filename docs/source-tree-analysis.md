# Source Tree Analysis

## Project Structure Overview

This is a **monorepo** project with multiple parts organized in a workspace structure.

```
authub/
├── apps/                    # Applications
│   ├── server/             # NestJS Backend Service
│   └── web/                # Next.js Frontend Application
├── libs/                    # Shared Libraries
│   ├── account/            # Account Management Module
│   ├── app/                # App Management Module
│   ├── common/             # Common Utilities
│   └── types/              # Shared Type Definitions
├── docs/                    # Documentation
├── locales/                 # Internationalization Files
├── scripts/                 # Build and Utility Scripts
└── [config files]          # Root Configuration
```

---

## Part 1: Web Application (`apps/web/`)

**Type:** Web Application (Next.js)  
**Purpose:** Frontend user interface for authentication and account management

### Critical Directories

```
apps/web/
├── src/
│   ├── app/                # Next.js App Router pages
│   │   ├── (login)/        # Login layout group
│   │   │   ├── login/      # Login page
│   │   │   └── register/   # Registration page
│   │   ├── (main)/         # Main layout group
│   │   │   ├── apps/       # Apps management pages
│   │   │   ├── profile/    # User profile pages
│   │   │   └── users/      # Users management pages
│   │   └── layout.tsx      # Root layout
│   ├── components/         # React components
│   │   ├── common/         # Reusable common components
│   │   ├── layout/         # Layout components
│   │   └── background/     # Background effects
│   ├── rest/               # API client functions
│   │   ├── account.ts      # Account API
│   │   ├── profile/        # Profile API
│   │   └── public.ts       # Public API
│   ├── state/              # Jotai state atoms
│   │   ├── access.ts       # Access permissions state
│   │   ├── profile.ts      # User profile state
│   │   └── app.ts          # App context state
│   ├── hooks/              # Custom React hooks
│   ├── utils/              # Utility functions
│   ├── config/             # Configuration
│   ├── events/             # Event handlers
│   ├── plugin/             # Plugins (REST, locales, middleware)
│   ├── schema/             # Zod schemas
│   └── types/              # TypeScript types
├── public/                 # Static assets
│   └── assets/             # Images and icons
├── next.config.ts          # Next.js configuration
└── package.json            # Dependencies
```

### Entry Points

- **Main Entry:** `src/app/layout.tsx` - Root layout component
- **Pages:** `src/app/(main)/page.tsx` - Home page
- **API Routes:** Next.js App Router API routes (if any)

### Integration Points

- **→ Server:** REST API calls via `src/rest/*` → `apps/server/api/*`
- **→ Types:** Uses `@meta-1/authub-types` from `libs/types`

---

## Part 2: Server Application (`apps/server/`)

**Type:** Backend Service (NestJS)  
**Purpose:** RESTful API backend for authentication and account management

### Critical Directories

```
apps/server/
├── src/
│   ├── controller/         # REST API controllers
│   │   ├── app.controller.ts      # Default/welcome endpoint
│   │   ├── assets.controller.ts   # File upload endpoints
│   │   ├── config.controller.ts   # Configuration endpoints
│   │   └── mail-code.controller.ts # Email code endpoints
│   ├── dto/                # Data Transfer Objects
│   ├── main.ts             # Application entry point
│   ├── app.module.ts       # Root module
│   └── app.swagger.ts      # Swagger documentation setup
└── tsconfig.app.json       # TypeScript config
```

### Entry Points

- **Main Entry:** `src/main.ts` - Bootstrap NestJS application
- **Module:** `src/app.module.ts` - Root module configuration

### Integration Points

- **→ Account Module:** Uses `@meta-1/authub-account` from `libs/account`
- **→ App Module:** Uses `@meta-1/authub-app` from `libs/app`
- **→ Common:** Uses `@meta-1/authub-common` from `libs/common`
- **→ Database:** TypeORM entities from `libs/*/entity/`
- **→ External:** Nacos config, Redis cache, MySQL database

---

## Part 3: Shared Libraries (`libs/`)

### Account Library (`libs/account/`)

**Purpose:** Account management, authentication, OTP functionality

```
libs/account/
├── src/
│   ├── controller/         # Account API controllers
│   │   ├── account.controller.ts      # Account CRUD
│   │   └── account-otp.controller.ts   # OTP management
│   ├── service/            # Business logic
│   │   ├── account.service.ts
│   │   ├── account-otp.service.ts
│   │   └── account-config.service.ts
│   ├── entity/             # TypeORM entities
│   │   ├── account.entity.ts
│   │   └── account-token.entity.ts
│   ├── dto/                # DTOs for API
│   ├── guards/             # Auth guards
│   │   └── auth.guard.ts
│   └── shared/             # Shared constants and types
└── tsconfig.lib.json
```

### App Library (`libs/app/`)

**Purpose:** Application management functionality

```
libs/app/
├── src/
│   ├── controller/         # App API controllers
│   ├── service/            # App business logic
│   ├── entity/             # App entities
│   │   ├── app.entity.ts
│   │   └── app-account.entity.ts
│   └── dto/                # App DTOs
└── tsconfig.lib.json
```

### Common Library (`libs/common/`)

**Purpose:** Shared utilities and services

```
libs/common/
├── src/
│   ├── service/            # Common services
│   │   └── config.service.ts
│   └── shared/             # Shared constants and types
└── tsconfig.lib.json
```

### Types Library (`libs/types/`)

**Purpose:** Shared TypeScript types and Zod schemas

```
libs/types/
├── src/
│   ├── account/            # Account-related types
│   ├── app/                # App-related types
│   └── common.types.ts     # Common types
└── tsconfig.lib.json
```

---

## Root Level Files

### Configuration Files

- `package.json` - Root package.json with workspace configuration
- `pnpm-workspace.yaml` - pnpm workspace configuration
- `tsconfig.json` - Root TypeScript configuration
- `tsconfig.build.json` - Build TypeScript configuration
- `tsconfig.web.json` - Web-specific TypeScript configuration
- `nest-cli.json` - NestJS CLI configuration
- `biome.json` - Biome linter/formatter configuration
- `webpack.config.js` - Webpack configuration for NestJS

### Documentation

- `README.md` - Project overview and quick start guide
- `docs/` - Generated documentation

### Scripts

- `scripts/sync-locales-cli.ts` - Locale synchronization script

### Internationalization

- `locales/en.json` - English translations
- `locales/zh-CN.json` - Chinese translations

---

## Integration Architecture

### Communication Flow

1. **Web → Server:**
   - REST API calls via `apps/web/src/rest/*`
   - Uses `@/utils/rest` for HTTP client
   - Base URL configured via proxy or environment

2. **Server → Libraries:**
   - Imports modules from `libs/*`
   - Uses dependency injection pattern
   - Shared entities and DTOs

3. **Shared Types:**
   - `libs/types` provides types for both web and server
   - Zod schemas for validation
   - Shared across monorepo via TypeScript path aliases

### Key Integration Points

- **Authentication:** `libs/account` provides auth guards and services
- **API Contracts:** Defined in controllers, shared via types
- **State Management:** Web uses Jotai, Server uses NestJS DI
- **Database:** TypeORM entities in `libs/*/entity/`
- **Configuration:** Nacos for server, environment variables for web

---

## Critical Folders Summary

| Folder | Purpose | Critical Files |
|--------|---------|----------------|
| `apps/web/src/app/` | Next.js pages and routes | `layout.tsx`, page components |
| `apps/web/src/components/` | React components | Layout, common components |
| `apps/web/src/rest/` | API client functions | Account, profile APIs |
| `apps/server/src/controller/` | REST API endpoints | All controller files |
| `libs/account/src/` | Account module | Services, entities, controllers |
| `libs/types/src/` | Shared types | Type definitions and schemas |

---

## Entry Points Summary

- **Web Application:** `apps/web/src/app/layout.tsx`
- **Server Application:** `apps/server/src/main.ts`
- **Build:** Root `package.json` scripts
- **Development:** `pnpm run dev:web` or `pnpm run dev:server`

