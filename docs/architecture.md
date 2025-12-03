---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments:
  - docs/index.md
  - docs/project-overview.md
  - docs/architecture-server.md
  - docs/architecture-web.md
  - docs/integration-architecture.md
workflowType: 'architecture'
lastStep: 8
status: 'complete'
completedAt: '2025-12-03T03:00:01.000Z'
project_name: 'Authub'
user_name: 'Grant'
date: '2025-12-03T02:54:19.000Z'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together.

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**

从现有架构文档推断，Authub 的核心功能包括：

1. **用户认证与授权**
   - 用户注册、登录、登出
   - JWT Token 管理（多应用 Token 支持）
   - Token 自动刷新机制
   - 双因素认证（OTP）

2. **账号管理**
   - 用户资料管理（邮箱、用户名、头像）
   - 账号状态管理（启用/禁用、软删除）
   - 会话管理
   - 多应用账号关联

3. **应用管理**
   - 多应用支持（一个账号可关联多个应用）
   - 应用 CRUD 操作
   - 应用配置管理（回调地址、首页地址）
   - 应用内角色管理（管理员/成员）

4. **资源管理**
   - 文件上传（预签名 URL）
   - 头像管理
   - 支持 S3/OSS 存储

5. **辅助功能**
   - 邮件验证码发送
   - 国际化支持（中文/英文）
   - 配置中心化管理

**Non-Functional Requirements:**

从架构文档中识别到的关键 NFR：

1. **安全性**
   - RSA 密码加密传输
   - JWT Token 认证（无状态）
   - 双因素认证（OTP）
   - 基于角色的访问控制（RBAC）
   - XSS 和 CSRF 防护
   - HttpOnly Cookie 存储 Token
   - 密码加密存储

2. **可扩展性**
   - Monorepo 架构支持模块化扩展
   - 模块化服务架构（NestJS）
   - 共享类型库确保类型一致性
   - 配置中心化管理（Nacos）

3. **性能**
   - Redis 缓存（会话、Token、频繁访问数据）
   - 数据库查询优化（TypeORM）
   - 连接池管理
   - Next.js 自动代码分割

4. **可用性**
   - Nacos 降级支持（配置中心不可用时仍可启动）
   - 错误处理和全局异常过滤
   - 健康检查端点

5. **开发体验**
   - 完整的 TypeScript 支持
   - 前后端共享类型定义
   - Swagger API 文档自动生成
   - Biome 代码检查和格式化

**Scale & Complexity:**

- Primary domain: 全栈 Web 应用（Next.js + NestJS）
- Complexity level: 中等偏高（企业级）
- Estimated architectural components: 
  - 前端：App Router 架构，组件化设计
  - 后端：模块化服务架构，4+ 核心模块
  - 共享库：4 个共享库（account, app, common, types）
  - 外部集成：MySQL, Redis, Nacos, S3/OSS, AWS SES/Aliyun

### Technical Constraints & Dependencies

**技术栈约束：**
- Node.js >= 18
- TypeScript 5.9.3
- Next.js 16.0.4（App Router）
- NestJS 11.1.9
- MySQL >= 8.0
- Redis >= 6.0
- Nacos >= 2.0（可选，支持降级）

**架构约束：**
- Monorepo 结构（必须保持）
- 前后端类型共享（通过 `@meta-1/authub-types`）
- RESTful API 设计（当前架构）
- 模块化设计（NestJS Module 系统）

**外部依赖：**
- 数据库：MySQL（数据持久化）
- 缓存：Redis（会话和缓存）
- 配置中心：Nacos（配置管理，可选）
- 存储：S3/OSS（文件存储）
- 邮件：AWS SES / Aliyun（邮件服务）

### Cross-Cutting Concerns Identified

1. **认证与授权**
   - 影响所有受保护端点
   - 需要统一的 AuthGuard 机制
   - Token 管理策略（多应用场景）

2. **错误处理**
   - 统一的错误响应格式
   - 全局异常过滤器
   - 前后端错误处理一致性

3. **国际化**
   - 前后端都需要支持
   - 语言文件管理
   - API 响应国际化

4. **类型安全**
   - 前后端类型共享
   - Zod Schema 验证
   - TypeScript 类型一致性

5. **配置管理**
   - 集中式配置（Nacos）
   - 环境隔离
   - 配置热更新
   - 降级策略

6. **状态管理**
   - 前端：Jotai（客户端状态）+ TanStack Query（服务端状态）
   - 后端：Redis（会话状态）+ MySQL（持久化状态）

## Starter Template Evaluation

### Primary Technology Domain

全栈 Web 应用（Next.js + NestJS）基于项目需求分析确定。

### Existing Architecture Foundation

由于这是一个已存在的项目，基础架构已经建立。项目采用了以下基础架构决策：

**Monorepo 结构：**
- 使用 pnpm workspace 管理多包项目
- 包含 apps（web, server）和 libs（account, app, common, types）

**前端基础架构（Next.js）：**
- Next.js 16.0.4（App Router）
- React 19.2.0
- TypeScript 5.9.3
- Tailwind CSS 3.4.0
- 组件化架构

**后端基础架构（NestJS）：**
- NestJS 11.1.9
- TypeScript 5.9.3
- TypeORM 0.3.27
- 模块化服务架构

**共享库架构：**
- 类型共享（@meta-1/authub-types）
- 模块化设计（account, app, common）

### Architectural Decisions Established

**语言与运行时：**
- TypeScript 5.9.3（前后端统一）
- Node.js >= 18

**样式方案：**
- Tailwind CSS 3.4.0（原子化 CSS）

