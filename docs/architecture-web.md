# Web 架构文档

**生成日期**: 2025-12-03  
**项目类型**: Web (Next.js)  
**架构模式**: App Router + 组件化

## 执行摘要

Authub Web 是基于 Next.js 16 和 React 19 构建的现代化统一用户授权平台前端应用。采用 Next.js App Router 架构，支持服务端渲染和客户端交互，使用 Jotai 进行轻量级状态管理，TanStack Query 管理服务端状态，提供完整的用户认证、账号管理和应用管理功能。

## 项目分类

- **仓库类型**: Monorepo（多部分项目）
- **项目类型**: Web
- **主要语言**: TypeScript 5.9.3
- **架构模式**: App Router + 组件化

## 技术栈

| 类别 | 技术 | 版本 | 说明 |
|------|------|------|------|
| **运行时** | Node.js | >= 18 | JavaScript 运行时环境 |
| **语言** | TypeScript | 5.9.3 | 类型安全的 JavaScript 超集 |
| **框架** | Next.js | 16.0.4 | React 应用框架（App Router） |
| **UI库** | React | 19.2.0 | 用户界面库 |
| **DOM库** | React DOM | 19.2.0 | React DOM 渲染 |
| **样式框架** | Tailwind CSS | 3.4.0 | 原子化 CSS 框架 |
| **PostCSS** | PostCSS | 8.4.0 | CSS 后处理器 |
| **Autoprefixer** | Autoprefixer | 10.4.0 | CSS 自动前缀 |
| **状态管理** | Jotai | 2.10.2 | 轻量级原子状态管理 |
| **数据获取** | TanStack Query | 5.80.3 | 数据获取和缓存 |
|缓存 |
| **HTTP客户端** | Axios | 1.8.2 | HTTP 请求库 |
| **表单管理** | React Hook Form | 7.55.0 | 表单状态管理 |
| **国际化** | i18next | 23.11.5 | 国际化框架 |
| **React i18n** | react-i18next | 15.5.1 | React i18n 集成 |
| **语言检测** | i18next-browser-languagedetector | 8.0.4 | 浏览器语言检测 |
| **主题切换** | next-themes | 0.4.4 | 主题切换支持 |
| **URL状态** | nuqs | 2.4.3 | URL 查询参数状态管理 |
| **加密** | JSEncrypt | 3.3.2 | RSA 加密库 |
| **图片裁剪** | Cropper.js | 1.6.2 | 图片裁剪库 |
| **React裁剪** | react-cropper | 2.3.3 | React Cropper 组件 |
| **OTP输入** | input-otp | 1.4.2 | OTP 输入组件 |
| **Cookie管理** | js-cookie | 3.0.5 | Cookie 操作库 |
| **Next.js Cookie** | cookies-next | 5.0.2 | Next.js Cookie 工具 |
| **工具库** | es-toolkit | 1.35.0 | 现代工具库 |
| **3D图形** | ogl | 1.0.11 | WebGL 库 |
| **UI组件库** | @meta-1/design | 0.0.178 | 内部 UI 组件库 |
| **编辑器** | @meta-1/editor | 0.0.29 | 富文本编辑器 |

### 架构模式

- **App Router**: Next.js 16 App Router 架构
- **组件化**: React 组件化开发
- **服务端渲染**: Next.js SSR/SSG 支持
- **客户端状态**: Jotai 原子状态管理
- **服务端状态**: TanStack Query 数据缓存

## 架构模式详解

### App Router 架构

Next.js 16 使用 App Router 架构，基于文件系统的路由：

```
app/
├── (login)/          # 路由组（不影响 URL）
│   ├── layout.tsx   # 登录布局
│   ├── login/       # /login
│   └── register/    # /register
├── (main)/          # 路由组
│   ├── layout.tsx   # 主布局
│   ├── page.tsx     # /
│   ├── apps/        # /apps
│   ├── profile/     # /profile
│   └── users/       # /users
└── layout.tsx       # 根布局
```

### 组件化设计

采用 React 组件化开发，组件按功能分类：

- **Layout Components**: 布局组件（Root、Main、Login）
- **Common Components**: 通用组件（Access、Account、Form）
- **Feature Components**: 功能组件（Apps、Profile、Users）

