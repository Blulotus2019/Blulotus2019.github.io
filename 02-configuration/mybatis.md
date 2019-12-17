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

## MyBatis SQL 语句常用操作

- 通用 CRUD

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="ltd.newbee.mall.dao.AdminUserMapper">

    <resultMap id="BaseResultMap" type="com.wcs.mall.entity.AdminUser">
        <id column="user_id" jdbcType="INTEGER" property="userId"/>
        <result column="user_name" jdbcType="VARCHAR" property="userName"/>
        <result column="password" jdbcType="VARCHAR" property="password"/>
    </resultMap>

    <sql id="baseColumnList">
        user_id, user_name, password
    </sql>

    <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
        SELECT
        <include refid="baseColumnList"/>
        FROM admin_user
        WHERE user_id = #{userId,jdbcType=INTEGER}
    </select>

    <insert id="insert" parameterType="com.wcs.mall.entity.AdminUser">
        INSERT INTO admin_user(
        <include refid="baseColumnList"/>
        )
        VALUES (
        #{adminUserId,jdbcType=INTEGER},
        #{loginUserName,jdbcType=VARCHAR},
        #{loginPassword,jdbcType=VARCHAR}
        )
    </insert>

    <insert id="insertSelective" parameterType="com.wcs.mall.entity.AdminUser">
        INSERT INTO admin_user
        <trim prefix="(" suffix=")" suffixOverrides=",">
            <if test="adminUserId != null">
                user_id,
            </if>
            <if test="loginUserName != null">
                user_name,
            </if>
            <if test="loginPassword != null">
                password,
            </if>
        </trim>
        <trim prefix="values (" suffix=")" suffixOverrides=",">
            <if test="userId != null">
                #{userId,jdbcType=INTEGER},
            </if>
            <if test="userName != null">
                #{userName,jdbcType=VARCHAR},
            </if>
            <if test="password != null">
                #{password,jdbcType=VARCHAR},
            </if>
        </trim>
    </insert>

    <update id="updateByPrimaryKeySelective" parameterType="com.wcs.mall.entity.AdminUser">
        UPDATE admin_user
        <set>
            <if test="userName != null">
                user_name = #{userName,jdbcType=VARCHAR},
            </if>
            <if test="password != null">
                password = #{password,jdbcType=VARCHAR},
            </if>
        </set>
        WHERE
        admin_user_id = #{adminUserId,jdbcType=INTEGER}
    </update>

    <update id="updateByPrimaryKey" parameterType="com.wcs.mall.entity.AdminUser">
        UPDATE admin_user
        SET user_name = #{userName,jdbcType=VARCHAR},
            password  = #{password,jdbcType=VARCHAR},
            WHERE user_id = #{userId,jdbcType=INTEGER}
    </update>
</mapper>
```

- [Dynamic SQL](https://mybatis.org/mybatis-3/dynamic-sql.html)
- `if` 标签

```
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = #{state}
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

- `choose (when, otherwise)`标签

