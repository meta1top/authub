# Authub 项目文档索引

**项目名称:** Authub  
**项目类型:** Monorepo（多部分项目）  
**主要技术:** Next.js 16 + NestJS 11  
**最后更新:** 2025-12-03

## 项目概述

Authub 是一个基于 NestJS 和 Next.js 构建的企业级统一用户授权和身份管理系统 Monorepo 项目。项目采用模块化设计，提供完整的用户认证、授权、账号管理和应用管理功能。

### 项目结构

- **类型:** Monorepo（3 个部分）
- **主要语言:** TypeScript 5.9.3
- **架构:** 模块化服务架构 + 组件化前端

### 快速参考

#### Web Application (apps/web)

- **类型:** Web Application
- **技术栈:** Next.js 16, React 19, TypeScript
- **根路径:** `apps/web`
- **入口点:** `apps/web/src/app/layout.tsx`
- **架构模式:** Component-based Layered Architecture
- **端口:** 4002

#### Backend Service (apps/server)

- **类型:** Backend Service
- **技术栈:** NestJS 11, TypeORM, MySQL, Redis
- **根路径:** `apps/server`
- **入口点:** `apps/server/src/main.ts`
- **架构模式:** Modular Service Architecture
- **端口:** 3100

#### Shared Libraries (libs)

- **类型:** Shared Libraries
- **技术栈:** TypeScript libraries
- **根路径:** `libs`
- **包含库:**
  - `@meta-1/authub-account` - 账号管理模块
  - `@meta-1/authub-app` - 应用管理模块
  - `@meta-1/authub-common` - 通用工具模块
  - `@meta-1/authub-types` - 共享类型定义

## 生成的文档

### 项目概览

- [项目概览](./project-overview.md) - 项目整体介绍和快速开始

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

### 组件和状态文档

- [UI 组件清单 - Web](./ui-component-inventory-web.md) - 前端组件目录
- [状态管理 - Web](./state-management-web.md) - 前端状态管理详解

### 元数据

- [项目部分元数据](./project-parts.json) - 项目结构和集成点 JSON 格式

## 现有文档

### README 文件

- [项目根 README](../README.md) - 项目概述和快速开始
- [Server README](../apps/server/README.md) - 后端服务文档
- [Web README](../apps/web/README.md) - 前端应用文档
- [Account Library README](../libs/account/README.md) - 账号管理模块文档
- [Types Library README](../libs/types/README.md) - 类型定义库文档

## 快速开始

### 环境要求

- Node.js >= 18
- pnpm >= 8
- Redis >= 6.0
- MySQL >= 8.0
- Nacos >= 2.0 (可选)

### 安装和启动

```bash
# 安装依赖
pnpm install

# 启动后端服务（端口 3100）
pnpm run dev:server

# 启动前端应用（端口 4002）
pnpm run dev:web
```

### 访问地址

- **前端应用:** http://localhost:4002
- **后端服务:** http://localhost:3100
- **API 文档:** http://localhost:3100/api

详细设置指南请参考: [开发指南](./development-guide.md)

## 文档导航指南

### 对于 AI 辅助开发

**创建 Brownfield PRD 时:**
- 参考本索引文件 (`index.md`) 作为主要入口点
- 查看 [项目概览](./project-overview.md) 了解项目整体结构
- 查看 [集成架构文档](./integration-architecture.md) 了解各部分如何集成

**对于 UI 功能开发:**
- 参考 [Web 架构文档](./architecture-web.md)
- 查看 [UI 组件清单](./ui-component-inventory-web.md) 了解可用组件
- 查看 [状态管理文档](./state-management-web.md) 了解状态管理方式

**对于 API 功能开发:**
- 参考 [Server 架构文档](./architecture-server.md)
- 查看 [API 合约文档](./api-contracts-server.md) 了解 API 端点
- 查看 [数据模型文档](./data-models-server.md) 了解数据库结构

**对于全栈功能开发:**
- 参考两部分架构文档（Web + Server）
- 查看 [集成架构文档](./integration-architecture.md) 了解数据流
- 查看 [源树分析](./source-tree-analysis.md) 了解代码组织

### 文档使用建议

1. **开始新功能前:** 先阅读相关架构文档，了解现有设计模式
2. **遇到问题时:** 查看开发指南和故障排查部分
3. **集成开发时:** 参考集成架构文档了解通信方式
4. **类型定义:** 使用 `@meta-1/authub-types` 确保类型一致

## 技术栈摘要

### Web 应用技术

- Next.js 16.0.4 (App Router)
- React 19.2.0
- TypeScript 5.9.3
- Jotai 2.10.2 (状态管理)
- TanStack Query 5.80.3 (数据获取)
- Tailwind CSS 3.4.0 (样式)
- @meta-1/design 0.0.178 (UI 组件库)

### Server 应用技术

- NestJS 11.1.9
- TypeScript 5.9.3
- TypeORM 0.3.27 (ORM)
- MySQL (数据库)
- Redis/ioredis 5.8.2 (缓存)
- Swagger 11.2.1 (API 文档)
- Nacos 2.6.0 (配置管理)

## 核心功能

- **用户认证** - 登录、注册、密码重置
- **账号管理** - 用户信息、权限管理
- **双因素认证** - OTP 支持
- **应用管理** - 多应用支持
- **资源管理** - 文件上传、头像管理

## 安全特性

- RSA 密码加密传输
- JWT Token 认证
- 双因素认证（OTP）
- 基于角色的访问控制（RBAC）
- XSS 和 CSRF 防护

## 配置管理

- **Nacos 配置中心** - 集中式配置管理
- **配置热更新** - 无需重启服务
- **环境隔离** - 支持多环境配置
- **降级支持** - Nacos 不可用时自动降级

## 项目状态

- **当前阶段:** 开发中
- **文档状态:** 完整
- **测试覆盖:** 进行中

## 相关资源

- [项目 GitHub 仓库](https://github.com/zkit-org/packages)
- [NestJS 文档](https://docs.nestjs.com/)
- [Next.js 文档](https://nextjs.org/docs)
- [TypeORM 文档](https://typeorm.io/)

---

**提示:** 这是 AI 辅助开发的主要检索入口点。所有文档都从本索引文件链接，便于快速查找相关信息。