**构建工具：**
- Next.js 内置构建系统
- NestJS CLI
- pnpm workspace

**测试框架：**
- 项目已配置测试基础设施

**代码组织：**
- Monorepo 结构（apps + libs）
- 模块化设计
- 共享类型库

**开发体验：**
- TypeScript 类型安全
- Biome 代码检查和格式化
- Swagger API 文档自动生成

**注意：** 项目初始化已完成，这些基础架构决策已经建立，无需重新初始化。

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- 数据库架构（MySQL + TypeORM）
- 认证架构（JWT + AuthGuard）
- API 架构（RESTful + Swagger）
- 前端架构（Next.js + 状态管理）
- 基础设施（配置管理、文件存储、邮件服务）

**Important Decisions (Shape Architecture):**
- 类型共享策略（@meta-1/authub-types）
- 错误处理标准（统一响应格式）
- 国际化策略（前后端共享）
- 缓存策略（Redis）

**Deferred Decisions (Post-MVP):**
- 暂无（项目已建立完整架构）

### Data Architecture

**Database Choice:**
- **Decision:** MySQL >= 8.0
- **Version:** 8.0+
- **Rationale:** 关系型数据模型，支持事务，与 TypeORM 集成良好
- **Affects:** 所有数据持久化功能

**ORM Choice:**
- **Decision:** TypeORM 0.3.27
- **Version:** 0.3.27
- **Rationale:** NestJS 集成良好，支持 TypeScript，提供迁移工具
- **Affects:** 所有数据库操作

**Data Validation Strategy:**
- **Decision:** Zod Schema 验证（通过 nestjs-zod）
- **Version:** Zod 3.25.76, nestjs-zod 5.0.1
- **Rationale:** 前后端类型共享，运行时验证，TypeScript 类型推导
- **Affects:** API 端点、表单验证

**Caching Strategy:**
- **Decision:** Redis（ioredis 5.8.2）
- **Version:** Redis >= 6.0, ioredis 5.8.2
- **Rationale:** 会话存储、Token 缓存、频繁访问数据缓存
- **Affects:** 认证、会话管理、性能优化

### Authentication & Security

**Authentication Method:**
- **Decision:** JWT Token（Bearer Token）
- **Version:** 通过 @meta-1/nest-security 0.0.8
- **Rationale:** 无状态认证，支持多应用 Token，自动刷新机制
- **Affects:** 所有受保护端点

**Password Encryption:**
- **Decision:** RSA 加密传输 + 密码加密存储
- **Rationale:** 传输层安全，存储层安全
- **Affects:** 登录、注册、密码重置

**Two-Factor Authentication:**
- **Decision:** OTP（基于 TOTP）
- **Rationale:** 增强安全性
- **Affects:** 账号安全设置

**Authorization Pattern:**
- **Decision:** 基于角色的访问控制（RBAC）
- **Rationale:** 支持多应用场景，灵活权限管理
- **Affects:** 应用管理、权限控制

**API Security Strategy:**
- **Decision:** AuthGuard 全局守卫 + HttpOnly Cookie Token 存储
- **Rationale:** 统一认证检查，防止 XSS 攻击
- **Affects:** 所有 API 端点

### API & Communication Patterns

**API Design Pattern:**
- **Decision:** RESTful API
- **Rationale:** 简单、标准、易于理解
- **Affects:** 前后端通信

**API Documentation Approach:**
- **Decision:** Swagger 11.2.1（自动生成）
- **Version:** Swagger 11.2.1
- **Rationale:** 自动生成，交互式文档
- **Affects:** API 开发、文档维护

**Error Handling Standards:**
- **Decision:** 统一响应格式 + 全局异常过滤器
- **Rationale:** 一致的错误处理，便于前端处理
- **Affects:** 所有 API 端点

**Communication Protocol:**
- **Decision:** HTTP/HTTPS + JSON
- **Rationale:** 标准协议，易于集成
- **Affects:** 前后端通信

### Frontend Architecture

**State Management Approach:**
- **Decision:** Jotai（客户端状态）+ TanStack Query（服务端状态）
- **Version:** Jotai 2.10.2, TanStack Query 5.80.3
- **Rationale:** 轻量级原子状态管理，强大的数据获取和缓存
- **Affects:** 所有前端组件

**Component Architecture:**
- **Decision:** 组件化设计（Layout + Common + Feature）
- **Rationale:** 可复用、可维护、清晰分层
- **Affects:** 所有 UI 组件

**Routing Strategy:**
- **Decision:** Next.js App Router（文件系统路由）
- **Rationale:** 内置路由，支持 SSR/SSG
- **Affects:** 所有页面

**Performance Optimization:**
- **Decision:** Next.js 自动代码分割 + TanStack Query 缓存
- **Rationale:** 减少初始加载时间，优化数据获取
- **Affects:** 所有页面和组件

**Internationalization:**
- **Decision:** i18next（前后端共享语言文件）
- **Version:** i18next 23.11.5, react-i18next 15.5.1
- **Rationale:** 前后端统一国际化，支持中文/英文
- **Affects:** 所有用户界面

### Infrastructure & Deployment

**Configuration Management:**
- **Decision:** Nacos 配置中心（支持降级）
- **Version:** Nacos >= 2.0, @meta-1/nest-nacos 0.0.7
- **Rationale:** 集中式配置管理，环境隔离，配置热更新
- **Affects:** 所有配置项

**File Storage:**
- **Decision:** S3/OSS（通过预签名 URL）
- **Rationale:** 可扩展、安全、支持大文件
- **Affects:** 文件上传、头像管理

