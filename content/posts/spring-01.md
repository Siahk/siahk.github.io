---
title: "Spring 01"
date: 2021-03-26T17:39:32+08:00
draft: false
---

# 1. Spring

Spring是一个开源的轻量级、非入侵式的控制反转（IOC）、面向切片编程（AOP）的免费框架。

## 1.1. Spring Boot 
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

# 2. IOC

## 2.1. Bean

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


# 3. 创建对象的方式

## 3.1. 下标赋值

```xml
<bean id="user" class="com.siahk.User">
    <constructor-arg index="0" value="qinjiang"/>
</bean>
```

## 3.2. 类型赋值（不建议）

```xml
<bean id="user" class="com.siahk.User">
    <constructor-arg index="java.lang.String" value="qinjiang"/>
</bean>
```

## 3.3. 参数名

```xml
<bean id="user" class="com.siahk.User">
    <constructor-arg name="name" value="qinjiang"/>
</bean>
```

- 总结：在配置文件加载时，容器中管理的对象就已经实例化了。

# 4. Spring的配置说明

## 4.1. 别名

```xml
<alias name="user" alias="user2"/>
```

## 4.2. bean 配置

```xml
<!--
    id: bean的唯一标识符。
    class: bean 对象所对应的全限定名：包名 + 类型。
    name: 别名,更高级，可以取多个别名。
-->
    <bean id="userT" class="com.siahk.pojo.UserT" name="userT2，userT3">
        <property name="name" value="西部开源"/>
    </bean>
```

## 4.3. import

一般用于团队开发使用，他可以将多个配置文件导入合并为一个。

```xml
    <import resource="beans2.xml"/>
    <import resource="beans.xml"/>
    <import resource="beans3.xml"/>
```

# 5. 依赖注入

## 5.1. 构造器注入

## 5.2. Set方式注入 【重点】

## 5.3. 扩展方式注入