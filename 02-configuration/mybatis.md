# mybatis

## springboot 整合 mybatis

- 添加依赖

```
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.1.0</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
```

- application.yml 配置

```
logging:
  level:
    com.wcs.mall: DEBUG #显示sql语句
spring
  datasource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=utf-8
    username: root
    password: root
mybatis:
  mapper-locations: classpath:mybatis/mapper/*.xml
  config-location: classpath:mybatis/mybatis-config.xml
```

- mybatis-config.xml 配置

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
         <!-- 配置关闭缓存  -->
         <setting name="cacheEnabled" value="false"/>
         <setting name="mapUnderscoreToCamelCase" value="true"/>
<!--         <setting name="useGeneratedKeys" value="true"/>-->
         <setting name="defaultExecutorType" value="REUSE"/>
         <!-- 事务超时时间 -->
         <setting name="defaultStatementTimeout" value="600"/>
    </settings>

    <typeAliases>
       <typeAlias type="com.wcs.mall.todo.entity.MallTodo" alias="mallTodo" />
    </typeAliases>
</configuration>
```

> mapper 文件不能和 mybatis-config.xml 文件放在同一个文件夹下

- Applicaion 启动类添加注解`@MapperScan`扫描包

```
@SpringBootApplication
@MapperScan(value = {"com.wcs.mall.*.dao"})
public class MallApplication {
    public static void main(String[] args) {
        SpringApplication.run(MallApplication.class, args);
    }

}
```

> 或者直接在 Mapper 类上面添加注解@Mapper，但每个类都要加好麻烦。

## MyBatis-Plus 插件
