---
title: "Springboot 01"
date: 2021-04-06T12:44:42+08:00
draft: false
---

# 微服务阶段

Javase：OOP

mysql：持续化

html+css+js+jquery+框架：视图，框架，框架不熟练，css不好；

javaweb：独立开发MVC三层架构的网站：原始

ssm：框架：简化了我们的开发流程

**war：tomcat 运行**

Spring再简化：Springboot-jar：内嵌tomcat； 微服务框架！

服务越来越多：springcloud；


# 第一个springboot程序

# 原理初探

自动配置：

**pom.xml**

- spring-boot-dependencies: 核心依赖在父工程中
- 我们在写或引入springboot依赖的时候，不需要指定版本，就是因为有这些仓库。

**启动器**

- ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    ```
- 启动器：就是springboot的启动场景；
- 例如spring-boot-starter-web，它就会自动导入web环境的所有依赖。
- springboot会将所有场景都变成一个个的启动器。
- 要使用什么功能，就只需要找到对应的启动器，`start`

**主程序**

```java
// @SpringBootApplication：标注这个类是一个spingboot应用。
@SpringBootApplication
public class Springboot01HelloworldApplication {

    public static void main(String[] args) {
        // 将spirngboot应用启动
        SpringApplication.run(Springboot01HelloworldApplication.class, args);
    }

}
```

- 注解
    -   ```java
        @SpringBootConfiguration // springboot的配置
            @Configuration // spring配置类
            @Component // 说明这也是一个spring的组件

        @EnableAutoConfiguration // 自动配置
            @AutoConfigurationPackage // 自动配置包
                @Import(AutoConfigurationPackages.Registrar.class) // 自动配置`包注册`
                @Import(AutoConfigurationImportSelector.class) // 自动配置导入选择
        
        // 获取所有的配置
        List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
        ```

获取候选的配置

```
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
	List<String> configurations = SpringFactoriesLoader.loadFactoryNames   (getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader());
	Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you " + "are using a custom packaging, make sure that file is correct.");
	return configurations;
}
```

META-INF/spring.factories：自动配置的核心文件。

结论：springboot所有自动配置都是在启动扫描并加载：spring.factories所有的自动配置类都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的start，就有对应的启动器了，有了启动器，我们自动装配就会生效，然后就配置成功。

1. springboot在启动的时候，从类路径下/META-INF/spring.factories 获取指定的值。
2. 将这些自动配置的类导入容器，自动配置就会生效，进行完成自动配置。
3. 以前我们需要自动配置的东西，现在spring帮我们做了。
4. 整合javaEE，解决方案和自动配置的东西都在spring-boot-autoconfigure这个包下。
5. 他会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加到容器中。
6. 容器中也会存在非常多的xxxAutoConfiguration的文件（@Bean），就是这些类给容器中导入了这个场景需要的组件，并自动配置，@Configuration， JavaConfig。
7. 有了自动配置类，免去了我们手动编写配置文件的工作。

# Springboot 配置

yaml 可以直接给实体类赋值

# SpringBoot Web 开发

jar：webapp

自动装配

springboot到底帮我们配置了什么东西，我们能不能修改？能修改那些东西？能不能扩展？

- xxxAutoConfiguration...向容器中自动配置组件
- xxxProperties：自动配置类，装配配置文件中自定义的一些内容。

要解决的问题：

- 导入静态资源。
- 首页。
- jsp，模板引擎。
- 装配扩展springmvc
- 增删改查。
- 拦截器
- 国际化

## 静态资源

```java
        @Override
		protected void addResourceHandlers(ResourceHandlerRegistry registry) {
			super.addResourceHandlers(registry);

			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}

			ServletContext servletContext = getServletContext();
			addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
			addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
				registration.addResourceLocations(this.resourceProperties.getStaticLocations());
				if (servletContext != null) {
					registration.addResourceLocations(new ServletContextResource(servletContext, SERVLET_LOCATION));
				}
			});
		}
```

总结：

1. 在springboot中，我们可以使用以下方式处理静态资源
    - webjars    `localhost:8080/webjars/`
    - public、staict、resources、/**    `localhost:8080/`
    - 优先级： resources>static(default)>public 

## 首页如何定制

## 模板引擎

结论：需要使用thymeleaf，只需要导入对应的依赖就可以了；我们将html放到我们的templates目录下即可。

```java
	public static final String DEFAULT_PREFIX = "classpath:/templates/";
	public static final String DEFAULT_SUFFIX = ".html";