详细组件清单请参考: [UI 组件清单文档](./ui-component-inventory-web.md)

### 状态管理策略

采用双状态管理策略：

- **Jotai**: 客户端轻量级状态（UI 状态、表单状态、用户状态）
- **TanStack Query**: 服务端状态（API 数据、缓存、同步）

详细状态管理请参考: [状态管理文档](./state-management-web.md)

### 数据流

```
User Interaction
    ↓
Component (React)
    ↓
┌──────────────┬──────────────┐
│   Jotai      │ TanStack     │
│ (客户端状态)  │ Query        │
│              │ (服务端状态)  │
└──────────────┴──────┬───────┘
                      │
                 REST API
                 (Axios)
                      │
                  Server
```

## 数据架构

### API 客户端

**位置:** `apps/web/src/rest/`

- `account.ts` - 账号相关 API
- `profile/*` - 用户资料 API
- `app/*` - 应用管理 API
- `assets.ts` - 资源管理 API
- `public.ts` - 公开 API

**HTTP 客户端:** Axios (封装在 `utils/rest.ts`)

**特性:**
- 统一错误处理
- 请求/响应拦截器
- Token 自动注入
- 响应数据格式化

### 状态管理

**Jotai Atoms:**

- `accessState` - 用户权限数组
- `isLoginState` - 登录状态
- `profileState` - 用户资料
- `currentAppState` - 当前应用上下文
- `configState` - 配置信息
- `layoutState` - 布局状态
- `publicState` - 公开状态

**TanStack Query:**

- API 数据缓存
- 自动重新获取
- 乐观更新
- 后台同步

## API 设计

### REST API 集成

**Base URL:** 通过 `NEXT_PUBLIC_API_URL` 环境变量配置  
**认证方式:** JWT Token (存储在 cookies)

#### API 调用示例

```typescript
// apps/web/src/rest/account.ts
import { post, get } from "@/utils/rest";
import type { LoginData, Token } from "@meta-1/authub-types";

export const login = (data: LoginData) => 
  post<Token, LoginData>("@api/account/login", data);

export const profile = () => 
  get<Profile>("@api/account/profile");
```

### 类型安全

使用共享类型库 `@meta-1/authub-types` 确保前后端类型一致：

```typescript
import type { LoginData, Profile, Token } from "@meta-1/authub-types";
import { LoginSchema } from "@meta-1/authub-types";
```

## 组件概述

### Layout Components

**位置:** `apps/web/src/components/layout/`

- `root/` - 根布局
- `html/` - HTML 结构
- `main/` - 主应用布局
- `login/` - 登录页面布局
- `app/` - 应用布局
- `loading/` - 加载状态布局

### Common Components

**位置:** `apps/web/src/components/common/`

- `access/` - 权限控制组件
- `account/` - 账号相关组件
- `form/` - 表单组件
- `layout/` - 布局辅助组件
- `background/` - 背景效果组件

### Feature Components

**位置:** `apps/web/src/app/(main)/`

- `apps/` - 应用管理页面
- `profile/` - 用户资料页面
- `users/` - 用户管理页面

详细组件清单请参考: [UI 组件清单文档](./ui-component-inventory-web.md)

## 源树结构

```
apps/web/
├── src/
│   ├── app/                # Next.js App Router
│   │   ├── (login)/       # 登录路由组
│   │   ├── (main)/        # 主路由组
│   │   └── layout.tsx     # 根布局
│   ├── components/         # React 组件
│   │   ├── common/        # 通用组件
│   │   ├── layout/        # 布局组件
│   │   └── background/    # 背景组件
│   ├── rest/              # API 客户端
│   │   ├── account.ts
│   │   ├── profile/
│   │   └── app/
│   ├── state/              # Jotai 状态
│   │   ├── access.ts
│   │   ├── profile.ts
│   │   └── app.ts
│   ├── hooks/              # 自定义 Hooks
│   │   ├── use.access.ts
│   │   ├── use.profile.ts
│   │   └── use.query.ts
│   ├── utils/              # 工具函数
│   │   ├── rest.ts        # HTTP 客户端
│   │   ├── token.ts       # Token 管理
│   │   └── crypto.ts      # 加密工具
│   ├── config/             # 配置
│   ├── schema/             # Zod Schemas
│   └── types/              # 类型定义
├── public/                 # 静态资源
│   └── assets/
├── next.config.ts          # Next.js 配置
└── package.json
```

