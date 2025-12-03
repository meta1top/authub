# Development Guide

## Prerequisites

### Required Software

- **Node.js** >= 18
- **pnpm** >= 8
- **Redis** >= 6.0
- **MySQL** >= 8.0
- **Nacos** >= 2.0 (optional, for configuration management)

### System Requirements

- Operating System: macOS, Linux, or Windows (with WSL)
- RAM: Minimum 4GB (8GB recommended)
- Disk Space: At least 2GB free space

## Installation

### Clone Repository

```bash
git clone <repository-url>
cd authub
```

### Install Dependencies

```bash
# Install all dependencies for monorepo
pnpm install
```

This will install dependencies for:
- Root workspace
- `apps/web` (Next.js frontend)
- `apps/server` (NestJS backend)
- All libraries in `libs/`

## Environment Setup

### Server Environment Variables

Create `apps/server/.env` file (if not exists):

```env
# Application Configuration
NODE_ENV=development
PORT=3100

# Nacos Configuration (Required)
NACOS_SERVER=localhost:8848
APP_NAME=authub-server
```

**Important:** Only the above environment variables need to be configured in `.env`. All other configurations (database, Redis, account settings) are managed through Nacos configuration center.

### Nacos Configuration

Create a configuration in Nacos with Data ID: `authub-server`, format: YAML:

```yaml
# Database Configuration
database:
  host: localhost
  port: 3306
  username: root
  password: your-password
  database: authub
  synchronize: false  # Set to false in production
  logging: false

# Redis Configuration
redis:
  host: localhost
  port: 6379
  password: ""
  db: 0

# Account Configuration
account:
  rsa:
    privateKey: |
      -----BEGIN RSA PRIVATE KEY-----
      your-private-key
      -----END RSA PRIVATE KEY-----
    publicKey: |
      -----BEGIN PUBLIC KEY-----
      your-public-key
      -----END PUBLIC KEY-----
  jwt:
    secret: your-jwt-secret
    expiresIn: 7d
  otp:
    issuer: AuthHub
```

**Note:** If Nacos is unavailable, the service will start in degraded mode (skipping database and Redis initialization).

### Web Environment Variables

The web application uses Next.js environment variables. Create `.env.local` in `apps/web/` if needed:

```env
NEXT_PUBLIC_API_URL=http://localhost:3100
```

## Development Commands

### Backend Development

```bash
# Start server in development mode (watch mode)
pnpm run dev:server

# Build server for production
pnpm run build:server

# Start production server
pnpm run start:server
```

**Server runs on:** http://localhost:3100

**Swagger API Documentation:** http://localhost:3100/api

### Frontend Development

```bash
# Start web app in development mode
pnpm run dev:web

# Build web app for production
pnpm run build:web

# Start production web app
pnpm run start:web
```

**Web app runs on:** http://localhost:4002 (or port specified in package.json)

### Utility Commands

```bash
# Sync locale files
pnpm run sync:locales

# Lint code
pnpm run lint

# Format code
pnpm run format

# Run tests
pnpm run test

# Run tests in watch mode
pnpm run test:watch

# Run tests with coverage
pnpm run test:cov

# Run E2E tests
pnpm run test:e2e
```

## Project Structure

```
authub/
├── apps/                    # Applications
│   ├── server/             # NestJS Backend Service
│   │   ├── src/
│   │   │   ├── controller/ # REST API controllers
│   │   │   ├── dto/        # Data Transfer Objects
│   │   │   ├── main.ts     # Application entry point
│   │   │   └── app.module.ts
│   │   └── tsconfig.app.json
│   └── web/                # Next.js Frontend Application
│       ├── src/
│       │   ├── app/        # Next.js App Router pages
│       │   ├── components/ # React components
│       │   ├── rest/      # API client functions
│       │   ├── state/     # Jotai state atoms
│       │   └── hooks/     # Custom React hooks
│       └── package.json
├── libs/                    # Shared Libraries
│   ├── account/           # Account Management Module
│   ├── app/               # App Management Module
│   ├── common/            # Common Utilities
│   └── types/             # Shared Type Definitions
├── locales/                # Internationalization Files
│   ├── en.json
│   └── zh-CN.json
├── scripts/                # Build and Utility Scripts
│   └── sync-locales-cli.ts
└── package.json            # Root package.json
```

