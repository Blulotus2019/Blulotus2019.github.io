# 代码开发过程中的一些注意事项

## 写模块化的代码

- 每个函数只做自己的事情：先自己管好自己。例如，以下函数：

```
void foo() {
  if (getOS().equals("MacOS")) {
    a();
  } else {
    b();
  }
  c();
  if (getOS().equals("MacOS")) {
    d();
  } else {
    e();
  }
}
```
这个函数是根据"MacOS"不同来做不同的事情，这里只有c()是共有的，**这两件事之间的共同点少于他们的不同点**，这样就导致函数的逻辑不清晰。应该修改为以下：
```
void fooMacOS() {
  a();
  c();
  d();
}
```
和
```
void fooOther() {
  b();
  c();
  e();
}
```
**如果两件事之间的共同点大于他们的不同点**，则可以把相同的提取出去。
```
void foo() {
  a();
  b()
  c();
  if (getOS().equals("MacOS")) {
    d();
  } else {
    e();
  }
}
```
可以把共有的提取出去
```
void preFoo() {
  a();
  b()
  c();
```
然后分为两个函数
```
void fooMacOS() {
  preFoo();
  d();
}
```
和
```
void fooOther() {
  preFoo();
  e();
}
```
这样既共享了代码，又让每一个函数只做一件事，让逻辑更清晰。

## 写可读的代码

- 使用有意义的函数和变量名

- 局部变量应该尽量接近他使用的地方
```
void foo() {
  ...
  ...
  int index = ...;
  bar(index);
  ...
}
```
而不是在一开头就定义一堆局部变量
```
void foo() {
  int index = ...;
  ...
  ...
  bar(index);
  ...
}
```

- 不要重用局部变量
```
String msg;
if (...) {
  msg = "succeed";
  log.info(msg);
} else {
  msg = "failed";
  log.info(msg);
}
```
这种赋值的做法，把局部变量的作用域不必要的增大，让人以为他可能在将来改变，也许会在其它地方被使用。更好的做法，其实是定义两个变量：
```
if (...) {
  String msg = "succeed";
  log.info(msg);
} else {
  String msg = "failed";
  log.info(msg);
}
```

- 把复杂的逻辑提取出去，做成“帮助函数”。
```
...
// put elephant1 into fridge2
openDoor(fridge2);
if (elephant1.alive()) {
  ...
} else {
   ...
}
closeDoor(fridge2);
...
```
将put elephant1 into fridge2提取出来定义成一个函数：
```
void put(Elephant elephant, Fridge fridge) {
  openDoor(fridge);
  if (elephant.alive()) {
    ...
  } else {
     ...
  }
  closeDoor(fridge);
}
```
然后原来的代码就变成：
```
...
put(elephant1, fridge2);
...
```
如此逻辑就更加清晰，注释也不需要了。

- 合理的换行。
```
   if (someLongCondition1() && someLongCondition2() && someLongCondition3() &&
     someLongCondition4()) {
     ...
   }
```
这几个boolean都处于同等的地位，将每一个表达式都放在新的一行，这样逻辑就更清晰了。
```
   if (someLongCondition1() &&
       someLongCondition2() &&
       someLongCondition3() &&
       someLongCondition4()) {
     ...
   }
```

## 写简单的代码

- 避免使用continue和break。在循环语句中使用continue和break会让循环的逻辑和终止的条件变得复杂。  

  出现continue或者break的原因，往往是对循环的逻辑没有想清楚。如果你考虑周全了，应该是几乎不需要continue或者break的。如果你的循环里出现了continue或者break，你就应该考虑改写这个循环。改写循环的办法有多种：  
  1.如果出现了continue，你往往只需要把continue的条件反向，就可以消除continue。
  2.如果出现了break，你往往可以把break的条件，合并到循环头部的终止条件里，从而去掉break。
  3.有时候你可以把break替换成return，从而去掉break。
  4.如果以上都失败了，你也许可以把循环里面复杂的部分提取出来，做成函数调用，之后continue或者break就可以去掉了。  

  实例一：

  ```
  List<String> goodNames = new ArrayList<>();
for (String name: names) {
  if (name.contains("bad")) {
    continue;
  }
  goodNames.add(name);
  ...
}  
  ```

  以上代码是告诉我们，当含有“bad”的时候是**不做**什么事，修改为**做**什么事，会让逻辑更清晰。
  可以等价修改为不含continue的代码:

  ```
List<String> goodNames = new ArrayList<>();
for (String name: names) {
  if (!name.contains("bad")) {
    goodNames.add(name);
    ...
  }
}  
  ```

  实例二：
  for和while头部都有一个循环的“终止条件”，那本来应该是这个循环唯一的退出条件。如果你在循环中间有break，它其实给这个循环增加了一个退出条件。你往往只需要把这个条件合并到循环头部，就可以去掉break。

  ```
while (condition1) {
  ...
  if (condition2) {
    break;
  }
}
  ```
  当condition2成立的时候终止循环，这里只要将condition2反转后放到while头部终止条件即可去掉break。
  ```
while (condition1 && !condition2) {
  ...
}
  ```

  实例三：很多break退出循环之后，其实接下来就是一个return。这种break往往可以直接换成return。

  ```
public boolean hasBadName(List<String> names) {
    boolean result = false;

    for (String name: names) {
        if (name.contains("bad")) {
            result = true;
            break;
        }
    }
    return result;
}
  ```
  可以改写为：
  ```
public boolean hasBadName(List<String> names) {
    for (String name: names) {
        if (name.contains("bad")) {
            return true;
        }
    }
    return false;
}
  ```
  99%的break和continue，都可以通过替换成return语句，或者翻转if条件的方式来消除掉。

## 正确处理异常

- 异常处理原则：谁知情谁处理，谁负责谁处理，谁导致谁处理

## 正确处理null指针

- 尽量不要产生null指针。尽量不要用null来初始化变量，函数尽量不要返回null。如果你的函数要返回“没有”，“出错了”之类的结果，尽量使用Java的异常机制。
  比如，如果你有一个函数find，可以帮你找到一个String，也有可能什么也找不到，你可以这样写：
  ```
public String find() throws NotFoundException {
  if (...) {
    return ...;
  } else {
    throw new NotFoundException();
  }
}
  ```
- TODO结合晓风关于null的处理
- 要明确null返回的意义，不能没有找到的时候返回null，程序出错或异常的时候也返回null。

## 防止过度工程
三原则:  

- 先把眼前的问题解决掉，解决好，再考虑将来的扩展问题。
- 先写出可用的代码，反复推敲，再考虑是否需要重用的问题。
- 先写出可用，简单，明显没有bug的代码，再考虑测试的问题。
  