**Email Service:**
- **Decision:** AWS SES / Aliyun（通过 @meta-1/nest-message）
- **Version:** @meta-1/nest-message 0.0.9
- **Rationale:** 可靠、可扩展
- **Affects:** 邮件验证码发送

**Monitoring & Logging:**
- **Decision:** NestJS 内置日志系统 + Swagger API 文档
- **Rationale:** 开发模式详细日志，生产模式错误日志
- **Affects:** 所有服务

### Decision Impact Analysis

**Implementation Sequence:**
1. 数据库架构（MySQL + TypeORM）
2. 认证架构（JWT + AuthGuard）
3. API 架构（RESTful + Swagger）
4. 前端架构（Next.js + 状态管理）
5. 基础设施（配置管理、文件存储、邮件服务）

**Cross-Component Dependencies:**
- 认证架构影响所有受保护端点
- 数据架构影响所有业务逻辑
- 类型共享确保前后端一致性
- 配置管理影响所有服务启动

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:**
6 个主要领域已识别，AI 代理可能做出不同选择，需要统一模式。

### Naming Patterns

**Database Naming Conventions:**

- **Table Names:** snake_case（小写，下划线分隔）
  - 示例：`account`, `account_token`, `app_account`
- **Column Names:** snake_case（小写，下划线分隔）
  - 示例：`user_id`, `create_time`, `last_time`
- **Foreign Keys:** `{referenced_table}_id` 格式
  - 示例：`account_id`, `app_id`
- **Indexes:** 由 TypeORM 自动管理，遵循表名列名约定
- **Naming Strategy:** TypeORM SnakeNamingStrategy（自动转换）

**API Naming Conventions:**

- **REST Endpoints:** `/api/{module}` 或 `/api/{module}/{resource}`（kebab-case，复数形式）
  - 示例：`/api/account`, `/api/apps`, `/api/mail-code`
- **Route Parameters:** `:id` 或 `{id}`（小写）
  - 示例：`/api/apps/:id`, `/api/apps/{id}`
- **Query Parameters:** camelCase
  - 示例：`userId`, `pageSize`, `pageNum`
- **Request Headers:** 标准 HTTP 头命名（如 `Authorization`, `Content-Type`）

**Code Naming Conventions:**

- **Component Names:** PascalCase
  - 示例：`UserCard`, `AppList`, `EmailCodeInput`
- **File Names:**
  - 组件文件：`{ComponentName}.tsx`（PascalCase）
  - Hook 文件：`use{Feature}.ts`（camelCase，以 `use` 开头）
  - API 客户端文件：`{module}.ts`（kebab-case）
  - 状态文件：`{feature}.ts`（kebab-case）
  - 工具文件：`{feature}.ts`（kebab-case）
- **Function Names:** camelCase
  - 示例：`getUserData()`, `handleSubmit()`, `validateEmail()`
- **Variable Names:** camelCase
  - 示例：`userId`, `isLoading`, `userData`
- **Constant Names:** UPPER_SNAKE_CASE
  - 示例：`MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT`
- **Type/Interface Names:** PascalCase
  - 示例：`UserData`, `AppResponse`, `RestResult`

### Structure Patterns

**Project Organization:**

- **Backend Structure (NestJS):**
  ```
  apps/server/src/
  ├── controller/     # REST API 控制器
  ├── dto/           # 数据传输对象
  ├── app.module.ts  # 根模块
  └── main.ts        # 应用入口
  ```
- **Frontend Structure (Next.js):**
  ```
  apps/web/src/
  ├── app/           # Next.js App Router（页面和路由）
  ├── components/    # React 组件
  │   ├── common/    # 通用组件
  │   └── layout/    # 布局组件
  ├── rest/          # API 客户端
  ├── state/         # Jotai 状态
  ├── hooks/         # 自定义 Hooks
  ├── utils/         # 工具函数
  └── types/         # 类型定义
  ```
- **Shared Library Structure:**
  ```
  libs/{module}/src/
  ├── controller/    # 控制器
  ├── service/       # 服务
  ├── dto/          # DTO
  ├── entity/       # 实体
  └── shared/       # 共享代码
  ```

**File Structure Patterns:**

- **Test Files:** 与源文件同目录，命名 `*.spec.ts` 或 `*.test.ts`
- **Config Files:** 根目录或 `config/` 目录
- **Static Assets:** `public/` 或 `assets/` 目录
- **Documentation:** `docs/` 目录
- **Environment Files:** `.env`, `.env.local`, `.env.production`

### Format Patterns

**API Response Formats:**

所有 API 响应遵循统一格式：

```typescript
interface RestResult<T> {
  code: number;        // 状态码 (0=成功)
  success: boolean;    // 是否成功
  message: string;     // 消息
  data: T;            // 数据
  timestamp: string;   // ISO 8601 时间戳
  path?: string;       // 请求路径（可选）
}
```

**Success Response Example:**
```typescript
{
  code: 0,
  success: true,
  message: "success",
  data: { id: "123", name: "App Name" },
  timestamp: "2025-12-03T02:54:19.000Z"
}
```

**Error Response Example:**
```typescript
{
  code: 400,
  success: false,
  message: "错误消息",
  data: null,
  timestamp: "2025-12-03T02:54:19.000Z"
}
```

**Data Exchange Formats:**

- **JSON Field Naming:** camelCase（前后端统一）
  - 示例：`userId`, `createTime`, `appKey`
- **Date Format:** ISO 8601 字符串（`YYYY-MM-DDTHH:mm:ss.sssZ`）
  - 示例：`"2025-12-03T02:54:19.000Z"`
