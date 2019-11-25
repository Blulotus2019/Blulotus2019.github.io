# Linux

## 文件操作

- 创建目录 mkdir(make directories)

  - 说明：mkdir 可建立目录并同时设置目录的权限。
  - 语法：`mkdir [-p][--help][--version][-m <目录属性>][目录名称]`
  - 参数：
    - `-p或–parents`若所要建立目录的上层目录目前尚未建立，则会一并建立上层目录。
    - `-m或–mode`建立目录时同时设置目录的权限。
  - 实例：`mkdir -p test1/test2` 创建 test 文件夹。

- 创建文件 touch

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

- 删除目录、文件 rm(remove)
  - 说明：执行 rm 指令可删除文件或目录，如欲删除目录必须加上参数”-r”，否则预设仅会删除文件。
  - 语法：`rm [-dfirv][--help][--version][文件或目录...]`
  - 参数：
    - `-d或–directory`直接把欲删除的目录的硬连接数据删成 0，删除该目录。
    - `-f或–force`强制删除文件或目录。
    - `-i或–interactive`删除既有文件或目录之前先询问用户。
    - `-r或-R或–recursive`递归处理，将指定目录下的所有文件及子目录一并处理。
    - `-v或–verbose`显示指令执行过程。
  - 实例：`rm -rf fileDir` 递归删除 fileDir 目录下所有文件
