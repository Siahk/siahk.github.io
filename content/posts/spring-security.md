---
title: "Spring Security"
date: 2021-05-02T15:47:40+08:00
draft: false
---

# 概述

- Spring 是一个非常流行和成功的 Java 应用开发框架
- Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案

**相关：**

- 用户认证
  - 设置用户名和密码
    - 通过数据库完成认证
  - 自定义登录页面
- 用户授权
  - 权限访问控制
  - 角色访问控制
  - 自定义错误页面
  - 用户注销
  - 自动登录
  - CSRF功能
- 注解使用

# 1 用户认证

## 1.1 设置用户和密码

### 1 通过配置文件

application.properties

```xml
server.security.user.name=root
server.security.user.password=123456
```

### 2 通过配置类

SecurityConfig.java

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    // 认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        String password = passwordEncoder.encode("123456");
        auth.inMemoryAuthentication().withUser("root").password(password).roles("admin");
    }
    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 3 自定义编写实现类

1. 创建配置类：设置使用哪个userDetailsService实现类

   配置类名称：SecurityConfig.java依赖于`WebSecurityConfigurerAdapter`

   ```java
   @Autowired
   private UserDetailsService userDetailsService;
   @Bean
   PasswordEncoder passwordEncoder() {
       return new BCryptPasswordEncoder();
   }
   // 认证 
   @Override
   protected void configure(AuthenticationManagerBuilder auth) throws Exception {
       auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
   }
   ```

2. 编写接口实现类，返回User对象，User对象包含用户名、密码和操作权限

   ```java
   @Service("userDetailsService")
   public class MyUserDetailsService implements UserDetailsService {
       @Autowired
       UserMapper userMapper; // 注入UserMapper类
       @Override
       public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
           User user = userMapper.queryUserByName(userName); // 调用UserMapper接口
           List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role"); // 权限 
           return new org.springframework.security.core.userdetails.User(user.getName(),
                   new BCryptPasswordEncoder().encode(user.getPwd()),auths); // 返回User对象
       }
   }
   ```

   *注：*

   ①*@Service*：*此注解属于业务逻辑层，service或者manager层；默认按照名称进行装配，名称通过name属性指定，如果没有name属性，当注解写在字段上时，默认name为字段名进行查找，如果注解写在setter方法上，默认name为方法名进行装配。当找不到匹配的bean时，才按照类型进行装配，如果name名称一旦指定就会按照名称进行装配。*

## 1.2 自定义登录页面

ServiceConfig.java

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin() // 自定义自己编写的登录页面
                .loginPage("/login.html") // 登录页面设置
                .loginProcessingUrl("/login") //设置访问路径
                .defaultSuccessUrl("/test").permitAll() // 登录成功之后，跳转路径
                .and()
                .authorizeRequests() // 授权请求
                .antMatchers("/","/index").permitAll() // 设置那些路径可以直接访问，不需要认证
                .anyRequest().authenticated();
        http.csrf().disable(); // 关闭csrf功能
    }
```

MvcConfig.java

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/login.html").setViewName("login");
    }
}
```

login.html

```html
<form action="/login" method="post">
    <label>用户名：<input type="text" name="username"/></label>
    <br>
    <label>密码<input type="text" name="password"/></label>
    <br>
    <input type="submit" th:value="login"/>
</form>
```

*注：接收的是POST请求*

# 2 用户授权

## 2.1 权限访问控制

### hasAuthority：设置单个权限

设置权限：

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests().antMatchers("/").hasAuthority("admin");
    }
```

获取权限：

```java
    @Override    
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
    }
```

### hasAnyAuthority：设置多个权限

设置权限

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests().antMatchers("/").hasAnyAuthority("admin, guest");
    }
```

获取权限

```java
    @Override    
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
    }
```



## 2.2 角色访问控制

设置权限

```java
// 单个角色
http.authorizeRequests().antMatchers("/").hasRole("sale");
// 多个角色
http.authorizeRequests().antMatchers("/").hasAnyRole("sale, lucy");
```

获取权限

```java
    @Override    
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_sale");
    }
```

## 2.3 自定义错误页面

在resources目录下添加`error`文件夹，建立对应错误的名字的页面

例：403.html、404.html、500.html

## 2.4 用户注销

1. 登录页面添加退出连接

   index.html

   ```java
   <body>
   <a href="/logout">logout</a>
   </body>
   ```

2. 配置类中添加映射地址

   ```java
   http.logout().logoutUrl("/logout").logoutSuccessUrl("/index").permitAll();
   ```

## 2.5 自动登录

1. 创建数据库表

2. 注入数据源，配置操作数据库对象（在配置类中）

   ```java
       @Autowired
       DataSource dataSource;
       // 配置对象
       @Bean
       public PersistentTokenRepository persistentTokenRepository() {
           JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
           jdbcTokenRepository.setDataSource(dataSource);
   //        jdbcTokenRepository.setCreateTableOnStartup(true); // 自动创建数据库表
           return jdbcTokenRepository;
       }
   ```

3. 修改配置类

   ```java
       // 授权
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http.rememberMe().tokenRepository(persistentTokenRepository()) // 设置操作对象
                   .tokenValiditySeconds(60) // 设置有效时长，单位秒
                   .userDetailsService(userDetailsService); // 设置查询的数据库对象
       }
   ```

## 2.6 CSRF功能

**跨站请求伪造**（Cross-site request forgery）也称**CSRF**：实现的原理是CSRF攻击者在用户已经登录目标网站之后，诱使用户访问一个攻击页面，利用目标网站对用户的信任，以用户身份在攻击页面对目标网站发起伪造用户操作的请求，达到攻击目的。

默认开启CSRF保护，关闭方式：

```java
// 配置类，授权方法里
http.csrf().disable();
```

针对的`PATCH`，`POST`，`PUT`，`DELETE`方法进行防护

登录页面添加一个隐藏域：

```html
<input type="hidden" th:if="${_csrf}!=null" th:href="${_csrf.token}" name="_csrf">
```

# 3 注解

使用注解需要先开启注解功能

## 3.1 @Secured

`@EnableGlobalMethodSecurity(securedEnabled = true)`

作用：判断是否有角色。需要注意匹配的字符串需要添加前缀"ROLE_"。

```java
// 控制器中使用
@Controller
public class UserController {
    @GetMapping("/test")
    @Secured({"ROLE_sale","ROLE_lucy"}) // 需要sale或lucy角色才能使用该方法
    public String test() {
        return "/test";
    }
}
```

## 3.2 @PreAuthorize

`@EnableGlobalMethodSecurity(prePostEnabled = true)`

作用：适合于进入方法前的验证，可以将登录用户的`role`/`permissions`参数传到方法中。

```java
// 控制器中使用
@Controller
public class UserController {
    @GetMapping("/test")
    // @PreAuthorize("hasRole('Role_管理员')")
    @PreAuthorize("hasAnyAuthority('menu:system')")
    public String test() {
        return "/test";
    }
}
```

## 3.3 @PostAuthorize

`@EnableGlobalMethodSecurity(prePostEnabled = true)`

作用：使用不多，在方法执行后再进行权限验证，适合验证带有返回值的权限。

## 3.4 @PreFilter

作用：进入控制器之前对数据进行过滤 

## 3.5 @PostFilter

作用：权限验证之后对数据进行过滤，留下用户名是Admin的数据。

    @GetMapping("/test")
    @PreAuthorize("hasRole('Role_管理员')")
    @PostFilter("filterObject.username == 'Admin'")

