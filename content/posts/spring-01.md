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

一般用于团  队开发使用，他可以将多个配置文件导入合并为一个。

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

# 6. Bean'Scope

| Scope | Description |
| :---: | ----------- |
|singleton|(Default) Scopes a single bean definition to a single object instance for each Spring IoC container.|
|prototype|Scopes a single bean definition to any number of object instances.
|request|Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring ApplicationContext.|
|session|Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`.|
|application|Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`.|
|websocket|Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`.|
|||

## 6.1. Bean singleton mode (单例模式：Spring默认机制)

```xml
<bean id="user2" class="com.siahk.pojo.User" c:age="18" c:name="kuangshen" scope="singleton"/>
```


## 6.2. Bean prototype mode （原型模式：每次获取的都是新对象）  
```xml
<bean id="user3" class="com.siahk.pojo.User" c:age="18" c:name="kuangshen" scope="prototype"/>
```


## 6.3. 其余：request、session、application，只在web开发中使用


# 7. Bean 的自动装配

+ 自动装配是Spring满足Bean依赖一种方式。
+ Spring会在上下文中自动寻找，并自动给bean装配属性。

1. xml中显示的配置

2. java中显示配置

3. 隐式的自动装配bean【重要】

## 7.1. 测试

环境搭建：一个人有两个宠物

## 7.2. 自动装配：byName

```xml
    <bean id="cat" class="com.siahk.pojo.Cat"/>
    <bean id="dog" class="com.siahk.pojo.Dog"/>
<!--
    byName: 会自动在容器的上下文中查找和自己对象set方法后面的值对应的 beanid。
-->
    <bean id="people" class="com.siahk.pojo.People" autowire="byName">
<!--        <property name="cat" ref="cat"/>-->
<!--        <property name="dog" ref="dog"/>-->
        <property name="name" value="kuangshen"/>
    </bean>
```

## 7.3. 自动装配：byType

```xml
    <bean class="com.siahk.pojo.Cat"/>
    <bean class="com.siahk.pojo.Dog"/>
<!--
    byType: 会自动在容器的上下文中查找和自己对象属性类型相同的 beanid。
-->
    <bean id="people" class="com.siahk.pojo.People" autowire="byType">
        <property name="name" value="kuangshen"/>
    </bean>
```

## 小结
+ byName：需要保证id唯一，bean的id需要于set方法的值唯一。
+ byType：保证类型唯一，bean的属性的类型一致。

***

## 7.4. 使用注解实现自动装配

使用注解须知：
 1. 导入约束：context约束
 2.  == 配置注解的支持：`<context:annotation-config/>` ==  重要 ==
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

**@Autowired**
 1. 直接在属性上使用，也可以在set方式上使用。
 2. 使用Autowired可以不编写Set方法，前提是自动装配的属性在IOC容器中存在，且符合名字byName。

如果 **@Autowired** 自动装配的环境比较复杂，可以通过 **@Qualifier(value="ex")** 配合使用，指定一个唯一一个bean对象注入。

**@Resource**

小结： **@Autowired** 和 **@Resource** 的区别
+ 用于自动装配，放在属性段上。
+ **@Autowired** 通过byname的方式实现； **@Resource** 通过byName的方式实现，如果找不到则通过byType实现，两个都找不到的情况下，就报错。
+ 执行顺序不同： **@Autowired** 通过类型、名字； **@Resource** 通过名字、类型。

# 8. 注解开发

使用Spring的注解开发，必须保证Aop的包导入。

使用注解需要导入context约束，增加注解的支持。

代码：
```xml
 <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

## 8.1. bean

```java
//等价于 <bean id="user" class="com.siahk.dao.User"/>
//Component: 组件

@Component
public class User {
    public String name = "qinjiang";
}
```

## 8.2. 属性如何注入

```java
public class User {

    // 相当于 <property name="name" value="kuangshen"/>
    @Value("kuangshen")
    public String name;
}
```

## 8.3. 衍生的注解

**@Component** 有几个衍生注解，在web开发中，会按照mvc三层构架分层。
+ dao: **@Repository**
+ service: **@Service**
+ controller: **@Controller**

功能一样，将类注册到容器中，装配 Bean

## 8.4. 自动装配

- **@Autowired**：自动装配通过类型、名字；如果 **@Autowired** 自动装配的环境比较复杂，可以通过 **@Qualifier(value="ex")** 配合使用，指定一个唯一一个bean对象注入。
- **@Nullable**：字段标记了这个注解，说明这个字段可以为Null。
- **@Resource**：自动装配通过名字、类型。

## 8.5. 作用域

```java
@Component
@Scope("singleton")
public class User {

    // 相当于 <property name="name" value="kuangshen"/>
    @Value("kuangshen")
    public String name;
}
```

## 8.6. 小结

+ xml 和 注解
    + xml更加万能，适用于任何场景，维护简单方便。
    + 注解不是自己的类使用不了，维护相对复杂。
+ xml 与 注解 的最佳实践
    + xml 用来管理bean；
    + 注解只负责完成属性的注入；
    + 使用过程中需要注意一个问题：让注解生效，就需要开启注解的支持。

```xml
<!--    指定要扫描的包，这个包下的注解就会生效。-->
    <context:component-scan base-package="com.siahk.pojo"/>
    <context:annotation-config/>
```

# 9. 使用Java的方式配置Spring
不使用Spring的xml配置，全权交给java做。


