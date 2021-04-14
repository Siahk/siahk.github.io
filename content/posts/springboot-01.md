---
title: "Springboot 01"
date: 2021-04-06T12:44:42+08:00
draft: true
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

```java
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