# Spring Cloud

## Spring Boot 与 Spring Cloud 选型

参考Spring Cloud官网：https://spring.io/projects/spring-cloud#learn

| 服务注册中心 | 服务调用      | 服务降级      | 服务网关   | 服务配置  | 服务总线  |
| ------------ | ------------- | ------------- | ---------- | --------- | --------- |
| Eureka(停更) | Ribbon(停更)  | Hystrix(停更) | Zuul(停更) | Config    | Bus       |
| zookeeper    | LoadBalancer  | resilience4j  | Zuul2      | **Nacos** | **Nacos** |
| consul       | Feign(停更)   | sentinel      | gateway    |           |           |
| **Nacos**    | **OpenFeign** |               |            |           |           |