```

thymeleaf的含义：

- Simple expressions:
    - Variable Expressions: ${...}
    - Selection Variable Expressions: *{...}
    - Message Expressions: #{...}
    - Link URL Expressions: @{...}
    - Fragment Expressions: ~{...}
 
1. 首页配置：注意点所有的静态资源都需要使用thymeleaf接管。@{}
2. 页面国际化
    1. 我们需要配置i18n文件。
    2. 如果我们需要在项目中进行按钮自动切换，我们需要自定义一个组件。`LocaleResolver`
    3. 记得将我们自己写的组件，配置到容器中。`@Bean`
    4. #{}
3. 登录+拦截器
4. 员工列表展示
    1. 提取公共页面
        1. `th:fragment="sidebar"`
        2. `th:replace="~{commons/commons::sidebar}"`
        3. 如果要传递参数，可以直接使用`()`传参，接收判断即可。
    2. 列表循环展示

# Mybatis

整合包

mybaits-spring-boot-starter

1. 导入包
2. 编写配置文件
3. mybatis 配置
4. 编写sql语句
5. service层调用DAO层
6. controller 调用service层


M: 数据和业务
V: HTML
C: Controller, 交接


# SpringSecurity （安全）

## 简介

记住几个类：

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启Security模式

Spring Security的两个主要目标是“认证”和“授权”（访问控制）。

“认证”（Authentication）

“授权”（Authorization）

这个两个概念是通用的，而不是只在Spring Security中存在

# Shiro

# 1. 什么是Shiro？

 - Apache Shiro 是一个Java的安全（权限）框架。
 - Shiro 可以非常容易的开发出足够好的应用，其不仅可以用在JAVASE环境，也可以用在JAVAEE环境
 - Shiro可以完成，认证，授权，加密，会话管理，Web集成，缓存等。
 - 下载地址 http://shiro.apache.org/

1. 导入依赖
2. 配置文件
3. HelloWorld


spring-security都有。

```java
Subeject currentUser = SecurityUtils.getSubject();
Session session = currentUser.getSession();
currentUser.isAuthenticated();
currentUser.getPrincipal();
currentUser.hasRole("schwartz");
currentUser.isPermistted("lightsaber:wield");
currentUser.logout();
```

Springboot中集成

# Swagger

## SpringBoot 集成 Swagger

1. 新建一个SpringBoot=web项目
2. 导入相关依赖
``` java
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>3.0.0</version>
</dependency>

```
3. 编写一个hello工程
4. 配置Swagger==>Config
```java
@Configuration
@EnableSwagger2 // 开启Swagger2
public class SwaggerConfig {
}
```
5. 测试运行

## 配置Swagger

Swagger的Bean实例Docket
```java
public class SwaggerConfig {
    // 配置了Swagger的docket的bean实例
    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }
    private ApiInfo apiInfo() {
        // 作者信息
        final Contact DEFAULT_CONTACT = new Contact("heng", "", "");
        return new ApiInfo(
                "heng's swagger api documentation",
                "Api Documentation",
                "1.0",
                "urn:tos",
                DEFAULT_CONTACT,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList<VendorExtension>());
    }
}
```

## Swagger配置扫描接口

Docket.select()

```java
    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                // RequestHandlerSelectors，配置要扫描接口的方式、
                // basePackage，指定要扫描的包
                // any()，扫描全部
                // none()，都不扫描
                // withClassAnnotation()，扫描类上的注解，参数是一个注解的反射对象
                // withMethodAnnotation()，扫描方法上的注解
                .apis(RequestHandlerSelectors.basePackage("com.heng.swagger.controller"))
                // paths()，过滤路径
                .paths(PathSelectors.ant("/heng/**"))
                .build();
    }
```

配置是否启动Swagger
```java
    // 配置了Swagger的docket的bean实例
    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(false) // enable：是否启动Swagger；如果为false，则Swagger不能在浏览器中访问。
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.heng.swagger.controller"))
                // .paths(PathSelectors.ant("/heng/**"))
                .build();
    }
```

swagger如何只在生产环境中使用，而发布时不使用

- 判断是否是生产环境 flags = false
- 注入enble(flags)

```java
    // 配置了Swagger的docket的bean实例
    @Bean
    public Docket docket(Environment environment) {
        // 设置要显示的Swagger环境
        Profiles profiles = Profiles.of("dev","test");
        // 通过environment.acceptsProfiles判断是否处在自己设定的环境当中
        boolean flags = environment.acceptsProfiles(profiles);

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(flags) // enable：是否启动Swagger；如果为false，则Swagger不能在浏览器中访问。
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.heng.swagger.controller"))
                // .paths(PathSelectors.ant("/heng/**"))
                .build();
    }
```

配置API文档的分组

```java
.gropName("heng")
```

如何配置多个分组；多个Docket实例即可


# 任务

异步任务

定时任务 表达式

邮件发送

```java
TaskScheduler 任务调度者
TaskExecuter 任务执行者

@EnableScheduling // 开启定时功能的注解
@Scheduled //什么时候执行 

Cron表达式
```