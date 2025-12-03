# Server 架构文档

**生成日期**: 2025-12-03  
**项目类型**: Backend (NestJS)  
**架构模式**: 模块化服务架构

## 执行摘要

Authub Server 是基于 NestJS 11 构建的统一用户授权和身份管理后端服务，提供完整的用户认证、授权和管理功能。采用模块化服务架构设计，通过 NestJS 依赖注入系统组织代码，支持配置中心化管理、数据库集成和外部服务扩展。

## 项目分类

- **仓库类型**: Monorepo（多部分项目）
- **项目类型**: Backend
- **主要语言**: TypeScript 5.9.3
- **架构模式**: 模块化服务架构

## 技术栈

| 类别 | 技术 | 版本 | 说明 |
|------|------|------|------|
| **运行时** | Node.js | >= 18 | JavaScript 运行时环境 |
| **语言** | TypeScript | 5.9.3 | 类型安全的 JavaScript 超集 |
| **框架** | NestJS | 11.1.9 | 企业级 Node.js 框架 |
| **HTTP服务器** | Express | (via NestJS) | HTTP 服务器基础 |
| **数据库** | MySQL | >= 8.0 | 关系型数据库 |
| **ORM** | TypeORM | 0.3.27 | 数据库 ORM 框架 |
| **缓存** | Redis | >= 6.0 | 缓存和会话存储 |
| **Redis客户端** | ioredis | 5.8.2 | Redis 客户端库 |
| **配置管理** | Nacos | >= 2.0 | 配置中心和服务发现 |
| **Nacos集成** | @meta-1/nest-nacos | 0.0.7 | Nacos 集成模块 |
| **国际化** | nestjs-i18n | 10.5.1 | 多语言支持 |
| **API文档** | Swagger | 11.2.1 | API 文档自动生成 |
| **数据验证** | Zod | 3.25.76 | Schema 验证库 |
| **Zod集成** | nestjs-zod | 5.0.1 | NestJS Zod 集成 |
| **邮件服务** | @meta-1/nest-message | 0.0.9 | 邮件发送服务 |
| **资源管理** | @meta-1/nest-assets | 0.0.5 | 文件上传和存储 |
| **安全模块** | @meta-1/nest-security | 0.0.8 | 认证和授权 |
| **通用工具** | @meta-1/nest-common | 0.0.13 | 通用工具和装饰器 |
| **账号模块** | @meta-1/authub-account | - | 账号管理核心模块 |
| **应用模块** | @meta-1/authub-app | - | 应用管理模块 |
| **通用模块** | @meta-1/authub-common | - | 通用工具模块 |
| **类型定义** | @meta-1/authub-types | - | 共享类型定义 |

### 架构模式

- **模块化设计**: 基于 NestJS Module 系统
- **依赖注入**: NestJS DI 容器
- **配置驱动**: Nacos 配置中心管理配置
- **分层架构**: Controller → Service → Repository

## 架构模式详解

### 模块化服务架构

Server 采用 NestJS 模块化设计，每个功能模块独立封装：

```
AppModule (Root)
├── AccountModule (@meta-1/authub-account)
│   ├── AccountController
│   ├── AccountService
│   ├── AccountOtpService
│   └── AccountConfigService
├── AppModule (@meta-1/authub-app)
│   ├── AppController
│   └── AppService
├── CommonModule (@meta-1/authub-common)
│   └── ConfigService
├── SecurityModule (@meta-1/nest-security)
├── MessageModule (@meta-1/nest-message)
├── AssetsModule (@meta-1/nest-assets)
└── NacosModule (@meta-1/nest-nacos)
```

### 依赖注入

使用 NestJS 的依赖注入系统，通过构造函数注入依赖：

```typescript
@Injectable()
export class AccountService {
  constructor(
    @InjectRepository(Account) private repository: Repository<Account>,
    private readonly configService: AccountConfigService,
    private readonly encryptService: EncryptService,
  ) {}
}
```

### 配置管理

采用 Nacos 配置中心统一管理配置：

1. **环境变量配置**（仅需 Nacos 连接信息）:
   ```env
   NACOS_SERVER=localhost:8848
   APP_NAME=authub-server
   ```

2. **Nacos 配置**（业务配置）:
   ```yaml
   database:
     host: localhost
     port: 3306
     username: root
     password: your-password
     database: authub
   redis:
     host: localhost
     port: 6379
   account:
     rsa: {...}
     jwt: {...}
     otp: {...}
   ```

3. **降级支持**: Nacos 不可用时，服务以降级模式启动（跳过数据库和 Redis）

## 数据架构

### 数据库设计

**ORM:** TypeORM  
**数据库:** MySQL  
**命名策略:** Snake Case (SnakeNamingStrategy)

#### 核心实体

**Account (账号)**
- 用户基本信息
- 密码加密存储
- OTP 双因素认证支持
- 多应用关联

**AccountToken (账号令牌)**
- JWT Token 管理
- 多应用 Token 支持
- Token 过期管理

**App (应用)**
- 应用基本信息
- 应用配置

**AppAccount (应用账号关联)**
- 账号与应用的多对多关系
- 应用内权限管理

详细数据模型请参考: [数据模型文档](./data-models-server.md)

### 缓存架构

**Redis 使用场景:**
- 用户会话存储
- JWT Token 缓存
- OTP 验证码存储（时间限制）
- 频繁访问数据缓存

## API 设计

### RESTful API

