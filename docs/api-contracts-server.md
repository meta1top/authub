# API Contracts - Server

## Overview

This document describes all API endpoints provided by the Authub server backend.

**Base URL:** `/api`

**Authentication:** Bearer Token (JWT) via `Authorization` header

**Language Support:** Accept-Language header (zh-CN, en)

## API Endpoints

### Account Management

#### `GET /api/account/profile`
获取用户信息

**Authentication:** Required

**Response:**
```typescript
{
  id: string;
  email: string;
  username: string;
  avatar: string | null;
  // ... other profile fields
}
```

---

#### `POST /api/account/register`
用户注册

**Authentication:** Public

**Request Body:**
```typescript
{
  email: string;
  username: string;
  password: string; // RSA encrypted
}
```

**Response:**
```typescript
{
  accessToken: string;
  refreshToken: string;
}
```

---

#### `POST /api/account/login`
用户登录

**Authentication:** Public

**Request Body:**
```typescript
{
  email: string;
  password: string; // RSA encrypted
}
```

**Response:**
```typescript
{
  accessToken: string;
  refreshToken: string;
}
```

---

#### `POST /api/account/logout`
用户登出

**Authentication:** Required

**Response:** Success message

---

### OTP (Two-Factor Authentication)

#### `GET /api/account/otp/status`
获取 OTP 状态

**Authentication:** Required

**Response:**
```typescript
{
  enabled: boolean;
  // ... other status fields
}
```

---

#### `GET /api/account/otp/secret`
获取 OTP 密钥

**Authentication:** Required

**Response:**
```typescript
{
  secret: string;
  qrCode: string;
}
```

---

#### `POST /api/account/otp/enable`
启用 OTP

**Authentication:** Required

**Request Body:**
```typescript
{
  code: string; // OTP verification code
}
```

---

#### `POST /api/account/otp/disable`
禁用 OTP

**Authentication:** Required

**Request Body:**
```typescript
{
  code: string; // OTP verification code
}
```

---

### Assets Management

#### `POST /api/assets/upload/pre-sign`
生成预签名上传 URL

**Authentication:** Public

**Request Body:**
```typescript
{
  fileName: string;
  fileType: string;
  // ... other upload parameters
}
```

**Response:**
```typescript
{
  uploadUrl: string;
  // ... other upload details
}
```

---

### Mail Service

#### `POST /api/mail/code/send`
发送验证码邮件

**Authentication:** Public

**Request Body:**
```typescript
{
  email: string;
  type: string; // code type
}
```

---

### App Management

#### `GET /api/config/common`
获取公共配置

**Authentication:** Public

**Response:**
```typescript
{
  // Common configuration
}
```

---

### Default

#### `GET /`
欢迎页面（测试国际化功能）

**Authentication:** Public

**Response:**
```typescript
{
  message: string;
  hello: string;
  currentLang: string;
}
```

---

## Error Responses

All endpoints may return standard error responses:

```typescript
{
  statusCode: number;
  message: string;
  error?: string;
}
```

## Swagger Documentation

Interactive API documentation available at: `/docs`

