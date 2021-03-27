---
title: "Spring 01"
date: 2021-03-26T17:39:32+08:00
draft: false
tags: ["Spring","IOC"]
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

```construct-args```

## 5.2. Set方式注入 【重点】

- 依赖注入：Set注入
    + 依赖：Bean对象的创建依赖于容器
    + 注入：Bean对象中的所有属性，由容器来注入


```xml
    <bean id="address" class="com.siahk.pojo.Address">
        <property name="address" value="西安"/>
    </bean>

    <bean id="student" class="com.siahk.pojo.Student">
        <!--        第一种，普通值注入，value-->
        <property name="name" value="qinjiang"/>
        <!--        第二种，bean注入，ref-->
        <property name="address" ref="address"/>
        <!--        数组注入，ref-->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>水浒传</value>
                <value>三国演义</value>
            </array>
        </property>
        <!--        List-->
        <property name="hobbys">
            <list>
                <value>music</value>
                <value>movie</value>
                <value>picture</value>
            </list>
        </property>
        <!--        Map-->
        <property name="card">
            <map>
                <entry key="身份证" value="123434324234"/>
                <entry key="银行卡" value="23425234234234"/>
            </map>
        </property>
        <!--        Set-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>COC</value>
                <value>Dota</value>
            </set>
        </property>
        <!--        null-->
        <property name="wife">
            <null/>
        </property>
        <!--        Properties-->
        <property name="info">
            <props>
                <prop key="xuehao">31400029</prop>
                <prop key="sex">man</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
    </bean>
```

## 5.3. 扩展方式注入

### 5.3.1. p-nameworkspace

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- p命名空间注入，可以直接注入属性的值: property-->
    <bean id="user" class="com.siahk.pojo.User" p:name="qinjiang" p:age="18"/>
    <!-- c命名空间注入，通过构造器注入：construct-args-->
    <bean id="user2" class="com.siahk.pojo.User" c:age="18" c:name="kuangshen"/>


</beans>
```

### 5.3.2. c-nameworkspace

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- p命名空间注入，可以直接注入属性的值: property-->
    <bean id="user" class="com.siahk.pojo.User" p:name="qinjiang" p:age="18"/>
    <!-- c命名空间注入，通过构造器注入：construct-args-->
    <bean id="user2" class="com.siahk.pojo.User" c:age="18" c:name="kuangshen"/>


</beans>
```

### 测试

```java
    @Test
    public void test2() {
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user2", User.class);
        System.out.println(user);
    }
```

# 6. Bean 的作用域

## Bean 