## Common Development Tasks

### Adding a New API Endpoint (Server)

1. Create controller in `apps/server/src/controller/`:

```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('users')
export class UserController {
  @Get()
  findAll() {
    return { message: 'Get all users' };
  }
}
```

2. Register controller in `app.module.ts`

3. Access via: http://localhost:3100/users

### Adding a New Page (Web)

1. Create page in `apps/web/src/app/`:

```typescript
// apps/web/src/app/users/page.tsx
export default function UsersPage() {
  return <div>Users Page</div>;
}
```

2. Access via: http://localhost:4002/users

### Adding a New Library Module

1. Create library structure:

```bash
libs/
└── new-module/
    ├── src/
    │   ├── index.ts
    │   └── new-module.service.ts
    └── tsconfig.lib.json
```

2. Export from library `index.ts`
3. Import in consuming app: `import { NewModuleService } from '@meta-1/authub-new-module'`

### Working with Shared Types

Types are defined in `libs/types/src/`:

- `account/` - Account-related types and schemas
- `app/` - App-related types
- `common.types.ts` - Common types

Import in both web and server:

```typescript
import type { Account } from '@meta-1/authub-types/account';
```

## Testing

### Unit Tests

```bash
# Run all tests
pnpm run test

# Run tests in watch mode
pnpm run test:watch

# Run tests with coverage
pnpm run test:cov
```

### E2E Tests

```bash
pnpm run test:e2e
```

### Test Structure

- Unit tests: `*.spec.ts` or `*.test.ts`
- E2E tests: `apps/support/test/`

## Code Quality

### Linting

```bash
# Check code quality
pnpm run lint

# Auto-fix linting issues
pnpm run format
```

This project uses **Biome** for fast code checking and formatting.

### Code Style

- Use TypeScript strict mode
- Follow NestJS conventions for backend
- Follow Next.js conventions for frontend
- Use ESLint/Biome rules

## Internationalization

### Adding Translations

1. Edit locale files in `locales/`:
   - `en.json` - English
   - `zh-CN.json` - Chinese

2. Sync locales:

```bash
pnpm run sync:locales
```

### Using Translations

**Server (NestJS):**

```typescript
import { I18n, I18nContext } from '@meta-1/nest-common';

@Controller('users')
export class UserController {
  @Get()
  async findAll(@I18n() i18n: I18nContext) {
    return {
      message: i18n.t('users.list.success'),
      data: []
    };
  }
}
```

**Web (Next.js):**

```typescript
import { useTranslation } from 'react-i18next';

export function UserComponent() {
  const { t } = useTranslation();
  return <div>{t('users.title')}</div>;
}
```

## Debugging

### Server Debugging

```bash
# Debug server with Node.js inspector
NODE_OPTIONS='--inspect' pnpm run dev:server
```

Then attach debugger in VS Code or Chrome DevTools.

### Web Debugging

Use React DevTools and Next.js DevTools browser extensions.

### Debug Tests

```bash
pnpm run test:debug
```

## Troubleshooting

### Redis Connection Failed

```bash
# Check Redis is running
redis-cli ping

# Should return: PONG
```

### Nacos Connection Failed

1. Verify Nacos is running: http://localhost:8848/nacos
2. Check `NACOS_SERVER` environment variable
3. Service will start in degraded mode if Nacos unavailable

### Database Connection Failed

1. Verify MySQL is running
2. Check Nacos configuration for database settings
3. Verify database credentials

### Port Already in Use

```bash
# Find process using port
lsof -i :3100  # Server port
lsof -i :4002  # Web port

# Kill process
kill -9 <PID>
```

### Dependency Issues

```bash
# Clean install
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

## Next Steps

- Read [Architecture Documentation](./architecture-server.md) for server architecture
- Read [Architecture Documentation](./architecture-web.md) for web architecture
- Check [API Contracts](./api-contracts-server.md) for API endpoints
- Review [Data Models](./data-models-server.md) for database schema

