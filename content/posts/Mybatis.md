---
title: "Mybatis"
date: 2021-03-31T13:38:44+08:00
draft: false
---

### 环境
- JDK1.8
- Mysql 5.7
- Maven 3.6.1
- IDEA

### 回顾
- JDBC
- Mysql
- Java 基础
- Maven
- Junit

SSM 框架：配置文件的，最好的方式：看官方文档

# 1 简介
## 1.1 什么是Mybatis

- MyBatis 是一款优秀的**持久层框架**。
- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 1.2 持久层

数据持久化
- 持久化是将程序数据在持久状态和瞬时状态间转换的机制。通俗的讲，就是瞬时数据（比如内存中的数据，是不能永久保存的）持久化为持久数据（比如持久化至数据库中，能够长久保存）。
- 内存：**断电即失**
- 数据库（JDBC），io文件持久化。
- 生活：冷藏.罐头

**为什么需要持久化**

- 有一些对象，不能让它丢掉
- 内存太贵

## 1.3 持久层

Dao层、Serveice层、Controller层……
- 完成持久化工作的代码块
- 层界限十分明显

## 1.4 什么么需要Mybatis
- 帮助程序员将数据存入到数据库中
- 方便
- 传统的JDBC代码太复杂，简化
- 不用Mybatis也可以，更容易上手，技术没有高低之分
- 优点：
    - 简单易学
    - 灵活
    - sql和代码的分离，提高了可维护性
    - 提供映射标签，支持对象和数据库的orm字段关系映射
    - 提供对象关系映射标签，支持对象关系组建维护。
    - 提供xml标签，支持编写动态sql

**最重要的一点：使用的人多**

Spring SpringMVC SpringBoot

# 2 第一个Mybatis程序
思路：搭建环境->Mybatis->编写代码->测试！

## 2.1 搭建环境
搭建数据库
```sql
USE demo_siahk;

CREATE TABLE 'user'(
	'id' INT(20) NOT NULL PRIMARY KEY,
	'naem' VARCHAR(30) DEFAULT NULL,
	'pwd' VARCHAR(30) DEFAULT NULL
) 


INSERT INTO `user` (id, name, pwd) VALUES (1, '王五', '123456'),(2, '张三', '123456'),(3, '李四', '123890');
```

## 2.2 创建一个模板

- 编写mybatis的核心配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--核心配置文件-->
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://sh-cdb-b11akhd4.sql.tencentcdb.com:60170/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="mxqs6689**"/>
            </dataSource>
        </environment>
    </environments>

</configuration>
```

```java
//sqlSessionFactory --> sqlSession
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            //使用Mybatis
            // 第一步：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句

    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();

    }
}
```

## 2.3 编写代码

- 实体类

```java
// 实体类
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

- Dao接口

```java
public interface UserDao {
    List<User> getUserList();
}
```

- 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应的Dao/Mapper/接口-->
<mapper namespace="com.heng.dao.UserDao">
    <select id="getUserList" resultType="com.heng.pojo.User">
        SELECT * FROM mybatis.user;
    </select>
</mapper>
```

## 2.4 测试

注意点：org.apache.ibatis.binding.BindingException: Type interface com.heng.dao.UserDao is not known to the MapperRegistry.

**MapperRegistry是什么？**

核心文件中注册mappers。


```xml
<!--在build中配置resources，来防止资源导入失败的问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```


- junit测试

```java
@Test
    public void test() {

        // 第一步：获取sqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 方式一：getMapper
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();




        for (User user: userList) {
            System.out.println(user);
        }

        // 关闭sqlSession
        sqlSession.close();
    }
```

可能会遇到的问题：

1. 配置文件没有注册
2. 绑定接口错误
3. 方法名不对
4. 返回类型不对
5. Maven 导出资源问题

# 3 CRUD（Create，Retrieve，Update，Delete）

## 3.1 namespace

namespace中的包名要和 Dao/Mapper 接口的包名一致

## 3.2 select

选择，查询语句；

- id：就是对应的namespace中的方法名；
- resultType：Sql语句执行的返回值！
- parametType：参数类型！

1. 编写接口

```java
// 根据ID查询用户
    User getUserById(int id);
```

2. 编写对应的Mapper中的sql语句

```xml
<select id="getUserById" parameterType="int" resultType="com.heng.pojo.User">
        SELECT * FROM demo_siahk.user WHERE id = #{id};
</select>
```

3. 测试

```java
@Test
public void getUserById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.getUserById(2);

    System.out.println(user);

    sqlSession.close();
}
```

## 3.3 insert

```xml
<insert id="addUser" parameterType="com.heng.pojo.User">
    INSERT INTO demo_siahk.user (id, name, pwd) VALUES (#{id},#{name},#{pwd});
</insert>
```

## 3.4 update

```xml
<update id="updateUser" parameterType="com.heng.pojo.User">
    update demo_siahk.user
    set name = #{name}, pwd = #{pwd}
    where id = #{id};
</update>
```

## 3.5 delete

```xml
<delete id="deleteUser" parameterType="int">
    delete from demo_siahk.user where id = #{id};
</delete>
```

注意点：

- 增删改需要提交事务

## 3.6 分析错误

- 标签不要匹配错
- resource 使用 mapper 需要使用路径
- 程序配置文件必须符合规范
- NullPointException，没有注册到资源
- 输出的xml文件中存在中文乱码问题
- maven资源没有导出问题

## 3.7 Map

假如实体类或者数据库中的表的字段或参数过多，考虑使用map。

```java
    int addUser2(Map<String, Object> map);
```

```xml
    <insert id="addUser2" parameterType="map">
        INSERT INTO demo_siahk.user (id, name, pwd) VALUES (#{userId},#{userName},#{password});
    </insert>
```

```java
@Test
    public void addUser2() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        Map<String, Object> map = new HashMap<String, Object>();

        map.put("userId", 5);
        map.put("userName", "Hello");
        map.put("password", "122222");

        mapper.addUser2(map);

        sqlSession.commit();
        sqlSession.close();
    }
```

Map 传递参数，直接在sql中取出key即可。`parameterType="map"`

对象传递参数，直接在sql取出对象的属性即可。`parameterType="Object"`

只有一个基本类型参数的情况下，可以直接在sql取到。

多个参数用Map，**或者注解**

## 3.8 思考题

模糊查询怎么写？

1. java代码执行的时候，传递通配符 % %。

```java
List<User> userList = mapper.getUserLike("%李%");
```

2. 在sql拼接中使用通配符

```xml
select * from demo_siahk.user where name like "%"#{value}"%";
```