**Base URL:** `/api`  
**认证方式:** Bearer Token (JWT)

#### API 端点分类

1. **账号管理** (`/api/account/*`)
   - 登录、注册、登出
   - 用户信息获取
   - OTP 管理

2. **应用管理** (`/api/apps/*`)
   - 应用列表
   - 应用 CRUD
   - 应用配置

3. **资源管理** (`/api/assets/*`)
   - 文件上传
   - 头像管理

4. **配置管理** (`/api/config`)
   - 服务器配置获取

5. **邮件验证码** (`/api/mail-code/*`)
   - 验证码发送

详细 API 文档请参考: [API 合约文档](./api-contracts-server.md)

### 统一响应格式

所有 API 响应遵循统一格式：

```typescript
{
  code: number;        // 状态码 (0=成功)
  success: boolean;    // 是否成功
  message: string;     // 消息
  data: T;            // 数据
  timestamp: string;    // 时间戳
}
```

### 错误处理

使用全局异常过滤器统一处理错误：

```typescript
import { ErrorsFilter, AppError } from '@meta-1/nest-common';

app.useGlobalFilters(new ErrorsFilter());
```

## 组件概述

### Controller Layer

**位置:** `apps/server/src/controller/`

- `AppController` - 应用根控制器
- `AssetsController` - 资源管理控制器
- `ConfigController` - 配置管理控制器
- `MailCodeController` - 邮件验证码控制器

### Service Layer

**位置:** `libs/*/src/service/`

- `AccountService` - 账号业务逻辑
- `AccountOtpService` - OTP 业务逻辑
- `AccountConfigService` - 账号配置服务
- `AppService` - 应用业务逻辑

### Entity Layer

**位置:** `libs/*/src/entity/`

- `AccountEntity` - 账号实体
- `AccountTokenEntity` - Token 实体
- `AppEntity` - 应用实体
- `AppAccountEntity` - 应用账号关联实体

## 源树结构

```
apps/server/
├── src/
│   ├── controller/         # REST API 控制器
│   │   ├── app.controller.ts
│   │   ├── assets.controller.ts
│   │   ├── config.controller.ts
│   │   └── mail-code.controller.ts
│   ├── dto/                # 数据传输对象
│   │   └── config.dto.ts
│   ├── app.module.ts       # 根模块
│   ├── app.swagger.ts      # Swagger 配置
│   └── main.ts             # 应用入口
└── tsconfig.app.json       # TypeScript 配置
```

详细源树请参考: [源树分析文档](./source-tree-analysis.md)

## 开发工作流

### 启动开发服务器

```bash
pnpm run dev:server
```

服务运行在: http://localhost:3100

### 访问 Swagger 文档

http://localhost:3100/api

### 添加新功能

1. **创建 Controller:**
   ```bash
   nest g controller users
   ```

2. **创建 Service:**
   ```bash
   nest g service users
   ```

3. **注册到 Module:**
   ```typescript
   @Module({
     controllers: [UserController],
     providers: [UserService],
   })
   export class UserModule {}
   ```

详细开发指南请参考: [开发指南文档](./development-guide.md)

## 部署架构

### 构建流程

```bash
# 构建生产版本
pnpm run build:server

# 输出目录: dist/apps/server/
```

### 运行生产版本

```bash
pnpm run start:server
```

### 环境要求

- Node.js >= 18
- MySQL >= 8.0
- Redis >= 6.0
- Nacos >= 2.0 (可选)

### 配置管理

- 生产环境配置通过 Nacos 管理
- 支持配置热更新
- 支持环境隔离（namespace）

## 测试策略

### 单元测试

```bash
pnpm run test
```

### 测试覆盖率

```bash
pnpm run test:cov
```

### E2E 测试

```bash
pnpm run test:e2e
```

## 安全特性

### 认证

- JWT Token 认证
- Token 自动刷新机制
- 多应用 Token 支持

### 授权

- 基于角色的访问控制（RBAC）
- `AuthGuard` 全局守卫
- 权限检查在服务层

### 数据保护

- RSA 密码加密传输
- 密码加密存储
- SQL 注入防护（TypeORM）
- XSS 防护

## 集成点

### 与 Web 应用集成

- REST API 通信
- JWT Token 认证
- 共享类型定义 (`@meta-1/authub-types`)

### 与外部服务集成

- **Nacos**: 配置管理
- **MySQL**: 数据持久化
- **Redis**: 缓存和会话
- **AWS SES / Aliyun**: 邮件服务
- **S3 / OSS**: 文件存储

详细集成架构请参考: [集成架构文档](./integration-architecture.md)

## 性能优化

### 缓存策略

- Redis 缓存频繁访问数据
- 会话缓存减少数据库查询
- Token 缓存提升验证性能

### 数据库优化

- TypeORM 查询优化
- 索引优化
- 连接池管理

## 监控和日志

### 日志

- NestJS 内置日志系统
- 开发模式详细日志
- 生产模式错误日志

### 监控

- Swagger API 文档
- 健康检查端点
- 错误追踪

## 故障排查

### 常见问题

1. **Redis 连接失败**
   - 检查 Redis 服务状态
   - 验证 Nacos 配置

2. **Nacos 连接失败**
   - 检查 Nacos 服务状态
   - 验证环境变量配置
   - 服务会以降级模式启动

3. **数据库连接失败**
   - 检查 MySQL 服务状态
   - 验证 Nacos 数据库配置

详细故障排查请参考: [开发指南文档](./development-guide.md)