```
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = #{state}
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

- `trim (where, set)`标签

  - `where` 标签能把 `if` 条件中前面多出来的 `AND` 和 `OR` 去除

    ```
    <!-- sql条件都可以放在这里面 -->
    <sql>
        <where>
            <if test="id!=null && id!=0">
                id=#{id}
            </if>
        </where>
    </sql>
    ```

  - `trim` 去除后面的 `where`

    - 后面多出的 `and` 或者 `or where` 标签不能解决
    - `prefix=""`:前缀:`trim` 标签体中是整个字符串拼串后的结果。`prefix` 给拼串后的整个字符串加一个前缀
    - `prefixOverrides=""`:前缀覆盖:去掉整个字符串前面多余的字符
    - `suffix=""`:后缀:`suffix` 给拼串后的整个字符串加一个后缀
    - `suffixOverrides=""`:后缀覆盖:去掉整个字符串后面多余的字符

      ```
      <!-- 自定义字符串的截取规则 -->
      <select>
              ...
          <trim prefix="WHERE" suffixOverrides="AND">
              <if test="id!=null">
                  id=#{id} AND
              </if>
              <if test="lastName!=null & lastName!='';">
                  last_name like #{lastName} AND
              </if>
              <if test="email!=null and email.trim()!=''">
                  email=#{email} AND
              </if>
              <!-- ognl会进行字符串与数字的转换判断  "0"==0 -->
              <if test="gender==0 or gender==1">
                  gender=#{gender}
              </if>
          </trim>
      </select>
      ```

  - `set` 标签

    ```
        <update id="updateAuthorIfNecessary">
        UPDATE Author
        <set>
            <if test="username != null">username=#{username},</if>
            <if test="password != null">password=#{password},</if>
            <if test="email != null">email=#{email},</if>
            <if test="bio != null">bio=#{bio}</if>
        </set>
        WHERE id=#{id}
        </update>
        <!-- 以上可以修改为下面的代码 -->
        <update id="updateAuthorIfNecessary">
        UPDATE Author
            <trim prefix="SET" suffixOverrides=",">
                <if test="username != null">username=#{username},</if>
                <if test="password != null">password=#{password},</if>
                <if test="email != null">email=#{email},</if>
                <if test="bio != null">bio=#{bio},</if>
            </trim>
        </update>
    ```

- `foreach` 标签

  ```
  <select id="selectPostIn" resultType="domain.blog.Post">
  SELECT * FROM POST P WHERE id in
  <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
          #{item}
  </foreach>
  </select>
  ```

  - 批量更新，dao 层要打上注解`@Param("userList")`

    ```
    <update id="updateByBatch" parameterType="java.util.List">
        <foreach collection="userList" item="user" index="index" separator=";">
            UPDATE user
            SET
            name = #{user.name},
            password = #{user.password}
            WHERE
            id = #{user.id}
        </foreach>
    </update>
    ```

  - 批量增加`@Param("proExperimentInspectionList")`

    ```
    <insert id="addByBatch" parameterType="java.util.List">
        INSERT INTO user(
        <include refid="userColumn"/>)VALUES
        <foreach collection="userList" item="user" index="index" separator=",">
            (
            #{user.id},
            #{user.name}
    	)
        </foreach>
    </insert>
    ```

  - 批量更新和批量增加需要数据库连接属性 `allowMultiQueries=true`

- `bind` 标签

```
<!--
bind：可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值
这样模糊查询就可以不用'%${pattern}%'，但还是传参的时候就传 "%模糊%"  更好
-->
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

- 动态查询字段 `STATEMENT`

```
<!--statementType=STATEMENT:非预编译,动态字段名不能使用预编译,且把 #{}改成 ${}-->
<select id="getFilterColumnById" statementType="STATEMENT" resultType="result">
    SELECT
    <foreach collection="columnList" item="item" separator="," index="index">
        ${item}
    </foreach>
    FROM
    pro_item
    WHERE id = ${id}
</select>
```

- `collection` 嵌套结果集的方式，使用 `collection` 标签定义关联的集合类型的属性封装规则

```
<resultMap type="com.atguigu.mybatis.bean.Department" id="MyDept">
    <id column="did" property="id"/>
    <result column="dept_name" property="departmentName"/>
        //collection定义关联集合类型的属性的封装规则
        //ofType:指定集合里面元素的类型
    <collection property="emps" ofType="com.atguigu.mybatis.bean.Employee">
        <!-- 定义这个集合中元素的封装规则 -->
        <id column="eid" property="id"/>
        <result column="last_name" property="lastName"/>
    </collection>
</resultMap>

<!-- collection：分段查询 -->
<resultMap type="com.atguigu.mybatis.bean.Department" id="MyDeptStep">
    <id column="id" property="id"/>
    <id column="dept_name" property="departmentName"/>
    <collection property="emps"
        select="com.atguigu.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
        column="{deptId=id}" fetchType="lazy"></collection>
</resultMap>

<!-- public Department getDeptByIdStep(Integer id); -->
<select id="getDeptByIdStep" resultMap="MyDeptStep">
    select id,dept_name from tbl_dept where id=#{id}
</select>

<!--
    多列的值传递过去：
    将多列的值封装map传递；
    column="{key1=column1,key2=column2}"
    fetchType="lazy"：表示使用延迟加载；- lazy：延迟 - eager：立即
 -->
```

- `association`(是用来定义对象的，collection 是用来定义集合) 分步查询！

```
<!--
    使用association进行分步查询：
    1、先按照员工id查询员工信息
    2、根据查询员工信息中的d_id值去部门表查出部门信息
    3、部门设置到员工中；
-->
<!--  id  last_name  email   gender    department_id   -->
<resultMap type="com.atguigu.mybatis.bean.Employee" id="MyEmpByStep">
    <id column="id" property="id"/>
    <result column="last_name" property="lastName"/>
    <result column="email" property="email"/>
    <result column="gender" property="gender"/>
    <!--  association定义关联对象的封装规则
        select:表明当前属性是调用select指定的方法查出的结果
        column:指定将哪一列的值传给这个方法

        流程：使用select指定的方法（传入column指定的这列参数的值）查出对象，
            并封装给property指定的属性。
        -->
    <association property="dept"
        select="com.atguigu.mybatis.dao.DepartmentMapper.getDeptById"
        //DepartmentMapper.xml中的getDeptById
        column="department_id">
    </association>
</resultMap>

<!--  public Employee getEmpByIdStep(Integer id);-->
<select id="getEmpByIdStep" resultMap="MyEmpByStep">
    SELECT * FROM tbl_employee WHERE id=#{id}
    <if test="_parameter!=null">
        and 1=1
    </if>
</select>

<!--
    可以使用延迟加载（懒加载）；(按需加载)
    Employee==>Dept：
    我们每次查询Employee对象的时候，都将一起查询出来。
    部门信息在我们使用的时候再去查询；
    分段查询的基础之上加上两个配置：mybatis-conf.xml中加上
    显示的指定每个我们需要更改的配置的值，即使他是默认的。防止版本更新带来的问题
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"/>
-->
```

