# Project Overview

**项目名称:** Authub  
**项目类型:** Monorepo (多部分项目)  
**主要技术:** Next.js 16 + NestJS 11  
**生成日期:** 2025-12-03

## 执行摘要

Authub 是一个基于 NestJS 和 Next.js 构建的企业级统一用户授权和身份管理系统 Monorepo 项目。项目采用模块化设计，提供完整的用户认证、授权、账号管理和应用管理功能，支持多应用场景和双因素认证。

## 项目结构

### 仓库类型

**Monorepo** - 包含多个独立应用和共享库

### 项目组成部分

| 部分 ID | 类型 | 显示名称 | 根路径 | 技术栈 |
|---------|------|----------|--------|--------|
| web | Web | Web Application | `apps/web` | Next.js 16, React 19, TypeScript |
| server | Backend | Backend Service | `apps/server` | NestJS 11, TypeORM, MySQL, Redis |
| libs | Library | Shared Libraries | `libs` | TypeScript libraries |

## 技术栈摘要

### Web 应用

- **框架:** Next.js 16.0.4
- **UI 库:** React 19.2.0
- **语言:** TypeScript 5.9.3
- **状态管理:** Jotai 2.10.2
- **数据获取:** TanStack Query 5.80.3
- **样式:** Tailwind CSS 3.4.0
- **UI 组件库:** @meta-1/design 0.0.178

### Server 应用

- **框架:** NestJS 11.1.9
- **语言:** TypeScript 5.9.3
- **ORM:** TypeORM 0.3.27
- **数据库:** MySQL (mysql2 3.15.3)
- **缓存:** Redis (ioredis 5.8.2)
- **API 文档:** Swagger 11.2.1
- **配置管理:** Nacos 2.6.0

### 共享库

- **@meta-1/authub-account** - 账号管理模块
- **@meta-1/authub-app** - 应用管理模块
- **@meta-1/authub-common** - 通用工具模块
- **@meta-1/authub-types** - 共享类型定义

## 架构类型

### Web 应用架构

**模式:** Component-based Layered Architecture

- App Router 架构
- 组件化设计
- 服务端渲染支持
- 客户端状态管理（Jotai）
- 服务端状态管理（TanStack Query）

### Server 应用架构

**模式:** Modular Service Architecture

- 模块化设计
- 依赖注入
- 配置驱动
- 分层架构（Controller → Service → Repository）

## 仓库结构

```
authub/
├── apps/                    # 应用程序
│   ├── server/             # NestJS 后端服务
│   └── web/                # Next.js 前端应用
├── libs/                    # 共享库
│   ├── account/            # 账号管理模块
│   ├── app/                # 应用管理模块
│   ├── common/             # 通用工具模块
│   └── types/              # 共享类型定义
├── docs/                    # 项目文档
├── locales/                 # 国际化文件
│   ├── en.json
│   └── zh-CN.json
├── scripts/                # 构建和工具脚本
└── package.json            # 根 package.json
```

## 核心功能

### 用户认证

- 用户登录、注册
- 密码重置
- JWT Token 管理
- 多应用 Token 支持

### 账号管理

- 用户信息管理
- 权限管理
- 双因素认证（OTP）
- 会话管理

### 应用管理

- 多应用支持
- 应用配置管理
- 应用账号关联

### 资源管理

- 文件上传
- 头像管理
- S3/OSS 存储支持

## 文档导航

### 架构文档

- [Server 架构文档](./architecture-server.md) - 后端服务架构详解
- [Web 架构文档](./architecture-web.md) - 前端应用架构详解
- [集成架构文档](./integration-architecture.md) - 各部分集成方式

### API 文档

- [API 合约 - Server](./api-contracts-server.md) - 后端 API 端点文档

### 数据文档

- [数据模型 - Server](./data-models-server.md) - 数据库架构和实体定义

### 开发文档

- [开发指南](./development-guide.md) - 开发环境设置和开发流程
- [源树分析](./source-tree-analysis.md) - 项目目录结构详解

### 组件文档

- [UI 组件清单 - Web](./ui-component-inventory-web.md) - 前端组件目录
- [状态管理 - Web](./state-management-web.md) - 前端状态管理详解

## 快速开始

### 环境要求

- Node.js >= 18
- pnpm >= 8
- Redis >= 6.0
- MySQL >= 8.0
- Nacos >= 2.0 (可选)

### 安装依赖

```bash
pnpm install
```

### 启动开发服务器

```bash
# 启动后端服务
pnpm run dev:server

# 启动前端应用
pnpm run dev:web
```

### 访问应用

- **前端应用:** http://localhost:4002
- **后端服务:** http://localhost:3100
- **API 文档:** http://localhost:3100/api

详细快速开始指南请参考: [开发指南](./development-guide.md)

## 技术亮点

### 配置管理

- **Nacos 配置中心** - 集中式配置管理
- **配置热更新** - 无需重启服务
- **环境隔离** - 支持多环境配置
- **降级支持** - Nacos 不可用时自动降级

### 安全特性

- **RSA 密码加密** - 传输层加密
- **JWT Token 认证** - 无状态认证
- **双因素认证** - OTP 支持
- **权限控制** - 基于角色的访问控制（RBAC）

### 开发体验

- **类型安全** - 完整的 TypeScript 支持
- **共享类型** - 前后端类型一致
- **API 文档** - Swagger 自动生成
- **代码规范** - Biome 代码检查和格式化

## 项目状态

**当前阶段:** 开发中  
**文档状态:** 完整  
**测试覆盖:** 进行中

## 相关链接

- [项目 README](../README.md)
- [Server README](../apps/server/README.md)
- [Web README](../apps/web/README.md)
- [Account Library README](../libs/account/README.md)
- [Types Library README](../libs/types/README.md)

