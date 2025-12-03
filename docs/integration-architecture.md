# Integration Architecture

## Overview

This document describes how the different parts of the Authub monorepo communicate and integrate with each other. The project consists of three main parts: **Web Application**, **Server Application**, and **Shared Libraries**.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Web Application                       │
│                      (apps/web - Next.js)                    │
│                                                               │
│  ┌──────────────┐      ┌──────────────┐                    │
│  │   Pages      │──────│  Components  │                    │
│  └──────────────┘      └──────────────┘                    │
│         │                      │                            │
│         └──────────┬───────────┘                            │
│                    │                                         │
│              ┌─────▼─────┐                                   │
│              │ REST API  │                                   │
│              │  Client   │                                   │
│              └─────┬─────┘                                   │
│                    │                                         │
└────────────────────┼─────────────────────────────────────────┘
                     │ REST API (HTTP/HTTPS)
                     │ JWT Authentication
                     │ JSON Data Format
┌────────────────────┼─────────────────────────────────────────┐
│                    │      Server Application                 │
│                    │   (apps/server - NestJS)               │
│                    │                                         │
│              ┌─────▼─────┐                                   │
│              │ Controllers│                                   │
│              └─────┬─────┘                                   │
│                    │                                         │
│         ┌──────────┼──────────┐                             │
│         │          │          │                             │
│    ┌────▼────┐ ┌───▼────┐ ┌───▼────┐                       │
│    │ Account │ │  App   │ │ Common │                       │
│    │ Module  │ │ Module │ │ Module │                       │
│    └────┬────┘ └───┬────┘ └───┬────┘                       │
│         │          │          │                             │
│         └──────────┼──────────┘                             │
│                    │                                         │
│              ┌─────▼─────┐                                   │
│              │  Services │                                   │
│              └─────┬─────┘                                   │
│                    │                                         │
└────────────────────┼─────────────────────────────────────────┘
                     │
         ┌───────────┼───────────┐
         │           │           │
    ┌────▼────┐ ┌───▼────┐ ┌───▼────┐
    │  MySQL  │ │ Redis  │ │ Nacos  │
    │Database │ │ Cache  │ │ Config │
    └─────────┘ └────────┘ └────────┘

┌─────────────────────────────────────────────────────────────┐
│                    Shared Libraries                          │
│                         (libs/)                               │
│                                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   account/   │  │     app/     │  │   common/    │      │
│  │  (Services,  │  │  (Services, │  │  (Utilities) │      │
│  │  Entities,   │  │  Entities)  │  │              │      │
│  │ Controllers) │  │             │  │              │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              types/                                   │  │
│  │  (Shared TypeScript types & Zod schemas)              │  │
│  │  Used by both Web and Server                          │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Integration Points

### 1. Web → Server: REST API Communication

**Protocol:** HTTP/HTTPS REST API  
**Client Library:** Axios  
**Base URL:** Configurable via `NEXT_PUBLIC_API_URL` environment variable

#### Authentication Flow

1. User logs in via `POST /api/account/login`
2. Server returns JWT token
3. Token stored in cookies (httpOnly, secure)
4. Subsequent requests include token in Authorization header or cookies
5. Server validates token using `AuthGuard` from `@meta-1/authub-account`

#### API Endpoints

**Account Management:**
- `POST /api/account/login` - User login
- `POST /api/account/register` - User registration
- `POST /api/account/logout` - User logout
- `GET /api/account/profile` - Get user profile

**App Management:**
- `GET /api/apps` - List user applications
- `POST /api/apps` - Create application
- `PUT /api/apps/:id` - Update application
- `DELETE /api/apps/:id` - Delete application

**Assets:**
- `POST /api/assets/upload` - Upload file/avatar
- `GET /api/assets/:id` - Get asset

**Configuration:**
- `GET /api/config` - Get server configuration

**Mail Code:**
- `POST /api/mail-code/send` - Send verification code

#### Request/Response Format

**Request:**
```typescript
// Example: Login request
POST /api/account/login
Content-Type: application/json

{
  "username": "user@example.com",
  "password": "encrypted_password" // RSA encrypted
}
```

