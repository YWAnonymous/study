# mybatis资料

## springboot整合mybatis

### 1、引入依赖

```xml
<!--mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>

<!--druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.19</version>
</dependency>

<!--mybatis-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

### 2、配置properties

```properties
# 连接mysql
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&useSSL=false
spring.datasource.username=root
spring.datasource.password=root
# 配置mybatis
mybatis.mapper-locations =classpath:com/zhou/mappers/*.xml
mybatis.type-aliases-package=com.zhou.entity
# 配置日志
logging.level.com.zhou.dao=debug
```

### 3、UserMapper.xml

```xml
<!--接口名与Mybatis的映射文件名一定要一模一样-->
<!--创建UserMapper文件的时候，路径 com/zhou/mapper/UserMapper-->
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.zhou.dao.UserDao">
    <select id="findAll" resultType="User" >
        select id,username,password from user;
    </select>
</mapper>
```

### 4、UserDao.java

```java
// 使用mapper注解
@Mapper
public interface UserDao {
    List<User> findAll();
}
```

## jdbc连接mysql

```java
//加载(注册)驱动
Class.forName("com.mysql.jdbc.Driver");
//连接获取数据库
String url ="jdbc:mysql://localhost:3306/test";
Connection connection = DriverManager.getConnection(url,"root","root");
//创建statement对象来执行sql语句
Statement statement = connection.createStatement();
//执行sql语句
ResultSet rs = statement.executeQuery("select * from user;");
//遍历查询结果
if(rs.next()){
    //打印字段
    System.out.println(rs.getInt("id"));
    System.out.println(rs.getString("username"));
}
//关闭连接
rs.close();
statement.close();
connection.close();
```