- **Boolean Values:** `true`/`false`（不使用 `1`/`0`）
- **Null Handling:** 使用 `null`（不使用 `undefined`）
- **Array vs Object:** 单个项目返回对象，多个项目返回数组

### Communication Patterns

**Event System Patterns:**

- **Event Naming:** kebab-case，点分隔
  - 示例：`user.created`, `app.updated`, `account.deleted`
- **Event Payload Structure:** 与 API 响应格式一致
- **Event Versioning:** 通过事件名称或负载中的 `version` 字段管理

**State Management Patterns:**

- **State Updates:** 不可变更新（使用 Jotai 原子更新）
- **Action Naming:** camelCase，动词开头
  - 示例：`setUserData`, `updateApp`, `clearCache`
- **Selector Patterns:** 使用 Jotai `atom` 和 `useAtom` Hook
- **State Organization:** 按功能模块组织（`state/access.ts`, `state/app.ts`, `state/profile.ts`）

### Process Patterns

**Error Handling Patterns:**

- **Global Error Handling:** 使用 NestJS `ErrorsFilter` 全局异常过滤器
- **Error Boundaries:** React Error Boundary 处理前端错误
- **User Error Messages:** 通过 API 响应的 `message` 字段显示
- **Logging vs User Errors:** 开发环境记录详细日志，生产环境仅显示用户友好消息

**Loading State Patterns:**

- **Loading State Naming:** `isLoading`, `isFetching`, `isSubmitting`
- **Global vs Local:** 使用 TanStack Query 的 `isLoading` 状态（全局），组件内使用本地状态（如 `isSubmitting`）
- **Loading State Persistence:** TanStack Query 自动管理缓存和加载状态
- **Loading UI Patterns:** 使用 Suspense 边界和加载指示器组件

### Enforcement Guidelines

**All AI Agents MUST:**

- 遵循数据库命名约定（snake_case）
- 使用统一的 API 响应格式（RestResult<T>）
- 遵循代码命名约定（camelCase 变量/函数，PascalCase 组件/类型）
- 使用 kebab-case 命名 API 端点和文件目录
- 遵循项目结构组织模式
- 使用 ISO 8601 格式的日期时间
- 使用 camelCase 命名 JSON 字段
- 实现不可变状态更新

**Pattern Enforcement:**

- **Verification:** Biome 代码检查和格式化工具
- **Documentation Location:** 本架构文档和 `.cursor/rules/` 目录
- **Update Process:** 通过架构文档更新，通知所有 AI 代理

### Pattern Examples

**Good Examples:**

```typescript
// ✅ 正确的组件命名
export function UserCard({ userId }: { userId: string }) {
  // ✅ 正确的变量命名
  const [isLoading, setIsLoading] = useState(false);
  
  // ✅ 正确的 API 调用
  const { data } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => get<UserResponse>(`@api/account/profile`)
  });
  
  return <div>{data?.data?.username}</div>;
}

// ✅ 正确的 API 端点命名
POST /api/account/login
GET /api/apps/:id

// ✅ 正确的数据库表名
account
account_token
app_account
```

**Anti-Patterns:**

```typescript
// ❌ 错误的组件命名（应使用 PascalCase）
export function userCard() { }

// ❌ 错误的 API 端点命名（应使用 kebab-case）
POST /api/Account/Login  // ❌
GET /api/apps/{Id}        // ❌

// ❌ 错误的数据库表名（应使用 snake_case）
AccountToken  // ❌
AppAccount   // ❌

// ❌ 错误的 JSON 字段命名（应使用 camelCase）
{
  "user_id": "123",      // ❌
  "create_time": "..."   // ❌
}
```

## Project Structure & Boundaries

### Complete Project Directory Structure