详细源树请参考: [源树分析文档](./source-tree-analysis.md)

## 开发工作流

### 启动开发服务器

```bash
pnpm run dev:web
```

应用运行在: http://localhost:4002

### 添加新页面

在 `apps/web/src/app/` 下创建目录和 `page.tsx`:

```typescript
// apps/web/src/app/new-page/page.tsx
export default function NewPage() {
  return <div>New Page</div>;
}
```

### 添加新组件

在 `apps/web/src/components/` 下创建组件:

```typescript
// apps/web/src/components/common/NewComponent.tsx
export function NewComponent() {
  return <div>New Component</div>;
}
```

### 添加 API 调用

在 `apps/web/src/rest/` 下创建 API 函数:

```typescript
// apps/web/src/rest/new-api.ts
import { get } from "@/utils/rest";

export const getNewData = () => get<NewData>("@api/new-endpoint");
```

详细开发指南请参考: [开发指南文档](./development-guide.md)

## 部署架构

### 构建流程

```bash
# 构建生产版本
pnpm run build:web

# 输出目录: .next/
```

### 运行生产版本

```bash
pnpm run start:web
```

### 部署平台

- **Vercel**: 推荐平台（Next.js 官方支持）
- **Netlify**: 支持 Next.js
- **自托管**: Node.js 服务器

### 环境变量

```env
NEXT_PUBLIC_API_URL=http://localhost:3100
```

## 测试策略

### 组件测试

使用 React Testing Library 和 Jest

### E2E 测试

使用 Playwright 或 Cypress

## 安全特性

### 前端安全

- RSA 加密敏感数据（密码）
- HTTPS 传输
- Token 自动刷新
- XSS 防护
- CSRF 防护

### 认证流程

1. 用户输入密码
2. 使用 RSA 公钥加密密码
3. 发送加密密码到服务器
4. 服务器返回 JWT Token
5. Token 存储在 httpOnly cookies
6. 后续请求自动携带 Token

## 性能优化

### 代码分割

- Next.js 自动代码分割
- 路由级别代码分割
- 动态导入组件

### 缓存策略

- TanStack Query 自动缓存
- Next.js 静态页面缓存
- 浏览器缓存静态资源

### 优化技术

- 图片优化（Next.js Image）
- 字体优化
- CSS 优化（Tailwind CSS 生产构建）

## 国际化

### 支持语言

- 中文 (zh-CN)
- 英文 (en)

### 实现方式

- i18next 框架
- 语言文件: `locales/en.json`, `locales/zh-CN.json`
- 自动语言检测
- URL 参数支持 (`?lang=en`)

### 使用示例

```typescript
import { useTranslation } from 'react-i18next';

export function Component() {
  const { t } = useTranslation();
  return <div>{t('common.welcome')}</div>;
}
```

## 主题支持

### 明暗主题

使用 `next-themes` 实现主题切换：

- 自动检测系统主题
- 手动切换主题
- 主题持久化存储

## 集成点

### 与 Server 集成

- REST API 通信
- JWT Token 认证
- 共享类型定义 (`@meta-1/authub-types`)

### 与外部服务集成

- 文件上传到 S3/OSS（通过 Server）
- 邮件服务（通过 Server）

详细集成架构请参考: [集成架构文档](./integration-architecture.md)

## 监控和调试

### 开发工具

- React DevTools
- Next.js DevTools
- TanStack Query DevTools

### 错误追踪

- 全局错误边界
- API 错误处理
- 用户友好的错误提示

## 故障排查

### 常见问题

1. **API 请求失败**
   - 检查 `NEXT_PUBLIC_API_URL` 环境变量
   - 检查服务器是否运行
   - 检查网络连接

2. **Token 过期**
   - 自动刷新机制
   - 跳转到登录页面

3. **构建失败**
   - 检查 TypeScript 错误
   - 检查依赖版本
   - 清理 `.next` 目录重新构建

详细故障排查请参考: [开发指南文档](./development-guide.md)