**Response:**
```typescript
// Standard response format
{
  "code": 0,
  "success": true,
  "message": "Success",
  "data": {
    "token": "jwt_token_here",
    "expiresIn": 604800
  },
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

#### Error Handling

Errors follow the same response format:
```typescript
{
  "code": 400,
  "success": false,
  "message": "Error message",
  "data": null,
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

### 2. Server → Libraries: Module Integration

**Pattern:** NestJS Dependency Injection  
**Modules Used:**

#### Account Module (`@meta-1/authub-account`)

**Exports:**
- `AccountModule` - Main module
- `AuthGuard` - Authentication guard (used globally)
- Services: `AccountService`, `AccountOtpService`, `AccountConfigService`
- Controllers: `AccountController`, `AccountOtpController`
- Entities: `AccountEntity`, `AccountTokenEntity`

**Usage:**
```typescript
// apps/server/src/app.module.ts
import { AccountModule, AuthGuard } from "@meta-1/authub-account";

@Module({
  imports: [AccountModule],
  providers: [
    {
      provide: APP_GUARD,
      useClass: AuthGuard,
    },
  ],
})
export class AppModule {}
```

#### App Module (`@meta-1/authub-app`)

**Exports:**
- `AppModule` - Application management module
- `AppService` - Business logic
- `AppController` - API endpoints
- Entities: `AppEntity`, `AppAccountEntity`

#### Common Module (`@meta-1/authub-common`)

**Exports:**
- `CommonModule` - Shared utilities
- `ServerConfig` - Configuration types
- Error codes and constants

### 3. Shared Types: Type Safety Across Parts

**Library:** `@meta-1/authub-types`  
**Purpose:** Shared TypeScript types and Zod schemas used by both Web and Server

#### Type Categories

**Account Types:**
- `LoginData`, `RegisterData`, `Profile`, `Token`
- `AccountOtp` related types
- Zod schemas: `LoginSchema`, `RegisterSchema`

**App Types:**
- `AppListItem`, `App` types
- `AppSchema` for validation

**Common Types:**
- `CommonConfig` - Shared configuration types
- Utility types and interfaces

#### Usage Examples

**Web:**
```typescript
// apps/web/src/rest/account.ts
import type { LoginData, Profile, Token } from "@meta-1/authub-types";
import { RegisterData } from "@meta-1/authub-types";

export const login = (data: LoginData) => 
  post<Token, LoginData>("@api/account/login", data);
```

**Server:**
```typescript
// apps/server/src/dto/config.dto.ts
import { CommonConfigSchema } from "@meta-1/authub-types";
```

### 4. Server → Database: TypeORM Integration

**ORM:** TypeORM  
**Database:** MySQL  
**Pattern:** Entity-based data access

#### Entity Location

Entities are defined in library modules:
- `libs/account/src/entity/` - Account entities
- `libs/app/src/entity/` - App entities

#### Database Configuration

Configured via Nacos:
```yaml
database:
  host: localhost
  port: 3306
  username: root
  password: your-password
  database: authub
  synchronize: false
  logging: false
```

#### Entity Usage

```typescript
// Server automatically loads entities via autoLoadEntities: true
// Entities are imported from libraries:
import { AccountEntity } from "@meta-1/authub-account";
```

### 5. Server → Cache: Redis Integration

**Client:** ioredis  
**Purpose:** Session storage, caching, token management

#### Configuration

Via Nacos:
```yaml
redis:
  host: localhost
  port: 6379
  password: ""
  db: 0
```

#### Usage

- Session storage for user tokens
- Cache for frequently accessed data
- OTP code storage (time-limited)

### 6. Server → External Services

#### Nacos Configuration Center

**Purpose:** Centralized configuration management  
**Integration:** `@meta-1/nest-nacos`

**Configuration Flow:**
1. Server reads `NACOS_SERVER` and `APP_NAME` from environment
2. Connects to Nacos and loads configuration with Data ID = `APP_NAME`
3. Parses YAML configuration into `ServerConfig` type
4. Uses configuration for database, Redis, and account settings
5. Falls back to degraded mode if Nacos unavailable

#### Email Services

**Providers:**
- AWS SES (via `@aws-sdk/client-sesv2`)
- Aliyun Email Push (via `@alicloud/dm20151123`)

**Usage:** Managed by `@meta-1/nest-message` module

#### File Storage

**Providers:**
- AWS S3
- Aliyun OSS

**Usage:** Managed by `@meta-1/nest-assets` module

## Data Flow Examples

### User Login Flow

```
1. User submits login form (Web)
   ↓
2. Password encrypted with RSA public key (Web)
   ↓
3. POST /api/account/login with encrypted password (Web → Server)
   ↓
4. AccountController receives request (Server)
   ↓
5. AccountService validates credentials (Server → Account Module)
   ↓
6. Query AccountEntity from database (Server → MySQL via TypeORM)
   ↓
7. Generate JWT token (Server)
   ↓
8. Store session in Redis (Server → Redis)
   ↓
9. Return token to client (Server → Web)
   ↓
10. Store token in cookies (Web)
```

### Profile Retrieval Flow

```
1. User navigates to profile page (Web)
   ↓
2. GET /api/account/profile request (Web → Server)
   ↓
3. AuthGuard validates JWT token (Server → Account Module)
   ↓
4. Extract user ID from token (Server)
   ↓
5. Check Redis cache for profile (Server → Redis)
   ↓
6. If not cached, query AccountEntity (Server → MySQL)
   ↓
7. Return profile data (Server → Web)
   ↓
8. Update React state with profile (Web)
```

## Integration Summary

| Integration Point | From | To | Protocol/Pattern | Purpose |
|------------------|------|-----|------------------|---------|
| REST API | Web | Server | HTTP/HTTPS, JSON | User interactions, data fetching |
| Module Import | Server | Libraries | NestJS DI | Business logic, entities, services |
| Type Sharing | Web/Server | Types Library | TypeScript imports | Type safety, validation schemas |
| Database Access | Server | MySQL | TypeORM | Data persistence |
| Cache Access | Server | Redis | ioredis | Session, caching |
| Configuration | Server | Nacos | HTTP API | Centralized config management |
| Email Service | Server | AWS/Aliyun | SDK | Email notifications |
| File Storage | Server | S3/OSS | SDK | Asset storage |

## Security Considerations

### Authentication
- JWT tokens with expiration
- RSA encryption for password transmission
- HttpOnly cookies for token storage
- Token validation on every protected endpoint

### Authorization
- Role-based access control (RBAC) via `AuthGuard`
- Permission checks at service level
- API endpoint protection

### Data Protection
- HTTPS for all API communication
- Encrypted password storage
- Secure session management
- XSS and CSRF protection

## Monitoring and Debugging

### API Monitoring
- Swagger documentation: http://localhost:3100/api
- Request/response logging in development mode
- Error tracking via global exception filter

### Database Monitoring
- TypeORM query logging (when enabled)
- Connection pool monitoring

### Cache Monitoring
- Redis connection status
- Cache hit/miss metrics

## Future Integration Points

Potential areas for expansion:
- WebSocket support for real-time features
- Message queue (RabbitMQ/Kafka) for async processing
- GraphQL API as alternative to REST
- Microservices architecture migration
- Service mesh integration (Istio/Linkerd)