```
authub/
├── README.md                          # 项目根 README
├── package.json                       # 根 package.json（Monorepo 配置）
├── pnpm-workspace.yaml               # pnpm workspace 配置
├── pnpm-lock.yaml                    # pnpm 锁定文件
├── tsconfig.json                     # TypeScript 根配置
├── tsconfig.build.json               # TypeScript 构建配置
├── tsconfig.web.json                 # TypeScript Web 配置
├── nest-cli.json                    # NestJS CLI 配置
├── webpack.config.js                 # Webpack 配置
├── biome.json                        # Biome 代码检查和格式化配置
│
├── apps/                             # 应用程序目录
│   ├── server/                       # NestJS 后端服务
│   │   ├── README.md
│   │   ├── tsconfig.app.json        # Server TypeScript 配置
│   │   └── src/
│   │       ├── main.ts              # 应用入口
│   │       ├── app.module.ts        # 根模块
│   │       ├── app.swagger.ts       # Swagger 配置
│   │       ├── controller/          # REST API 控制器
│   │       │   ├── app.controller.ts
│   │       │   ├── assets.controller.ts
│   │       │   ├── config.controller.ts
│   │       │   ├── mail-code.controller.ts
│   │       │   └── index.ts
│   │       └── dto/                 # 数据传输对象
│   │           ├── config.dto.ts
│   │           └── index.ts
│   │
│   └── web/                          # Next.js 前端应用
│       ├── README.md
│       ├── package.json
│       ├── next.config.ts           # Next.js 配置
│       ├── next-env.d.ts            # Next.js 类型定义
│       ├── tsconfig.json            # Web TypeScript 配置
│       ├── public/                  # 静态资源
│       │   ├── favicon.ico
│       │   ├── android-chrome-*.png
│       │   ├── apple-touch-icon.png
│       │   ├── site.webmanifest
│       │   └── assets/
│       │       └── image/
│       │           ├── 2fa/         # 2FA 相关图片
│       │           └── avatar.jpeg
│       │
│       └── src/
│           ├── app/                 # Next.js App Router
│           │   ├── layout.tsx      # 根布局
│           │   ├── (login)/        # 登录路由组
│           │   │   ├── layout.tsx
│           │   │   ├── login/
│           │   │   │   ├── page.tsx
│           │   │   │   └── components/
│           │   │   └── register/
│           │   │       ├── page.tsx
│           │   │       └── components/
│           │   └── (main)/         # 主应用路由组
│           │       ├── layout.tsx
│           │       ├── page.tsx    # 首页
│           │       ├── apps/        # 应用管理
│           │       │   ├── page.tsx
│           │       │   ├── components/
│           │       │   └── [id]/    # 应用详情
│           │       ├── profile/     # 用户资料
│           │       │   ├── page.tsx
│           │       │   ├── components/
│           │       │   └── security/ # 安全设置
│           │       └── users/       # 用户管理
│           │           ├── page.tsx
│           │           └── components/
│           │
│           ├── components/          # React 组件
│           │   ├── background/     # 背景组件
│           │   ├── common/         # 通用组件
│           │   │   ├── access/      # 权限控制
│           │   │   ├── account/    # 账号相关
│           │   │   ├── breadcrumb/ # 面包屑
│           │   │   ├── input/      # 输入组件
│           │   │   ├── page/       # 页面组件
│           │   │   └── ...
│           │   └── layout/         # 布局组件
│           │       ├── root/       # 根布局
│           │       ├── main/       # 主布局
│           │       ├── login/      # 登录布局
│           │       └── app/        # 应用布局
│           │
│           ├── rest/               # API 客户端
│           │   ├── account.ts     # 账号 API
│           │   ├── app/           # 应用 API
│           │   ├── assets.ts      # 资源 API
│           │   ├── profile/       # 资料 API
│           │   └── public.ts     # 公开 API
│           │
│           ├── state/              # Jotai 状态
│           │   ├── access.ts      # 权限状态
│           │   ├── app.ts         # 应用状态
│           │   ├── config.ts      # 配置状态
│           │   ├── layout.ts     # 布局状态
│           │   ├── profile.ts    # 用户资料状态
│           │   └── public.ts     # 公开状态
│           │
│           ├── hooks/             # 自定义 Hooks
│           │   ├── use.access.ts
│           │   ├── use.profile.ts
│           │   ├── use.query.ts
│           │   ├── use.mutation.ts
│           │   └── ...
│           │
│           ├── utils/            # 工具函数
│           │   ├── rest.ts      # HTTP 客户端
│           │   ├── token.ts     # Token 管理
│           │   ├── crypto.ts    # 加密工具
│           │   ├── i18next.ts   # 国际化工具
│           │   └── ...
│           │
│           ├── config/           # 配置
│           │   ├── lang.list.ts
│           │   └── locale.ts
│           │
│           ├── schema/           # Zod Schemas
│           │   ├── app/
│           │   └── profile/
│           │
│           ├── types/            # 类型定义
│           │   ├── access.ts
│           │   └── rest.ts
│           │
│           ├── assets/           # 资源文件
│           │   ├── icons/
│           │   ├── image/
│           │   └── style/
│           │
│           ├── events/          # 事件定义
│           │   ├── auth.ts
│           │   └── index.ts
│           │
│           ├── plugin/          # Next.js 插件
│           │   ├── rest.client.ts
│           │   ├── rest.server.ts
│           │   ├── middleware.ts
│           │   └── ...
│           │
│           └── proxy.ts         # 代理配置
│
├── libs/                          # 共享库目录
│   ├── account/                   # 账号管理模块
│   │   ├── README.md
│   │   ├── tsconfig.lib.json
│   │   └── src/
│   │       ├── account.module.ts
│   │       ├── index.ts
│   │       ├── controller/        # 控制器
│   │       │   ├── account.controller.ts
│   │       │   ├── account-otp.controller.ts
│   │       │   └── index.ts
│   │       ├── service/          # 服务
│   │       │   ├── account.service.ts
│   │       │   ├── account-otp.service.ts
│   │       │   ├── account-config.service.ts
│   │       │   └── index.ts
│   │       ├── dto/              # DTO
│   │       │   ├── account.dto.ts
│   │       │   ├── account-otp.dto.ts
│   │       │   └── index.ts
│   │       ├── entity/           # 实体
│   │       │   ├── account.entity.ts
│   │       │   ├── account-token.entity.ts
│   │       │   └── index.ts
│   │       ├── guards/           # 守卫
│   │       │   ├── auth.guard.ts
│   │       │   ├── auth.guard.types.ts
│   │       │   └── index.ts
│   │       └── shared/           # 共享代码
│   │           ├── account.const.ts
│   │           ├── account.error-code.ts
│   │           ├── account.types.ts
│   │           └── index.ts
│   │
│   ├── app/                       # 应用管理模块
│   │   ├── tsconfig.lib.json
│   │   └── src/
│   │       ├── app.module.ts
│   │       ├── app.service.ts
│   │       ├── app.service.spec.ts
│   │       ├── index.ts
│   │       ├── controller/
│   │       ├── service/
│   │       ├── dto/
│   │       ├── entity/
│   │       └── shared/
│   │
│   ├── common/                    # 通用工具模块
│   │   ├── tsconfig.lib.json
│   │   └── src/
│   │       ├── common.module.ts
│   │       ├── index.ts
│   │       ├── service/
│   │       └── shared/
│   │
│   └── types/                     # 共享类型定义
│       ├── README.md
│       ├── tsconfig.lib.json
│       └── src/
│           ├── index.ts
│           ├── common.types.ts
│           ├── regular.ts
│           ├── account/           # 账号类型
│           │   ├── account.schema.ts
│           │   ├── account-otp.schema.ts
│           │   └── index.ts
│           └── app/               # 应用类型
│               ├── app.schema.ts
│               ├── app.types.ts
│               └── index.ts
│
├── docs/                          # 项目文档
│   ├── index.md                   # 文档索引
│   ├── project-overview.md        # 项目概览
│   ├── architecture.md            # 架构决策文档（本文件）
│   ├── architecture-server.md     # Server 架构
│   ├── architecture-web.md        # Web 架构
│   ├── integration-architecture.md # 集成架构
│   ├── api-contracts-server.md    # API 合约
│   ├── data-models-server.md      # 数据模型
│   ├── development-guide.md       # 开发指南
│   ├── source-tree-analysis.md    # 源树分析
│   ├── state-management-web.md    # 状态管理
│   ├── ui-component-inventory-web.md # UI 组件清单
│   ├── project-parts.json         # 项目部分元数据
│   ├── project-scan-report.json   # 项目扫描报告
│   ├── bmm-workflow-status.yaml   # BMM 工作流状态
│   └── sprint-artifacts/          # Sprint 产物
│
├── locales/                       # 国际化文件
│   ├── en.json                    # 英文
│   └── zh-CN.json                 # 中文
│
├── scripts/                       # 脚本目录
│   └── sync-locales-cli.ts       # 同步国际化脚本
│
├── dist/                          # 构建输出目录
│   └── apps/
│       └── server/
│           └── i18n/
│
└── node_modules/                  # 依赖包（pnpm workspace）
```

