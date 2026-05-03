# 🎓 LM-University — 高校招生智能服务平台

> 一个面向高校招生场景的全栈信息管理系统，支持学校/专业管理、学生报名、录取追踪、AI 智能咨询等功能。  
> 技术栈：Spring Boot 3 + Vue 3 + TypeScript + MyBatis-Plus + MySQL。

[![Java](https://img.shields.io/badge/Java-17-blue)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.2-green)](https://spring.io/projects/spring-boot)
[![Vue](https://img.shields.io/badge/Vue-3.4-42b883)](https://vuejs.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow)](./LICENSE)

---

## 📖 目录

- [项目简介](#项目简介)
- [系统架构](#系统架构)
- [功能模块](#功能模块)
- [技术栈](#技术栈)
- [快速开始](#快速开始)
- [项目结构](#项目结构)
- [AI 大模型配置（以 DeepSeek 为例）](#ai-大模型配置)
- [数据库说明](#数据库说明)
- [API 文档](#api-文档)
- [部署](#部署)
- [贡献指南](#贡献指南)
- [许可证](#许可证)

---

## 项目简介

**LM-University** 是一套完整的高校招生服务系统，面向**管理员**和**学生**两类用户：

- **管理员**：通过后台管理端维护学校/专业数据、发布公告、处理报名与录取、配置 AI 大模型等。
- **学生**：通过学生门户浏览招生信息、在线报名、查看录取结果，并使用 AI 助手进行智能咨询。

系统后端采用 **分层架构 + DDD 模块化** 设计，对外提供 RESTful API，前端基于 **Vue 3 + Element Plus** 构建，前后端完全分离。

---
```
## 系统架构
┌─────────────────────────────────────────────────────────────┐
│                        前端应用                              │
│  ┌──────────────────┐         ┌──────────────────┐          │
│  │  admin‑web       │         │  portal‑web       │          │
│  │  (管理员后台)    │         │  (学生门户)       │          │
│  └────────┬─────────┘         └────────┬─────────┘          │
│           │                  ▲          │                   │
│           └──────────────────┼──────────┘                   │
│                              │                              │
├──────────────────────────────┼──────────────────────────────┤
│                     RESTful API (/api/v1/*)                 │
├──────────────────────────────┼──────────────────────────────┤
│                        后端服务 (Spring Boot 3)              │
│  ┌──────┐ ┌──────────┐ ┌──────┐ ┌───────┐ ┌──────┐        │
│  │ auth │ │ admission │ │  ai  │ │content│ │ file │        │
│  ├──────┤ ├──────────┤ ├──────┤ ├───────┤ ├──────┤        │
│  │application│interaction│account│common│  legacy │        │
│  └──────┘ └──────────┘ └──────┘ └───────┘ └──────┘        │
│           │                                                │
│           └─────────────────┬──────────────────────────────│
│                             │ MySQL                        │
└─────────────────────────────┴──────────────────────────────┘
```

- **后端**：Spring Boot 3 + MyBatis-Plus，按业务域拆分为 `auth`（认证）、`admission`（招生）、`ai`（智能咨询）、`content`（内容管理）等模块，符合 DDD 思想。
- **前端**：两个独立 Vue 3 项目，使用 Vite 构建、Pinia 状态管理、Vue Router 路由。
- **数据库**：MySQL，表结构来自 `sql/university.sql`（核心表）以及 `docs/ai-chat-tables.sql`（AI 聊天相关表）。
- **基础设施**：Docker Compose 位于 `infra/compose.yml`，可快速拉起开发环境。

---

## 功能模块

### 管理员端（`apps/admin-web`）

| 模块 | 说明 |
|------|------|
| 省份 / 学校 / 专业管理 | 基础数据维护 |
| 公告与资讯管理 | 发布招生动态 |
| 报名审核 | 管理学生报名记录 |
| 录取管理 | 发布录取结果 |
| 成绩管理 | 录入/查询学生成绩 |
| 咨询回复 | 管理咨询留言 |
| AI 配置 | 大模型接入（支持 DeepSeek 等） |
| 系统设置 | 站点信息、关于我们等 |

### 学生端（`apps/portal-web`）

| 模块 | 说明 |
|------|------|
| 招生信息浏览 | 查看学校、专业、公告 |
| 在线报名 | 提交报名申请 |
| 录取结果查询 | 查看录取状态 |
| 成绩查询 | 查看个人成绩 |
| 收藏 | 收藏感兴趣的学校与专业 |
| AI 智能助手 | 基于大模型的招生咨询（右下角悬浮按钮） |

---

## 技术栈

| 层级 | 技术 |
|------|------|
| 后端框架 | Spring Boot 3.3.2 |
| ORM | MyBatis-Plus 3.5.7 |
| 安全 | Spring Security + JWT（jjwt 0.12.6） |
| 数据库 | MySQL 8.x |
| 连接池 | HikariCP（Spring Boot 默认） |
| API 文档 | SpringDoc OpenAPI 2.6.0 |
| 前端框架 | Vue 3.4 + TypeScript |
| 构建工具 | Vite 5 |
| 状态管理 | Pinia 2 |
| UI 框架 | Element Plus 2.7（仅 admin-web） |
| AI 集成 | 通用 HTTP SSE 流式协议（支持 DeepSeek 等） |
| 容器化 | Docker Compose |

---

## 快速开始

### 环境要求

- **JDK 17+**
- **Maven 3.8+**
- **Node.js 18+** / npm
- **MySQL 8.0+**

### 1. 克隆仓库

```bash
git clone https://github.com/lsyaizyl/LM-University.git
cd LM-University
```

### 2. 数据库初始化

执行以下 SQL 文件：

```bash
# 核心表结构
mysql -u root -p < sql/university.sql

# AI 聊天相关表
mysql -u root -p < docs/ai-chat-tables.sql

# 学生成绩字段（如有需要）
mysql -u root -p < docs/student-score-field.sql
```

### 3. 后端配置

在 `backend/src/main/resources/application.properties`（或通过环境变量）配置数据库连接：

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/university?useUnicode=true&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=your_password
```

### 4. 启动后端

**Windows（双击）**：
```cmd
run-backend.cmd
```

**macOS / Linux**：
```bash
./mvnw spring-boot:run
```

后端默认运行在 `http://localhost:8080`。

### 5. 启动前端

#### 管理后台（admin-web）

**Windows（双击）**：
```cmd
run-admin-web.cmd
```

**npm**：
```bash
cd apps/admin-web
cp .env.example .env
npm install
npm run dev
```

管理后台默认运行在 `http://localhost:5173`。

#### 学生门户（portal-web）

**Windows（双击）**：
```cmd
run-portal-web.cmd
```

**npm**：
```bash
cd apps/portal-web
cp .env.example .env
npm install
npm run dev
```

学生门户默认运行在 `http://localhost:5174`。

### 6. （可选）使用 Docker Compose

```bash
cd infra
cp .env.example .env
docker-compose -f compose.yml up -d
```

---

## 项目结构

```
LM-University/
├── apps/                        # 前端应用
│   ├── admin-web/               # Vue 3 管理后台 (Element Plus)
│   │   ├── src/
│   │   │   ├── api/             # 接口层
│   │   │   ├── components/      # 公共组件
│   │   │   ├── router/          # 路由配置
│   │   │   ├── stores/          # Pinia 状态管理
│   │   │   ├── types/           # TypeScript 类型定义
│   │   │   ├── views/           # 页面视图
│   │   │   ├── App.vue
│   │   │   └── main.ts
│   │   ├── package.json
│   │   ├── vite.config.ts
│   │   └── .env.example
│   └── portal-web/              # Vue 3 学生门户
│       └── ...（结构同 admin-web）
├── backend/                     # Spring Boot 后端
│   ├── src/main/java/com/university/backend/
│   │   ├── account/             # 账户体系 (Account, Role)
│   │   ├── admission/           # 招生录取
│   │   ├── ai/                  # AI 大模型集成 (DeepSeek 等)
│   │   ├── application/         # 报名管理
│   │   ├── auth/                # 认证与授权 (JWT)
│   │   ├── common/              # 公共组件 (API 响应、安全、配置)
│   │   ├── content/             # 内容管理 (公告、资讯、关于我们)
│   │   ├── file/                # 文件上传
│   │   ├── interaction/         # 互动 (收藏、咨询)
│   │   └── legacy/              # 遗留系统兼容层
│   ├── src/test/                # 测试用例
│   └── pom.xml
├── docs/                        # 项目文档
│   ├── ai-model-config-guide.md # AI 大模型配置教程
│   ├── data-model-and-migration.md # 数据模型说明
│   ├── ai-chat-tables.sql       # AI 聊天表结构
│   └── student-score-field.sql  # 成绩字段扩展
├── infra/                       # 基础设施
│   ├── compose.yml              # Docker Compose
│   ├── .env.example
│   └── sql/                     # 数据质量报告
├── sql/                         # 数据库初始化脚本
│   └── university.sql
├── upload/                      # 文件上传目录
├── pom.xml                      # Maven 父 POM
├── mvnw / mvnw.cmd              # Maven Wrapper
└── run-*.cmd                    # Windows 一键启动脚本
```

---

## AI 大模型配置

系统通过**通用 HTTP API 模板**方式接入大模型，不绑定特定厂商，目前支持 **DeepSeek**，其他厂商（OpenAI、阿里百炼等）通过调整请求模板即可接入。

### 快速配置步骤

1. **执行建表脚本**：
   ```sql
   source docs/ai-chat-tables.sql;
   ```

2. **进入后台 → AI 配置页面**，填写以下关键项：

   | 配置项 | 示例值 |
   |--------|--------|
   | 接口地址 | `https://api.deepseek.com/chat/completions` |
   | API Key | 你的 DeepSeek API Key |
   | 模型 | `deepseek-v4-flash` |
   | 流式协议 | SSE |
   | 文本路径 | `choices.0.delta.content` |
   | 结束标记 | `[DONE]` |

3. **点击“测试连接”**，成功后启用 AI 功能。

4. **学生端验证**：登录学生账号，点击右下角 AI 悬浮按钮进行提问。

> 📖 详细配置教程请参阅：[docs/ai-model-config-guide.md](docs/ai-model-config-guide.md)

### 安全设计

- API Key **不会回显明文**，后台仅返回 `apiKeySet: true/false`。
- 学生 AI 上下文**仅包含当前学生本人的数据**，不会泄露其他学生信息。
- 学生聊天接口不接收前端传入的 `studentId`，由后端从 JWT Token 中解析。

---

## 数据库说明

### 核心表

| 表名 | 说明 |
|------|------|
| `users` | 管理员账号 |
| `student` | 学生账号及档案 |
| `province` | 省份数据 |
| `universityinformation` | 高校信息 |
| `professionalinformation` | 专业信息 |
| `collegeapplication` | 大学报名 |
| `professionalregistration` | 专业报名 |
| `admissionresults` | 录取结果 |
| `resultsinformation` | 成绩信息 |
| `news` | 新闻公告 |
| `aboutus` | 关于我们 |
| `systemintro` | 系统介绍 |
| `config` | 应用配置 |
| `chat` | 咨询留言 |
| `storeup` | 收藏 |
| `ai_model_config` | AI 大模型配置 |
| `ai_chat_conversation` | AI 对话会话 |
| `ai_chat_message` | AI 对话消息 |

> 📖 完整数据模型说明请参阅：[docs/data-model-and-migration.md](docs/data-model-and-migration.md)

---

## API 文档

启动后端后，访问 Swagger UI：

```
http://localhost:8080/swagger-ui.html
```

所有 API 统一前缀：`/api/v1/*`

---

## 部署

### 后端部署

```bash
cd backend
./mvnw clean package -DskipTests
java -jar target/backend-1.0.0-SNAPSHOT.jar
```

### 前端部署

```bash
# 管理后台
cd apps/admin-web
npm run build       # 产出到 dist/

# 学生门户
cd apps/portal-web
npm run build       # 产出到 dist/
```

将 `dist/` 目录部署到 Nginx 或其他 Web 服务器即可。

### 环境变量

| 变量 | 说明 |
|------|------|
| `DB_URL` | 数据库连接 URL |
| `DB_USERNAME` | 数据库用户名 |
| `DB_PASSWORD` | 数据库密码 |
| `JWT_SECRET` | JWT 签名密钥（生产环境务必修改） |

---

## 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 新建功能分支：`git checkout -b feature/amazing-feature`
3. 提交代码：`git commit -m 'feat: add amazing feature'`
4. 推送分支：`git push origin feature/amazing-feature`
5. 提交 Pull Request

---

## 许可证

本项目采用 [MIT License](./LICENSE) 开源。

---

<p align="center">
  <b>LM-University</b> — 让高校招生更智能、更高效 🚀
</p>
