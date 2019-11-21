# Linux

## 文件操作

- 删除目录、文件 rm(remove)

  - 语法：`rm [-dfirv][--help][--version][文件或目录...]`
  - 参数：
    - `-d或–directory`直接把欲删除的目录的硬连接数据删成 0，删除该目录。
    - `-f或–force`强制删除文件或目录。
    - `-i或–interactive`删除既有文件或目录之前先询问用户。
    - `-r或-R或–recursive`递归处理，将指定目录下的所有文件及子目录一并处理。
    - `-v或–verbose`显示指令执行过程。
  - 实例：`rm -rf fileDir` 递归删除 fileDir 目录下所有文件