### Architectural Boundaries

**API Boundaries:**

- **External API Endpoints:** `/api/*`（Base URL）
  - `/api/account/*` - 账号管理
  - `/api/apps/*` - 应用管理
  - `/api/assets/*` - 资源管理
  - `/api/config` - 配置管理
  - `/api/mail-code/*` - 邮件验证码
- **Authentication Boundary:** AuthGuard 全局守卫（`libs/account/src/guards/auth.guard.ts`）
- **Authorization Boundary:** 基于角色的访问控制（RBAC）
- **Data Access Boundary:** TypeORM Repository 模式（Entity → Repository → Service）

**Component Boundaries:**

- **Frontend Component Communication:** Props 传递 + Jotai 状态管理 + TanStack Query
- **State Management Boundaries:**
  - 客户端状态：Jotai atoms（`state/` 目录）
  - 服务端状态：TanStack Query（`hooks/use.query.ts`）
- **Service Communication Pattern:** REST API（HTTP/HTTPS + JSON）
- **Event-Driven Integration Points:** `events/` 目录（如 `auth.ts`）

**Data Boundaries:**

- **Database Schema Boundary:** MySQL 数据库（snake_case 命名）
- **Data Access Pattern:** TypeORM Entity → Repository → Service
- **Cache Boundary:** Redis（会话、Token、频繁访问数据）
- **External Data Integration Points:**
  - Nacos（配置中心）
  - S3/OSS（文件存储）
  - AWS SES / Aliyun（邮件服务）

### Requirements to Structure Mapping

**Feature/Epic Mapping:**

**用户认证与授权：**
- 组件：`apps/web/src/app/(login)/`
- API 路由：`/api/account/login`, `/api/account/register`
- 服务：`libs/account/src/service/account.service.ts`
- 守卫：`libs/account/src/guards/auth.guard.ts`
- 数据库：`account`, `account_token` 表
- 状态：`apps/web/src/state/access.ts`, `apps/web/src/state/profile.ts`

**账号管理：**
- 组件：`apps/web/src/app/(main)/profile/`
- API 路由：`/api/account/profile`, `/api/account/otp/*`
- 服务：`libs/account/src/service/account.service.ts`, `account-otp.service.ts`
- 数据库：`account` 表
- 状态：`apps/web/src/state/profile.ts`

**应用管理：**
- 组件：`apps/web/src/app/(main)/apps/`
- API 路由：`/api/apps/*`
- 服务：`libs/app/src/service/app.service.ts`
- 数据库：`app`, `app_account` 表
- 状态：`apps/web/src/state/app.ts`

**资源管理：**
- 组件：`apps/web/src/components/common/cropper/`
- API 路由：`/api/assets/upload/pre-sign`
- 控制器：`apps/server/src/controller/assets.controller.ts`
- 存储：S3/OSS（通过 `@meta-1/nest-assets`）

**Cross-Cutting Concerns:**

**认证系统：**
- 守卫：`libs/account/src/guards/auth.guard.ts`
- 状态：`apps/web/src/state/access.ts`
- 工具：`apps/web/src/utils/token.ts`
- 事件：`apps/web/src/events/auth.ts`

**国际化：**
- 语言文件：`locales/en.json`, `locales/zh-CN.json`
- 工具：`apps/web/src/utils/i18next.ts`, `locale.client.ts`, `locale.server.ts`
- 配置：`apps/web/src/config/locale.ts`

**错误处理：**
- 后端：`@meta-1/nest-common` ErrorsFilter
- 前端：`apps/web/src/utils/rest.ts`（统一错误处理）

**类型安全：**
- 共享类型：`libs/types/src/`
- Schema：Zod schemas（`libs/types/src/*/schema.ts`）

