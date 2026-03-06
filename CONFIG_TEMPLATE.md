# 配置文件说明

本项目的配置文件已做脱敏处理，请根据实际环境修改以下配置项。

## 后端配置 (backend/src/main/resources/application-dev.yml)

### 数据库配置
```yaml
spring:
  datasource:
    url: jdbc:mysql://your_mysql_host:3306/Api_project
    username: your_mysql_username
    password: your_mysql_password
```

### Redis 配置
```yaml
spring:
  data:
    redis:
      host: your_redis_host
      port: 6379
      password: your_redis_password
```

### RabbitMQ 配置
```yaml
spring:
  rabbitmq:
    addresses: your_rabbitmq_host
    username: your_rabbitmq_username
    password: your_rabbitmq_password
```

### 邮件配置
```yaml
spring:
  mail:
    host: smtp.126.com
    username: your_email@126.com
    password: your_email_password  # 邮箱授权码，不是登录密码
```

### Nacos 配置
```yaml
spring:
  cloud:
    nacos:
      discovery:
        server-addr: your_nacos_host:8848
        username: nacos
        password: your_nacos_password
      config:
        server-addr: your_nacos_host:8848
        username: nacos
        password: your_nacos_password
```

### Dubbo 配置
```yaml
dubbo:
  registry:
    address: nacos://your_nacos_host:8848
    parameters:
      username: nacos
      password: your_nacos_password
```

### 阿里云 OSS 配置
```yaml
aliyun:
  oss:
    endpoint: oss-cn-guangzhou.aliyuncs.com
    accessKeyId: your_aliyun_access_key_id
    accessKeySecret: your_aliyun_access_key_secret
    bucketName: your_bucket_name
```

### 微信支付配置（可选）
```yaml
wx:
  pay:
    appId: 你的小程序ID
    mchId: 你的商户号
    apiV3Key: 你的APIv3密钥
    privateKeyPath: classpath:cert/apiclient_key.pem
    privateCertPath: classpath:cert/apiclient_cert.pem
    notifyUrl: https://你的域名/api/pay/wx/notify
```

## 网关配置 (gateway/src/main/resources/application.yml)

### Dubbo 配置
```yaml
dubbo:
  registry:
    address: nacos://your_nacos_host:8848
    parameters:
      username: nacos
      password: your_nacos_password
```

## 生产环境配置 (backend/src/main/resources/application-prod.yml)

生产环境配置与开发环境类似，请根据实际生产环境修改相应配置。

## 安全建议

1. **不要将真实配置提交到 Git**
   - 使用 `.gitignore` 忽略包含敏感信息的配置文件
   - 或使用环境变量/配置中心管理敏感信息

2. **使用 Nacos 配置中心**
   - 将敏感配置放在 Nacos 配置中心
   - 本地只保留 Nacos 连接信息

3. **定期更换密钥**
   - 定期更换数据库密码
   - 定期更换 API 密钥
   - 定期更换 JWT 密钥

4. **使用强密码**
   - 密码长度至少 12 位
   - 包含大小写字母、数字、特殊字符

## 快速配置脚本

可以创建一个本地配置文件 `application-local.yml`（已在 .gitignore 中），然后在启动时指定：

```bash
java -jar backend.jar --spring.profiles.active=local
```

