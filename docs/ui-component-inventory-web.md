# UI Component Inventory - Web

## Overview

This document catalogs all UI components in the Authub web application.

**Component Library:** @meta-1/design 0.0.178  
**Styling:** Tailwind CSS 3.4.0  
**Base Components:** Radix UI

## Component Categories

### Layout Components

#### Root Layout
- **Path:** `components/layout/root/index.tsx`
- **Purpose:** Root layout wrapper

#### HTML Layout
- **Path:** `components/layout/html/index.tsx`
- **Purpose:** HTML structure and metadata

#### Main Layout
- **Path:** `components/layout/main/index.tsx`
- **Purpose:** Main application layout
  - Header with menus and profile
  - Footer
  - Navigation structure

#### Login Layout
- **Path:** `components/layout/login/index.tsx`
- **Purpose:** Login/authentication page layout

#### App Layout
- **Path:** `components/layout/app/index.tsx`
- **Purpose:** Application-specific layout

#### Loading Layout
- **Path:** `components/layout/loading/index.tsx`
- **Purpose:** Loading state layout

---

### Common Components

#### Access Control
- **Path:** `components/common/access/index.tsx`
- **Purpose:** Permission-based access control wrapper

#### Account Components
- **OTP Info:** `components/common/account/otp-info/index.tsx`
  - Displays OTP status and information

#### Atoms Hydrate
- **Path:** `components/common/atoms-hydrate/index.tsx`
- **Purpose:** Hydrates Jotai atoms from server state

#### Breadcrumb
- **Path:** `components/common/breadcrumb/index.tsx`
- **Variants:**
  - Apps breadcrumb: `components/common/breadcrumb/apps/index.tsx`
  - Profile breadcrumb: `components/common/breadcrumb/profile/index.tsx`
  - Users breadcrumb: `components/common/breadcrumb/users/index.tsx`

#### Coming Soon
- **Path:** `components/common/coming/index.tsx`
- **Purpose:** Placeholder for upcoming features

#### Cropper
- **Path:** `components/common/cropper/index.tsx`
- **Dialog:** `components/common/cropper/dialog/index.tsx`
- **Purpose:** Image cropping functionality (for avatar upload)

#### Input Components
- **Code Input:** `components/common/input/code/index.tsx`
- **Email Code Input:** `components/common/input/email-code/index.tsx`
- **OTP Input:** `components/common/input/otp/index.tsx`
  - Uses `input-otp` library for OTP code input

#### Logo
- **Path:** `components/common/logo/index.tsx`
- **Purpose:** Application logo component

#### Page Components
- **Page:** `components/common/page/index.tsx`
  - Main page wrapper
- **Page Header:** `components/common/page/header/index.tsx`
- **Page Title Bar:** `components/common/page/title-bar/index.tsx`
- **App Page:** `components/common/page/app/index.tsx`
  - Application-specific page layout
  - Includes hooks and configuration

#### Select Components
- **Language Selector:** `components/common/select/lang/index.tsx`
  - Language switching dropdown

#### Server State Loader
- **Path:** `components/common/server-state-loader/index.tsx`
- **State:** `components/common/server-state-loader/state.ts`
- **Purpose:** Loads and hydrates server-side state

#### Tabs Title
- **Path:** `components/common/tabs-title/index.tsx`
- **Purpose:** Tab navigation component

#### Theme Components
- **Theme Switcher:** `components/common/theme-switcher/index.tsx`
  - Theme toggle component
- **Theme Sync Provider:** `components/common/theme-sync-provider/index.tsx`
  - Syncs theme across components

---

### Background Components

#### Iridescence Background
- **Path:** `components/background/iridescence/index.tsx`
- **Style:** `components/background/iridescence/style.css`
- **Purpose:** Animated background effect

---

### Layout Hooks

- **Layout Config Hook:** `components/layout/hooks/use.layout.config.ts`
- **Layout Props Hook:** `components/layout/hooks/use.layout.props.ts`

---

## Component Patterns

### Design System
- Uses `@meta-1/design` component library
- Consistent styling with Tailwind CSS
- Radix UI primitives for accessibility

### Component Structure
- **Location:** `apps/web/src/components/`
- **Organization:** By category (layout, common, background)
- **Naming:** kebab-case for directories, PascalCase for components

### Reusability
- Common components in `components/common/`
- Layout components in `components/layout/`
- Specialized components in feature-specific directories

## Component Usage Patterns

1. **Server Components:** Next.js App Router server components
2. **Client Components:** Marked with `'use client'` directive
3. **State Management:** Jotai atoms for component state
4. **Data Fetching:** TanStack Query hooks

## Styling Approach

- **Utility-first:** Tailwind CSS classes
- **Component Variants:** Using classnames utility
- **Theme Support:** Dark/light theme via next-themes
- **Responsive:** Mobile-first responsive design