### Integration Points

**Internal Communication:**

- **前后端通信：** REST API（`apps/web/src/rest/` → `/api/*`）
- **模块间通信：** NestJS 依赖注入（`libs/*/src/`）
- **状态同步：** TanStack Query 自动同步（`hooks/use.query.ts`）

**External Integrations:**

- **配置中心：** Nacos（`@meta-1/nest-nacos`）
- **文件存储：** S3/OSS（`@meta-1/nest-assets`）
- **邮件服务：** AWS SES / Aliyun（`@meta-1/nest-message`）
- **数据库：** MySQL（TypeORM）
- **缓存：** Redis（ioredis）

**Data Flow:**

```
用户交互 (Web)
    ↓
React 组件 (apps/web/src/app/)
    ↓
API 调用 (apps/web/src/rest/)
    ↓
HTTP 请求 (apps/web/src/utils/rest.ts)
    ↓
REST API (apps/server/src/controller/)
    ↓
服务层 (libs/*/src/service/)
    ↓
数据访问 (TypeORM Repository)
    ↓
MySQL 数据库 / Redis 缓存
```

### File Organization Patterns

**Configuration Files:**
- 根目录：`package.json`, `tsconfig.json`, `biome.json`
- 应用级：`apps/*/package.json`, `apps/*/tsconfig.json`
- 环境：`.env`, `.env.local`（gitignore）

**Source Organization:**
- 按功能模块组织（`libs/{module}/src/`）
- 按路由组织（`apps/web/src/app/`）
- 按类型组织（`components/`, `hooks/`, `utils/`）

**Test Organization:**
- 单元测试：`*.spec.ts`（与源文件同目录）
- E2E 测试：`test/e2e/`（如需要）

**Asset Organization:**
- 静态资源：`apps/web/public/`
- 代码资源：`apps/web/src/assets/`
- 国际化：`locales/`

### Development Workflow Integration

**Development Server Structure:**
- 后端：`pnpm run dev:server`（端口 3100）
- 前端：`pnpm run dev:web`（端口 4002）

**Build Process Structure:**
- 后端：`pnpm run build:server` → `dist/apps/server/`
- 前端：`pnpm run build:web` → `apps/web/.next/`

**Deployment Structure:**
- 后端：Node.js 应用（`dist/apps/server/main.js`）
- 前端：Next.js 应用（`.next/` 目录）

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:**

- ✅ **Technology Stack Compatibility:** Next.js 16 + NestJS 11 + TypeScript 5.9.3 完全兼容
- ✅ **Version Compatibility:** 所有版本已记录并验证，无冲突
- ✅ **Pattern Alignment:** 实施模式与技术选择完全对齐
- ✅ **No Contradictory Decisions:** 所有决策相互支持，无矛盾

**Pattern Consistency:**

- ✅ **Naming Conventions:** 数据库（snake_case）、API（kebab-case）、代码（camelCase/PascalCase）完全一致
- ✅ **Structure Patterns:** Monorepo 结构与模块化架构完全支持模块化架构
- ✅ **Communication Patterns:** REST API 模式与前后端架构完全一致
- ✅ **Process Patterns:** 错误处理、加载状态模式与框架特性完全一致

**Structure Alignment:**

- ✅ **Project Structure Supports Decisions:** 项目结构完全支持所有架构决策
- ✅ **Boundaries Properly Defined:** API、组件、数据边界清晰明确
- ✅ **Structure Enables Patterns:** 项目结构完全支持所选模式
- ✅ **Integration Points Structured:** 所有集成点结构合理且明确

### Requirements Coverage Validation ✅

**Functional Requirements Coverage:**

- ✅ **用户认证与授权:** JWT + AuthGuard + OTP 完整支持
- ✅ **账号管理:** AccountService + Profile API + 状态管理完整支持
- ✅ **应用管理:** AppService + CRUD API + 多应用支持完整支持
- ✅ **资源管理:** AssetsController + S3/OSS + 预签名 URL 完整支持
- ✅ **辅助功能:** 邮件服务、国际化、配置管理完整支持

**Non-Functional Requirements Coverage:**

- ✅ **安全性:** RSA 加密、JWT、OTP、RBAC、XSS/CSRF 防护完整覆盖
- ✅ **可扩展性:** Monorepo、模块化、共享类型、配置中心完整支持
- ✅ **性能:** Redis 缓存、查询优化、代码分割完整支持
- ✅ **可用性:** Nacos 降级、错误处理、健康检查完整支持
- ✅ **开发体验:** TypeScript、共享类型、Swagger、Biome 完整支持

### Implementation Readiness Validation ✅

**Decision Completeness:**

- ✅ **Critical Decisions Documented:** 所有关键决策已记录版本
- ✅ **Implementation Patterns Comprehensive:** 实施模式覆盖所有主要冲突点
- ✅ **Consistency Rules Clear:** 一致性规则清晰且可执行
- ✅ **Examples Provided:** 所有主要模式都提供了示例

**Structure Completeness:**

- ✅ **Project Structure Complete:** 项目结构完整且具体
- ✅ **All Files Defined:** 所有文件和目录已定义
- ✅ **Integration Points Specified:** 所有集成点明确指定
- ✅ **Component Boundaries Clear:** 组件边界清晰明确

**Pattern Completeness:**

- ✅ **Conflict Points Addressed:** 所有潜在冲突点已处理
- ✅ **Naming Conventions Comprehensive:** 命名约定覆盖所有领域
- ✅ **Communication Patterns Specified:** 通信模式完全指定
- ✅ **Process Patterns Defined:** 流程模式（错误处理、加载状态）完全定义

