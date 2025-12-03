# State Management - Web

## Overview

The web application uses **Jotai** for atomic state management, providing lightweight and performant state management.

**State Management Library:** Jotai 2.10.2

## State Structure

### Access State

**File:** `apps/web/src/state/access.ts`

```typescript
export const accessState = atom<string[]>([])
```

**Purpose:** Stores user access permissions array

**Usage:** Provided by business logic, used for permission checks

---

### Profile State

**File:** `apps/web/src/state/profile.ts`

```typescript
export const isLoginState = atom<boolean>(false)
export const profileState = atom<Profile | null>(null)
```

**Purpose:**
- `isLoginState`: Tracks user login status
- `profileState`: Stores current user profile information

**Type:** `Profile` from `@meta-1/authub-types`

---

### App State

**File:** `apps/web/src/state/app.ts`

```typescript
export const currentAppState = atom<AppResponse | undefined>(undefined)
```

**Purpose:** Stores current application context

**Type:** `AppResponse` from `@meta-1/authub-types`

---

### Config State

**File:** `apps/web/src/state/config.ts`

**Purpose:** Application configuration state

---

### Layout State

**File:** `apps/web/src/state/layout.ts`

**Purpose:** Layout-related state (theme, sidebar, etc.)

---

### Public State

**File:** `apps/web/src/state/public.ts`

**Purpose:** Public/shared state accessible across the application

---

## Data Fetching

**Library:** TanStack Query (React Query) 5.80.3

**Purpose:** Server state management, caching, and synchronization

**Features:**
- Automatic caching
- Background refetching
- Optimistic updates
- DevTools integration

**Hydration:** Server-side state hydration via `HydrationBoundary` component

## State Management Patterns

### Atomic State (Jotai)

- **Pattern:** Atomic state management
- **Benefits:** 
  - Fine-grained reactivity
  - Automatic re-renders only for changed atoms
  - Composable state logic
  - No provider wrapping needed

### Server State (TanStack Query)

- **Pattern:** Server state caching and synchronization
- **Benefits:**
  - Automatic background updates
  - Request deduplication
  - Cache invalidation
  - Optimistic updates

## State Persistence

- **Client-side:** LocalStorage/Cookies via `cookies-next` and `js-cookie`
- **Server-side:** Server state loader for SSR hydration

## State Flow

1. **Initial Load:** Server-side state hydration → Client-side Jotai atoms
2. **User Actions:** Update Jotai atoms → Trigger TanStack Query mutations
3. **Server Updates:** TanStack Query refetch → Update Jotai atoms

