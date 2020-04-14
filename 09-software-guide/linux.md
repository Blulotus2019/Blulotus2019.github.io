# Linux

- [Linux 命令大全](https://man.linuxde.net/)

## 文件操作

- 查看目录信息：`ls`或`ll`（`ll`是`ls -l`的缩写，用来查看所有目录和文件详细信息）。

- 查找目录：`find /home -name "*.txt"`在/home 目录下查找以.txt 结尾的文件名。

- 创建目录：mkdir(make directories)

  - 说明：mkdir 可建立目录并同时设置目录的权限。
  - 语法：`mkdir [-p][--help][--version][-m <目录属性>][目录名称]`
  - 参数：
    - `-p或–parents`若所要建立目录的上层目录目前尚未建立，则会一并建立上层目录。
    - `-m或–mode`建立目录时同时设置目录的权限。
  - 实例：`mkdir -p test1/test2` 创建 test 文件夹。

- 创建文件：touch

  - 说明：使用 touch 指令可更改文件或目录的日期时间，包括存取时间和更改时间。
  - 语法：`touch [-acfm][-d <日期时间>][-r <参考文件或目 录>][-t <日期时间>] [--help] [--version][文件或目录...] 或 touch [-acfm][--help][--version][日期时 间][文件或目录...]`
  - 参数：
    - `-a或–time=atime或–time=access或–time=use`只更改存取时间。
    - `-c或–no-create`不建立任何文件。
    - `-d <时间日期>`使用指定的日期时间，而非现在的时间。
    - `-f`此参数将忽略不予处理，仅负责解决 BSD 版本 touch 指令的兼容性问题。
    - `-m或–time=mtime或–time=modify`只更改变动时间。
    - `-r<参考文件或目录>`把指定文件或目录的日期时间，统统设成和参考文件或目录的日期时间相同。
    - `-t<日期时间>`使用指定的日期时间，而非现在的时间。
  - 实例：`touch test.txt` 创建 test 文件。
    > 注：Linux 下没有文件后缀名区分文件类型之说，系统文件类型只有可执行文件和不可执行文件

- 编辑文件：`vim 文件`->`i`进入编辑模式->编辑完成->`Esc` 进入底行模式->输入`:wq`保存并退出或`q!`强制退出不保存。

- 压缩文件：tar

  - 语法：`tar -zcvf 打包压缩后的文件名 要打包压缩的文件`
  - 参数：
    - z：调用 gzip 压缩命令进行压缩
    - c：打包文件
    - v：显示运行过程
    - f：指定文件名
  - 实例：加入 test 目录下有三个文件分别是 :aaa.txt bbb.txt ccc.txt,如果我们要打包 test 目录并指定压缩后的压缩包名称为 test.tar.gz 可以使用命令：`tar -zcvf test.tar.gz aaa.txt bbb.txt ccc.txt`或`tar -zcvf test.tar.gz /test/`

- 解压文件：`tar [-xvf] 压缩文件`

  - x：代表解压
  - 实例 1：将/test 下的 test.tar.gz 解压到当前目录下可以使用命令：`tar -xvf test.tar.gz`
  - 实例 2：将/test 下的 test.tar.gz 解压到根目录/usr 下: `tar -xvf xxx.tar.gz -C /usr` （- C 代表指定解压的位置）

- 删除目录、文件：rm(remove)
  - 说明：执行 rm 指令可删除文件或目录，如欲删除目录必须加上参数”-r”，否则预设仅会删除文件。
  - 语法：`rm [-dfirv][--help][--version][文件或目录...]`
  - 参数：
    - `-d或–directory`直接把欲删除的目录的硬连接数据删成 0，删除该目录。
    - `-f或–force`强制删除文件或目录。
    - `-i或–interactive`删除既有文件或目录之前先询问用户。
    - `-r或-R或–recursive`递归处理，将指定目录下的所有文件及子目录一并处理。
    - `-v或–verbose`显示指令执行过程。
  - 实例：`rm -rf fileDir` 递归删除 fileDir 目录下所有文件

## 其他操作

- 运行文件：`./tomcat.sh`
- 后台运行服务：`nohup command &`
  - eg：后台设置内存参数启动 jar 包：`nohup java -Xms128m -Xmx512m -jar proManager.jar >temp.txt &`默认输出到 nohup.out 文件中
- 查看后台运行的状态：`jobs`;`jobs -l`选项可显示所有任务的 PID
- `ps -ef | grep command` 或者 `ps aux | grep command`或者`netstat -tanlp` 查看进程
- 杀掉对应的进程：`kill -9 进程id`（-9 表示强制终止）
- 更高级的用法：`ps aux | grep command | grep -v grep | awk '{print $1}' | xargs kill -9`这个表示直接通过 command 获取进程 id 并直接 kill 掉
- 双击`tab`提示
- 文件中 `/` 可以进行搜索
- 给文件添加可执行权限：`chmod 777 tomcat.sh`
- `man cal` （manaul,cal 查看 cal 计算器相关的指令）
