## SpringBoot jar包转war包
1. 将SpringBoot的项目的打包方式设置为war`<packaging>jar</packaging>`改为`<packaging>jar</packaging>`

2. 移除内嵌的tomcat模块，但是为了我们在本机测试方便，我们还需要引入`<scope>provided</scope>`
```
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
    <exclusions>  
        <exclusion>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-tomcat</artifactId>  
        </exclusion>  
    </exclusions>  
</dependency>  
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-tomcat</artifactId>  
    <scope>provided</scope>  
</dependency>  

```

3. 添加tomcat-servlet-api依赖

```
<dependency>  
    <groupId>org.apache.tomcat</groupId>  
    <artifactId>tomcat-servlet-api</artifactId>  
    <version>7.0.42</version>  
    <scope>provided</scope>  
</dependency> 
```

4. 修改入口方法 继承一个SpringBootServletInitializer类，并且覆盖configure方法
```

public class SpringDataJpaExampleApplication extends SpringBootServletInitializer {  

    @Override  
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {  
        return application.sources(SpringDataJpaExampleApplication.class);  
    }  

    public static void main(String[] args) {  
        SpringApplication.run(SpringDataJpaExampleApplication.class, args);  
    }  
}

```

5. 添加war插件，用来自定义打包以后的war包的名称”book“：项目访问路径 `http://localhost:8080/book/index.html`
```
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-war-plugin</artifactId>  
    <configuration>  
        <warSourceExcludes>src/main/resources/**</warSourceExcludes>  
        <warName>book</warName>  
    </configuration>  
</plugin>  

```

6. springboot版本与mybatis版本导致的问题，修改版本号如下：
```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/> 
    </parent>

        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.1</version>
        </dependency>
```