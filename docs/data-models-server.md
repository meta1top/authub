# Data Models - Server

## Overview

This document describes the database schema and data models used by the Authub server.

**ORM:** TypeORM  
**Database:** MySQL  
**Naming Strategy:** Snake Case (SnakeNamingStrategy)

## Entities

### Account

**Table:** `account`

用户账号表

| Column | Type | Description | Constraints |
|--------|------|-------------|-------------|
| id | varchar(20) | 账号ID | Primary Key, Snowflake ID |
| email | varchar(255) | 邮箱 | Unique, Not Null |
| username | varchar(32) | 用户名 | Not Null |
| password | varchar(255) | 密码（加密） | Not Null, Select: false |
| avatar | varchar(255) | 头像URL | Nullable |
| createTime | datetime | 创建时间 | Default: CURRENT_TIMESTAMP |
| lastTime | datetime | 最后活动时间 | Nullable |
| deleted | boolean | 是否已删除 | Default: false, Select: false |
| enable | boolean | 是否启用 | Default: true |
| otpSecret | varchar(255) | OTP密钥 | Nullable, Select: false |
| otpStatus | int | OTP绑定状态 (0=未绑定, 1=已绑定) | Default: 0 |
| otpEnableTime | datetime | OTP生效时间 | Nullable |
| joinAppId | varchar(20) | 加入应用ID | Nullable |

**Relationships:**
- One-to-Many with `AccountToken` (via accountId)
- Many-to-Many with `App` (via `AppAccount`)

---

### AccountToken

**Table:** `account_token`

账号令牌表（用于多应用场景）

| Column | Type | Description | Constraints |
|--------|------|-------------|-------------|
| id | varchar(20) | 令牌ID | Primary Key, Snowflake ID |
| accountId | varchar(20) | 账号ID | Not Null |
| appId | varchar(20) | 应用ID | Not Null |
| refreshToken | varchar(255) | 刷新令牌 | Nullable |
| accessToken | varchar(255) | 访问令牌 | Nullable |
| createTime | datetime | 创建时间 | Default: CURRENT_TIMESTAMP |
| updateTime | datetime | 更新时间 | Default: CURRENT_TIMESTAMP |

**Relationships:**
- Many-to-One with `Account` (via accountId)
- Many-to-One with `App` (via appId)

---

### App

**Table:** `app`

应用表

| Column | Type | Description | Constraints |
|--------|------|-------------|-------------|
| id | varchar(20) | 应用ID | Primary Key, Snowflake ID |
| name | varchar(255) | 应用名称 | Not Null |
| appKey | varchar(32) | 唯一标识 | Not Null |
| memo | varchar(255) | 说明 | Nullable |
| createTime | datetime | 创建时间 | Default: CURRENT_TIMESTAMP |
| enable | tinyint(1) | 是否启用 | Default: true |
| deleted | tinyint(1) | 是否已删除 | Default: false, Select: false |
| ownerId | varchar(20) | 创建人 | Not Null |
| logo | varchar(255) | LOGO URL | Nullable |
| callbackUrl | varchar(255) | 回调地址 | Nullable |
| homepage | varchar(255) | 首页地址 | Nullable |

**Relationships:**
- Many-to-Many with `Account` (via `AppAccount`)

---

### AppAccount

**Table:** `app_account`

应用账号关联表（多对多关系）

| Column | Type | Description | Constraints |
|--------|------|-------------|-------------|
| id | varchar(20) | 关联ID | Primary Key, Snowflake ID |
| appId | varchar(20) | 应用ID | Not Null |
| accountId | varchar(20) | 账号ID | Not Null |
| joinTime | datetime | 注册时间 | Default: CURRENT_TIMESTAMP |
| role | int | 角色 (1=管理员, 2=成员) | Default: 2 |

**Relationships:**
- Many-to-One with `App` (via appId)
- Many-to-One with `Account` (via accountId)

---

## ID Generation

All entities use **Snowflake ID** generation strategy for distributed ID generation.

## Naming Conventions

- **Table Names:** Snake case (e.g., `account_token`)
- **Column Names:** Snake case (e.g., `create_time`)
- **Entity Classes:** PascalCase (e.g., `AccountToken`)

## Indexes

- `account.email` - Unique index
- Foreign key relationships via TypeORM decorators

