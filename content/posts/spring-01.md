---
title: "Spring 01"
date: 2021-03-26T17:39:32+08:00
draft: true
---

# 1.Spring

Spring是一个开源的轻量级、非入侵式的控制反转（IOC）、面向切片编程（AOP）的免费框架。

### 1.1 Spring Boot 
快速开发的脚手架。
基于SpringBoot可以快速的开发单个微服务。
约定大于配置。

弊端：发展太久，配置十分繁琐

导入包：
```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.5</version>
</dependency>
```
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.5</version>
</dependency>
```

# 2.IOC

## Bean

```xml
<!-- 
    bean: 创建对象
    ref: 应用Spring容器中创建好的对象
    value: 具体的值，基本数据类型
-->
<bean id="hello" class="com.siahk.pojo.Hello">
    <property name="name" value="spring" />
</bean>
<>
```


## 创建对象的方式