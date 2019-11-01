# eureka设置用户密码登录

## 一. 注册中心

注册中心pom.xml增加

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

注册中心application.yml

```yml
spring:
    security:
        user:
            name: admin
            password: system
```

## 二. 其他服务使用

```yml
eureka:
    client:
        serviceUrl:
            defaultZone: http://admin:system@127.0.0.1:9001/registry-server/eureka/
```
