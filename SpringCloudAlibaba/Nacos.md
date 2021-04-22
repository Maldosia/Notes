# Nacos

# 1. 简介

Nacos(Naming Configuration Service的缩写)是一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

## 1.1比较

| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度      |
| ------------------ | ------- | ---------- | --------------- |
| Eureka             | AP      | 支持       | 低(2.X版本闭源) |
| Zookeeper          | CP      | 不支持     | 中              |
| Consul             | CP      | 不支持     | 高              |
| Nacos              | AP      | 支持       | 高              |

# 2. 搭建

# 3. 使用

```properties
#bootstrap.properties
#spring.application.name=mall-coupon
#spring.cloud.nacos.config.server-addr=127.0.0.1:8848
#spring.cloud.nacos.config.namespace=c99c0922-2c08-4ee5-886f-f16f03a2a317
#spring.cloud.nacos.config.group=prod
#
#spring.cloud.nacos.config.ext-config[0].data-id=datasource.yml
#spring.cloud.nacos.config.ext-config[0].group=dev
#spring.cloud.nacos.config.ext-config[0].refresh=true
#
#spring.cloud.nacos.config.ext-config[1].data-id=mybatis.yml
#spring.cloud.nacos.config.ext-config[1].group=dev
#spring.cloud.nacos.config.ext-config[1].refresh=true
#
#spring.cloud.nacos.config.ext-config[2].data-id=other.yml
#spring.cloud.nacos.config.ext-config[2].group=dev
#spring.cloud.nacos.config.ext-config[2].refresh=true
```

