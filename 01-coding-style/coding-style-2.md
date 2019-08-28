# 编程规范（二）

## Controller 规范
- 统一返回 ResultBean/PageBean 对象，方便前端对代码的重用
- ResultBean/PageBean 是 Controller 专用，不应该往其他地方传，否则可读性与 map, json 相比好不了多少
- Controller 只做参数格式的转换
  - 不允许把 json，map 这类对象传到 services 去，也不允许 services 返回 json、map
  - 定义一个 bean 看起来麻烦点，但代码清晰很多
>Contorller 只做参数格式转换，如果没有参数需要转换的，那么就一行代码。日志/参数校验/权限判断建议放到 service 里面，毕竟 controller 基本无法重用，而 service 重用较多。而我们的单元测试也不需要测试 controller，直接测试 service 即可。

## AOP 实现
- 在 AOP 里面统一处理异常，包装成相同的对象 ResultBean 给前台。
- ResultBean 定义带泛型，使用了lombok:
```
@Data
public class ResultBean<T> implements Serializable {

  private static final long serialVersionUID = 1L;

  public static final int NO_LOGIN = -1;

  public static final int SUCCESS = 0;

  public static final int FAIL = 1;

  public static final int NO_PERMISSION = 2;

  private String msg = "success";

  private int code = SUCCESS;

  private T data;

  public ResultBean() {
    super();
  }

  public ResultBean(T data) {
    super();
    this.data = data;
  }

  public ResultBean(Throwable e) {
    super();
    this.msg = e.toString();
    this.code = FAIL;
  }
}
```
- AOP代码，主要就是打印日志和捕获异常
  - 异常要区分已知异常和未知异常
  - 其中未知的异常是我们重点关注的，可以做一些邮件通知啥的
  - 已知异常可以再细分一下，可以不同的异常返回不同的返回码

  ```
/**
 * 处理和包装异常
 */
public class ControllerAOP {
  private static final Logger logger = LoggerFactory.getLogger(ControllerAOP.class);

  public Object handlerControllerMethod(ProceedingJoinPoint pjp) {
    long startTime = System.currentTimeMillis();

    ResultBean<?> result;

    try {
      result = (ResultBean<?>) pjp.proceed();
      logger.info(pjp.getSignature() + "use time:" + (System.currentTimeMillis() - startTime));
    } catch (Throwable e) {
      result = handlerException(pjp, e);
    }

    return result;
  }
  
  /**
    * 封装异常信息，注意区分已知异常（自己抛出的）和未知异常
    */
  private ResultBean<?> handlerException(ProceedingJoinPoint pjp, Throwable e) {
    ResultBean<?> result = new ResultBean();

    // 已知异常
    if (e instanceof CheckException) {
      result.setMsg(e.getLocalizedMessage());
      result.setCode(ResultBean.FAIL);
    } else if (e instanceof UnloginException) {
      result.setMsg("Unlogin");
      result.setCode(ResultBean.NO_LOGIN);
    } else {
      logger.error(pjp.getSignature() + " error ", e);
      //TODO 未知的异常，应该格外注意，可以发送邮件通知等
      result.setMsg(e.toString());
      result.setCode(ResultBean.FAIL);
    }

    return result;
  }
}
  ```

- 通过以上总结返回ResultBean的作用
  - 统一格式
  - 应用AOP
  - 包装异常信息
- 简单的controller实例

```
/**
 * 配置对象处理器
 * 
 * @author 晓风轻
 */
@RequestMapping("/config")
@RestController
public class ConfigController {

  private final ConfigService configService;

  public ConfigController(ConfigService configService) {
    this.configService = configService;
  }

  @GetMapping("/all")
  public ResultBean<Collection<Config>> getAll() {
    return new ResultBean<Collection<Config>>(configService.getAll());
  }

  /**
   * 新增数据, 返回新对象的id
   * 
   * @param config
   * @return
   */
  @PostMapping("/add")
  public ResultBean<Long> add(Config config) {
    return new ResultBean<Long>(configService.add(config));
  }

  /**
   * 根据id删除对象
   * 
   * @param id
   * @return
   */
  @PostMapping("/delete")
  public ResultBean<Boolean> delete(long id) {
    return new ResultBean<Boolean>(configService.delete(id));
  }

  @PostMapping("/update")
  public ResultBean<Boolean> update(Config config) {
    configService.update(config);
    return new ResultBean<Boolean>(true);
  }
}
```
## 工具类编写
- 隐藏实现
  - 定义自己的工具类，尽量不要在业务代码里面直接调用第三方的工具类
  - 放便以后业务改动，能够修改自己的工具类
  - 封装/解耦的思想
```
public static boolean isEmpty(String str) {
  return org.apache.commons.lang3.StringUtils.isEmpty(str);
}
```
- 使用父类/接口
  - 抽象的思想
  - 举例，假设我们写了一个判断arraylist是否为空的函数，一开始是这样的：
  ```
public static boolean isEmpty(ArrayList<?> list) {
  return list == null || list.size() == 0;
}
  ```
  - 这个时候，我们需要思考一下参数的类型能不能使用父类。我们看到我们只用了size方法，我们可以知道size方法再list接口上有，于是我们修改成这样：
  ```
public static boolean isEmpty(List<?> list) {
  return list == null || list.size() == 0;
}
  ```
  - 后面发现，size方法再list的父类/接口Collection上也有，那么我们可以修改为最终这样：
  ```
public static boolean isEmpty(Collection<?> collection) {
  return collection == null || collection.size() == 0;
}
  ```
  - 上面的string相关的工具类方法，使用相同的思路，我们最终修改一下，把参数类类型由String修改为CharSequence，参数名str修改为cs。如下：
  ```
public static boolean isEmpty(CharSequence cs) {
  return org.apache.commons.lang3.StringUtils.isEmpty(cs);
}
  ```
- 使用重载编写衍生函数组
  - 根据参数变化编写各种类型的入参函数，需要保证函数主要代码只有一份
## 函数编写
- 不要出现和业务无关的参数
  - 函数参数里面不要出现request，Response这些参数
- 避免使用Map，Json这些复杂对象作为参数和结果
- 有明确的输入输出和方法名
  - 尽量有清晰的输入输出参数，使人一看就知道函数做了啥。举例：
```
public void updateUserNickName(long userId, String nickname){
  //更新代码
}
```
  - 不看方法名，只看参数就能知道这个函数只更新了nickname一个字段
- 把可能变化的地方封装成函数
  - 开发初期，业务说只有管理员才可以删除某个对象，你就应该考虑到后面可能除了管理员，其他角色也可能可以删除，或者说对象的创建者也可以删除，这就是将来潜在的变化，你写代码的时候就要埋下伏笔，把是否能删除做成一个函数。后面需求变更的时候，你就只需要改一个函数。