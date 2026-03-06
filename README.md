# Bonelight API 开放平台

> 一个基于 Spring Cloud 微服务架构的 API 开放平台，提供 API 发布、管理、调用、计费等完整功能。

## 📋 项目简介

Bonelight API 是一个企业级 API 开放平台，类似于阿里云 API 市场。开发者可以在平台上发布自己的 API 接口，其他用户通过统一网关调用这些接口，平台提供鉴权、限流、计费、监控等完整的 API 管理能力。

### 核心功能

- 🔐 **统一鉴权** - AccessKey/SecretKey 签名认证机制
- 🚪 **API 网关** - 统一入口，请求路由与转发
- 📊 **调用统计** - 实时记录 API 调用次数和状态
- 💰 **计费系统** - 支持按次计费，集成微信支付
- 🔒 **限流保护** - 防止恶意调用，保护后端服务
- 📝 **API 文档** - Knife4j 自动生成接口文档
- 🔌 **客户端 SDK** - 简化第三方调用流程

---

## 🏗️ 技术架构

### 后端技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 3.2.3 | 核心框架 |
| Spring Cloud Gateway | 4.0.0 | API 网关 |
| Dubbo | 3.3.4 | RPC 框架 |
| Nacos | 2.5.1 | 服务注册与配置中心 |
| MyBatis-Plus | 3.5.9 | ORM 框架 |
| MySQL | 8.0.33 | 关系型数据库 |
| Redis | - | 缓存与会话存储 |
| RabbitMQ | - | 消息队列 |
| Spring Security | - | 安全框架 |
| JWT | 4.3.0 | Token 认证 |
| Knife4j | 4.5.0 | API 文档 |
| 阿里云 OSS | - | 文件存储 |

### 前端技术栈

- React 18 + TypeScript
- Ant Design 5.x (UI 组件库)
- Redux Toolkit (状态管理)
- React Router 7 (路由)
- Vite 6 (构建工具)
- ECharts / AntV G2 (数据可视化)

---

## 📦 项目结构

```
Bonelight-Api/
├── backend/          # 核心业务服务 (端口: 8081)
│   ├── controller/   # API 接口层
│   ├── service/      # 业务逻辑层
│   ├── DAO/          # 数据访问层
│   ├── config/       # 配置类 (Security, Redis, MybatisPlus 等)
│   ├── filter/       # 过滤器 (限流、日志)
│   ├── dubboImpl/    # Dubbo 服务实现
│   └── utils/        # 工具类 (OSS, 雪花ID, 签名等)
│
├── gateway/          # API 网关服务 (端口: 18000)
│   ├── filters/      # 网关过滤器链
│   │   ├── SecurityFilter.java       # 鉴权过滤器
│   │   ├── BusinessFilter.java       # 业务处理过滤器
│   │   ├── LoggingFilter.java        # 日志过滤器
│   │   └── RequestIdFilter.java      # 请求追踪
│   └── GatewayConfig.java            # 网关配置
│
├── clientSDK/        # 客户端 SDK
│   ├── client/       # SDK 客户端
│   └── utils/        # 签名工具
│
├── common/           # 公共模块
│   └── 共享实体类、工具类
│
├── front/            # 前端项目
│   ├── src/
│   │   ├── features/ # 功能模块 (auth, home, setting)
│   │   ├── store/    # Redux 状态管理
│   │   ├── routes/   # 路由配置
│   │   └── Api/      # API 请求封装
│   └── package.json
│
└── pom.xml           # Maven 父工程配置
```

---

## 🚀 快速开始

### 环境要求

- JDK 17+
- Maven 3.8+
- Node.js 16+
- MySQL 8.0+
- Redis 6.0+
- RabbitMQ 3.x
- Nacos 2.x

### 1. 克隆项目

```bash
git clone https://github.com/NoxLightman/Bonelight-Api.git
cd Bonelight-Api
```

### 2. 配置数据库

创建数据库并导入 SQL 脚本：

```sql
CREATE DATABASE Api_project CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 3. 修改配置文件

**重要：配置文件已做脱敏处理，请根据实际环境修改！**

详细配置说明请查看 [CONFIG_TEMPLATE.md](CONFIG_TEMPLATE.md)

编辑 `backend/src/main/resources/application-dev.yml`：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/Api_project
    username: root
    password: your_password

  data:
    redis:
      host: localhost
      port: 6379
      password: your_redis_password

  rabbitmq:
    addresses: localhost
    username: guest
    password: guest

  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
        username: nacos
        password: nacos
      config:
        server-addr: localhost:8848
        username: nacos
        password: nacos
```

同样修改 `gateway/src/main/resources/application.yml` 中的 Nacos 配置。


### 4. 启动 Nacos

```bash
# 下载 Nacos 后启动
sh startup.sh -m standalone
```

访问 Nacos 控制台：http://localhost:8848/nacos (默认账号密码: nacos/nacos)

### 5. 启动后端服务

```bash
# 启动 backend 服务
cd backend
mvn spring-boot:run

# 启动 gateway 服务
cd ../gateway
mvn spring-boot:run
```

### 6. 启动前端

```bash
cd front
npm install
npm run dev
```

访问前端：http://localhost:5173

---

## 🔑 核心流程

### API 调用流程

```
客户端 → Gateway (鉴权) → Backend (业务处理) → 目标 API
         ↓                    ↓
    签名验证              调用统计 & 扣费
    限流检查              Dubbo RPC
```

### 鉴权机制

1. 用户注册后获得 `accessKey` 和 `secretKey`
2. 调用 API 时，使用 `secretKey` 对请求参数签名
3. Gateway 的 `SecurityFilter` 验证签名有效性
4. 验证通过后转发到 Backend 处理

---

## 📝 API 文档

启动 Backend 服务后访问：

- Knife4j 文档：http://localhost:8081/doc.html
- 默认账号密码：`oilbone / 123123`

---

## ⚠️ 安全提醒

本项目配置文件已做脱敏处理，请勿将真实密钥提交到 Git。建议：

1. 使用环境变量或配置中心管理敏感信息
2. 定期更换数据库密码和 API 密钥
3. 生产环境使用强密码

---

## 📄 许可证

本项目仅供学习交流使用。

---

## 👨‍💻 作者

- GitHub: [@NoxLightman](https://github.com/NoxLightman)

---

## 🙏 致谢

感谢所有开源项目的贡献者！