### Gap Analysis Results

**Critical Gaps:**
无。所有关键决策和模式已完整定义。

**Important Gaps:**
1. **测试策略:** 架构文档中未详细说明测试组织方式
   - 建议：在开发指南中补充测试策略文档
2. **CI/CD 流程:** 未定义持续集成和部署流程
   - 建议：后续可补充 CI/CD 配置文档

**Nice-to-Have Gaps:**
1. **性能监控:** 可添加性能监控和日志聚合方案
2. **文档生成:** 可补充自动文档生成流程
3. **开发工具:** 可补充推荐的开发工具和扩展

### Validation Issues Addressed

未发现需要立即解决的验证问题。架构文档完整，所有决策一致，模式清晰。

### Architecture Completeness Checklist

**✅ Requirements Analysis**

- [x] Project context thoroughly analyzed
- [x] Scale and complexity assessed
- [x] Technical constraints identified
- [x] Cross-cutting concerns mapped

**✅ Architectural Decisions**

- [x] Critical decisions documented with versions
- [x] Technology stack fully specified
- [x] Integration patterns defined
- [x] Performance considerations addressed

**✅ Implementation Patterns**

- [x] Naming conventions established
- [x] Structure patterns defined
- [x] Communication patterns specified
- [x] Process patterns documented

**✅ Project Structure**

- [x] Complete directory structure defined
- [x] Component boundaries established
- [x] Integration points mapped
- [x] Requirements to structure mapping complete

### Architecture Readiness Assessment

**Overall Status:** ✅ READY FOR IMPLEMENTATION

**Confidence Level:** 高（基于验证结果）

**Key Strengths:**
1. 技术栈成熟且完全兼容
2. 架构决策完整且一致
3. 实施模式清晰且可执行
4. 项目结构完整且具体
5. 需求覆盖全面

**Areas for Future Enhancement:**
1. 测试策略详细化
2. CI/CD 流程定义
3. 性能监控方案
4. 开发工具推荐

### Implementation Handoff

**AI Agent Guidelines:**

- Follow all architectural decisions exactly as documented
- Use implementation patterns consistently across all components
- Respect project structure and boundaries
- Refer to this document for all architectural questions

**First Implementation Priority:**

项目已初始化，架构已建立。后续开发应：
1. 遵循已定义的架构决策和模式
2. 使用已建立的项目结构
3. 遵循命名约定和代码组织模式
4. 参考架构文档进行新功能开发

## Architecture Completion Summary

### Workflow Completion

**Architecture Decision Workflow:** COMPLETED ✅
**Total Steps Completed:** 8
**Date Completed:** 2025-12-03T03:00:01.000Z
**Document Location:** docs/architecture.md

### Final Architecture Deliverables

**📋 Complete Architecture Document**

- All architectural decisions documented with specific versions
- Implementation patterns ensuring AI agent consistency
- Complete project structure with all files and directories
- Requirements to architecture mapping
- Validation confirming coherence and completeness

**🏗️ Implementation Ready Foundation**

- 20+ architectural decisions made
- 6 major implementation pattern categories defined
- 4 architectural components specified (Web, Server, Shared Libraries, Infrastructure)
- 5 functional requirement categories fully supported
- 5 non-functional requirement categories fully addressed

**📚 AI Agent Implementation Guide**

- Technology stack with verified versions
- Consistency rules that prevent implementation conflicts
- Project structure with clear boundaries
- Integration patterns and communication standards

### Implementation Handoff

**For AI Agents:**
This architecture document is your complete guide for implementing Authub. Follow all decisions, patterns, and structures exactly as documented.

**First Implementation Priority:**

项目已初始化，架构已建立。后续开发应：
1. 遵循已定义的架构决策和模式
2. 使用已建立的项目结构
3. 遵循命名约定和代码组织模式
4. 参考架构文档进行新功能开发

**Development Sequence:**

1. Review the complete architecture document at `docs/architecture.md`
2. Follow architectural decisions when implementing new features
3. Use established patterns consistently across all components
4. Maintain consistency with documented rules
5. Update architecture document when making major technical decisions

### Quality Assurance Checklist

**✅ Architecture Coherence**

- [x] All decisions work together without conflicts
- [x] Technology choices are compatible
- [x] Patterns support the architectural decisions
- [x] Structure aligns with all choices

**✅ Requirements Coverage**

- [x] All functional requirements are supported
- [x] All non-functional requirements are addressed
- [x] Cross-cutting concerns are handled
- [x] Integration points are defined

**✅ Implementation Readiness**

- [x] Decisions are specific and actionable
- [x] Patterns prevent agent conflicts
- [x] Structure is complete and unambiguous
- [x] Examples are provided for clarity

### Project Success Factors

**🎯 Clear Decision Framework**
Every technology choice was made collaboratively with clear rationale, ensuring all stakeholders understand the architectural direction.

**🔧 Consistency Guarantee**
Implementation patterns and rules ensure that multiple AI agents will produce compatible, consistent code that works together seamlessly.

**📋 Complete Coverage**
All project requirements are architecturally supported, with clear mapping from business needs to technical implementation.

**🏗️ Solid Foundation**
The existing architecture and patterns provide a production-ready foundation following current best practices.

---

**Architecture Status:** READY FOR IMPLEMENTATION ✅

**Next Phase:** Begin implementation using the architectural decisions and patterns documented herein.

**Document Maintenance:** Update this architecture when major technical decisions are made during implementation.
