# Druid 常用配置

## Druid 单数据源

- [官方常见问题](https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
- Springboot 整合 Druid 配置文件

```
@Configuration
public class DruidConfiguration {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.druid")
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        //dataSource.setProxyFilters(Lists.newArrayList(statFilter())); //打开慢sql记录，可在application.yml中配置
        return dataSource;
    }

    @Bean
    public ServletRegistrationBean<StatViewServlet> statViewServlet() {
        ServletRegistrationBean<StatViewServlet> servletRegistrationBean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
        //设置ip白名单
        servletRegistrationBean.addInitParameter("allow", "127.0.0.1");
        //设置ip黑名单，优先级高于白名单
        servletRegistrationBean.addInitParameter("deny", "192.168.0.19");
        //设置控制台管理用户
        servletRegistrationBean.addInitParameter("loginUsername", "root");
        servletRegistrationBean.addInitParameter("loginPassword", "root");
        //是否可以重置数据
        servletRegistrationBean.addInitParameter("resetEnable", "false");
        return servletRegistrationBean;
    }

    @Bean
    public FilterRegistrationBean<WebStatFilter> webStatFilter() {
        //创建过滤器
        FilterRegistrationBean<WebStatFilter> filterRegistrationBean = new FilterRegistrationBean<>(new WebStatFilter());
        //设置过滤器过滤路径
        filterRegistrationBean.addUrlPatterns("/*");
        //忽略过滤的形式
        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
        return filterRegistrationBean;
    }

//    @Bean
//    public Filter statFilter() {
//        StatFilter filter = new StatFilter();
//        filter.setSlowSqlMillis(3000);
//        filter.setLogSlowSql(true);
//        filter.setMergeSql(true);
//        return filter;
//    }

    @Bean
    public ServletRegistrationBean<StatViewServlet> servletRegistrationBean() {
        return new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
    }
}
```

- application.yml 配置

```
spring:
  datasource:
    druid:
      url: jdbc:mysql://139.9.112.174:3306/mall?characterEncoding=UTF8&serverTimezone=Asia/Shanghai
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root
      initialSize: 5                                 #初始化连接大小
      minIdle: 5                                     #最小连接池数量
      maxActive: 20                                  #最大连接池数量
      maxWait: 60000                                 #获取连接时最大等待时间，单位毫秒
      timeBetweenEvictionRunsMillis: 60000           #配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      minEvictableIdleTimeMillis: 300000             #配置一个连接在池中最小生存的时间，单位是毫秒
      validationQuery: SELECT 1 FROM DUAL            #测试连接
      testWhileIdle: true                            #申请连接的时候检测，建议配置为true，不影响性能，并且保证安全性
      testOnBorrow: false                            #获取连接时执行检测，建议关闭，影响性能
      testOnReturn: false                            #归还连接时执行检测，建议关闭，影响性能
      poolPreparedStatements: false                  #是否开启PSCache，PSCache对支持游标的数据库性能提升巨大，oracle建议开启，mysql下建议关闭
      maxPoolPreparedStatementPerConnectionSize: 20  #开启poolPreparedStatements后生效
      filters: stat,wall,log4j2                      #配置扩展插件，常用的插件有=>stat:监控统计  log4j:日志  wall:防御sql注入
      connectionProperties: 'druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000'  #通过connectProperties属性来打开mergeSql功能;慢SQL记录
```

- [管理界面地址](http://localhost:8080/druid/login.html)

## Druid 多数据源

- application.yml 配置

```
spring:
  datasource:
    druid:
      mall:
        username: root
        password: root
        driver-class-name: com.mysql.cj.jdbc.Driver
        type: com.alibaba.druid.pool.DruidDataSource
        url: jdbc:mysql://139.9.112.174:3306/mall?characterEncoding=utf8&serverTimezone=Asia/Shanghai
        # 数据源其他配置
        initialSize: 5
        minIdle: 5
        maxActive: 50
        maxWait: 900000
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: SELECT CURRENT_DATE
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
      sky:
        username: root
        password: root
        driver-class-name: com.mysql.cj.jdbc.Driver
        type: com.alibaba.druid.pool.DruidDataSource
        url: jdbc:mysql://139.9.112.174:3306/sky?characterEncoding=utf8&serverTimezone=Asia/Shanghai
        # 数据源其他配置
        initialSize: 5
        ...
```

- DruidConfiguration 配置类

```
@Configuration
public class DruidConfiguration {

    @Bean(name = "mall")
    @ConfigurationProperties(prefix = "spring.datasource.druid.mall")
    public DataSource mallDruid() {
        return new DruidDataSource();
    }

    @Bean(name = "sky")
    @ConfigurationProperties(prefix = "spring.datasource.druid.sky")
    public DataSource skyDruid() {
        return new DruidDataSource();
    }

    @Bean
    @Primary
    public DynamicDataSource dataSource() {
        Map<String, DataSource> targetDataSources = new HashMap<>();
        targetDataSources.put("mall", mallDruid());
        targetDataSources.put("sky", skyDruid());
        // 这里设置默认数据源为mall
        return new DynamicDataSource(mallDruid(), targetDataSources);
    }

    ...
}
```

- mybatis 自定义数据源

```
public class DynamicDataSource extends AbstractRoutingDataSource {
    public DynamicDataSource(DataSource defaultTargetDataSource, Map<String, DataSource> targetDataSources) {
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        super.setTargetDataSources(new HashMap<>(targetDataSources));
        super.afterPropertiesSet();
    }
    @Override
    protected Object determineCurrentLookupKey() {
        return DynamicDataSourceHolder.getDataSource();
    }
}
```

- mybatis 自定义数据源管理器

```
class DynamicDataSourceHolder {
	private static final ThreadLocal<String> holder = new ThreadLocal<String>();

	static void setDataSource(String name) {
		holder.set(name);
	}

	static String getDataSource() {
		return holder.get();
	}
}
```

- mybatis 自定义注解,用来声明数据源

```
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface DataSource {
	String value();
}
```

- mybatis 切面，用来切换数据源

```
@Aspect
@Component
public class DataSourceAspect {

    @Pointcut("execution(* com.wcs.mall.*.dao.*.*(..))")
    public void dataSourcePointCut() {

    }

    @Before("dataSourcePointCut()")
    public void before(JoinPoint point) {
        Object target = point.getTarget();
        DataSource dataSource = target.getClass().getInterfaces()[0].getAnnotation(DataSource.class);
        if (dataSource != null) {
            String key = dataSource.value();
            System.out.println("当前运行的数据源为：" + key);
            DynamicDataSourceHolder.setDataSource(key);
        }
    }
}
```

- 在 dao 层打上注解，指定数据库

```
@DataSource("sky")
public interface SkyUserDao {
    List<SkyUser> findAll();
}
```

- 启动类排除默认数据源

```
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class MallApplication {
        ...
}
```