实体类：
```java
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("Qinjiang") //属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}

```
测试类：
```java
public class MyTest {
    public static void main(String[] args) {

        // 如果完全使用了配置类方式去做，就只能通过 AnnotationConfig 上下文来获取容器，通过配置类的class对象加载。
        ApplicationContext context = new AnnotationConfigApplicationContext(SiahkConfig.class);

        User getUser = context.getBean("user", User.class);
        System.out.println(getUser.getName());
    }
}
```
配置文件：
```java
// 这个也会被Spring容器托管，注册到容器中，因为它也是一个@Component。
// @Configuration 代表这是一个配置类，和bean.xml是一样的。
@Configuration
@Component("com.siahk.pojo")
@Import(com.siahk.pojo.config.SiahkConfig2.class)
public class SiahkConfig {

    // 注册一个Bean就相当于之前写的一个Bean标签。
    // 这个方法的名字就相当于bean标签中的id属性。
    // 这个方法的返回值，就相当于bean标签中的class属性。
    @Bean
    public User user(){
        return new User(); // 就是返回要注入到bean的对象。
    }
}
```
这种纯Java的配置方式，在Springboot中随处可见。

# 10. 代理模式

## 10.1 静态代理

角色分析：
- 抽象角色：一般会使用接口或抽象类解决。
- 真实角色：被代理的角色。
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作。
- 客户：访问代理对象的人。

代码步骤（租房例子）：
1. 接口
```java
public interface Rent {
    public void rent();
}
```
2. 真实角色
```java
public class Host implements Rent {
    public void rent(){
        System.out.println("房东要出租房子");
    }
}
```
3. 代理角色
```java
public class Proxy implements Rent {
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    @Override
    public void rent() {
        host.rent();
        System.out.println("中介出租房子");
    }

    // 看房
    public void seeHouse() {
        System.out.println("Proxy seeHouse.");
    }

    // 合同
    public void hetong() {
        System.out.println("hetong");
    }

    // 中介费
    public void fare(){
        System.out.println("fare");
    }
}
```
4. 客户端访问代理角色
```java
public class Client {
    public static void main(String[] args) {
        Host host = new Host();
        // 代理,中介帮房东出租房子，但是代理角色一般会有一些附属操作。
        Proxy proxy = new Proxy(host);

        // 不用面对房东，直接找中介租房即可。
        proxy.rent();
    }
}
```

代理模式的好处：
- 可以使真实角色的操作更加存粹，不用去关注一些公共业务。
- 公共业务就交给代理角色，实现了业务的分工。
- 公共业务发生扩展的时候，方便集中管理。

缺点：
- 一个真实角色就会产生一个代理角色；代码量会翻倍，开发效率会变低。

## 10.2 加深理解

## 10.3 动态代理

- 动态代理静态代理角色一样，
- 动态代理的代理类是动态生成的，不是我们直接写好的。
- 动态代理也分为两大类：基于接口的动态代理；基于类的动态代理。
    - 基于接口--JDK 动态代理
    - 基于类： cglib
    - java字节码实现：javasist

需要了解两个类：Proxy（代理），InvocationHandler（调用处理程序）

动态代理的好处：
- 可以使真实角色的操作更加存粹，不用去关注一些公共业务。
- 公共业务就交给代理角色，实现了业务的分工。
- 公共业务发生扩展的时候，方便集中管理。
- 一个动态代理类代理的是一个接口，一般就是对应一类业务。
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可。

# 11. AOP

 
## 11.2. Aop在Spring中的作用
提供声明式事务：允许用户自定义切面
- 横切关注点：跨越应用程序多个模块的方法和功能，即是，与我们的业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。入日志，安全，缓存，事务等等……
- 切面（ASPECT）：横切关注点被模块化的特殊对象。即，它是一个类。
- 通知（Advice）：切面必须要完成的工作，即，它是类中的一个方法。
- 目标（Target）：被通知对象。
- 代理（Proxy）：向目标对象应用通知之后创建的对象。
- 切入点（PointCut）：切面通知执行的“地点”的定义。
- 连接点（JoinPoint）：与切入点匹配的执行点。

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5中类型的Advice：
<br/>
| 通知类型 | 连接点 | 实现接口 |
| :---: | :---: | :---|
| 前置通知 | 方法前 | org.springframework.aop.MethodBeforeAdvice |
| 后置通知 | 方法后 | org.springframework.aop.AfterReturningAdvice |
| 环绕通知 | 方法前后 | org.aopalliance.intercept.MethodInterceptor |
| 异常抛出通知 | 方法抛出异常 | org.springframework.aop.ThrowsAdvice |
| 引介通知 | 类中增加新的方法属性 | org.springframework.aop.IntroductionInterceptor |

即 Aop 在不改变原有代码的情况下，去增加新的功能。

## 11.3. 使用Spring实现Aop
【重点】使用AOP植入，需要导入一个依赖包。
```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</artifactId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

方式一：使用Spring的API接口【主要SpringAPI接口实现】

方式二：自定义来实现AOP【主要是切面定义】

方式三：使用注解实现

# 12. 整合Mybatis
步骤：
1. 导入相关jar包
    - junit
    - mybatis
    - mysql数据库
    - spring相关的
    - aop织入
    - mybtis-spring
2. 编写配置文件
3. 测试

## 12.1. 回忆mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试

## 12.2. 


# 13. 声明式事务

## 13.1. 回顾事务

- 把一组业务当成一个业务来做；要么都成功，要么都失败。
- 事务在项目开发中，十分的重要，涉及到数据的一致性问题，不能马虎。
- 确保完整性和一致性。

事务ACID原则：

- 原子性
- 一致性
- 隔离性
    - 多个可能操作同一个资源，防止数据损坏。
- 持久性
    - 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写道存储器中。

## 13.2. 