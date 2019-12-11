# 开发中常见问题处理

- 异常处理原则：谁知情谁处理，谁负责谁处理，谁导致谁处理
- 不要在 foreach 循环里进行元素的 remove/add 操作(会出现并发修改异常)
  - remove 元素请使用 Iterator 方式，如果并发操作，需要对 Iterator 对象加锁。
- 数据库批量修改时`<foreach></foreach>`，传入的 list 不能为空
- 先判断 null 再判断空
- retVal = returnValue
- 在代码中写分页查询逻辑时，若 count 为 0 应直接返回，避免执行后面的分页语句。
- 数组转 list 的时候:

  ```
  String[] arr = {"a", "b", "c"};
  List<String> list = Arrays.asList(arr);

  ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(arr));
  ```

  - 返回的 list 长度是固定的，因此使用 list.add("d")会报错。应改成:

  ```
  ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(arr));
  ```

- JSON 工具使用 Jackson，fastjson 漏洞多
- add 添加的时候返回新增的字段或整个对象
- `int` 字段用数字表示的时候，尽量不用 0，0 在 `if` 标签会被 mybatis 当成空字符串，这条根源在于，字段尽量用包装类，因为基本类型都有一个默认值，不利于 mybatis 做非空判断
- `list.size / 0.75`，负载因子 0.75，防止触发扩容：容量 16，放到第 12 个的时候就会触发扩容，所以 `list.size/0.75=21`
- `@EnableAsync @Async` 异步操作注解，eg：发邮件的时候