- 鉴别器 `discriminator`：mybatis 可以使用 `discriminator` 判断某列的值，然后根据某列的值改变封装行为

```
<!--
封装Employee：
如果查出的是女生：就把部门信息查询出来，否则不查询；
如果是男生，把last_name这一列的值赋值给email;
-->
<resultMap type="com.atguigu.mybatis.bean.Employee" id="MyEmpDis">
    <id column="id" property="id"/>
    <result column="last_name" property="lastName"/>
    <result column="email" property="email"/>
    <result column="gender" property="gender"/>
    <!-- column：指定判定的列名
    javaType：列值对应的java类型 -->
    <discriminator javaType="string" column="gender">
        <!--女生  resultType:指定封装的结果类型；不能缺少。/resultMap-->
        <case value="0" resultType="com.atguigu.mybatis.bean.Employee">
            <association property="dept"
                select="com.atguigu.mybatis.dao.DepartmentMapper.getDeptById"
                column="d_id">
            </association>
        </case>
        <!-- 男生 ;如果是男生，把last_name这一列的值赋值给email; -->
        <case value="1" resultType="com.atguigu.mybatis.bean.Employee">
            <id column="id" property="id"/>
            <result column="last_name" property="lastName"/>
            <result column="last_name" property="email"/>
            <result column="gender" property="gender"/>
        </case>
    </discriminator>
</resultMap>
```

- `CASE WHEN`

```
<!-- 修改默认收货地址 -->
UPDATE shop_address SET state = CASE WHEN id = #{id} THEN 1 ELSE 0 END
WHERE app_user_id = #{app_user_id}

UPDATE 表名 SET 字段1= CASE WHEN 条件1 THEN 1 ELSE 0 END,
字段2= CASE WHEN 条件2 THEN 0 ELSE 1 END
```

## MyBatis 一级缓存和二级缓存

- 一级缓存：本地缓存
  - sqlSession 级别的缓存
  - 一级缓存是一直开启的
  - SqlSession 级别的一个 Map 与数据库同一次会话期间查询到的数据会放在本地缓存中
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库
- 一级缓存失效情况（没有使用到当前一级缓存的情况，效果就是，还需要再向数据库发出查询）
  - sqlSession 不同
  - sqlSession 相同，查询条件不同.(当前一级缓存中还没有这个数据)
  - sqlSession 相同，两次查询之间执行了增删改操作(这次增删改可能对当前数据有影响)
  - sqlSession 相同，手动清除了一级缓存（缓存清空）
- 二级缓存：全局缓存。
  - 基于 namespace 级别的缓存：一个 namespace 对应一个二级缓存
- 工作机制：
  - 一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果会话关闭；一级缓存中的数据会被保存到二级缓存中；新的会话查询信息，就可以参照二级缓存中的内容
  - 不同 namespace 查出的数据会放在自己对应的缓存中（map）
  - 效果：数据会从二级缓存中获取；查出的数据都会被默认先放在一级缓存中；只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中
- 使用：
  - 开启全局二级缓存配置：`<setting name="cacheEnabled" value="true"/>`
  - 去 mapper.xml 中配置使用二级缓存：<cache></cache>
  - POJO 需要实现序列化接口
- 和缓存有关的设置/属性：
  - `cacheEnabled=true`：false：关闭缓存（二级缓存关闭）（一级缓存一直可用的）
  - 每个 select 标签都有 `useCache="true"`：false：不使用缓存（一级缓存依然使用，二级缓存不使用）
  - 每个增删改标签的：`flushCache="true"`：（一级二级都会清除）,增删改执行完成后就会清除缓存；
  - `sqlSession.clearCache()`;只是清除当前 session 的一级缓存；
  - `localCacheScope`：本地缓存作用域：（一级缓存 SESSION）；当前会话的所有数据保存在会话缓存中
  - `STATEMENT`：可以禁用一级缓存

## MyBatis-Plus 插件
