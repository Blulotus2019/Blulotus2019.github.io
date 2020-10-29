## 杀死指定进程
- 查看所有端口使用情况：`netstat -ano`
- 查找特定端口号：`netstat -ano |findstr "端口号"`
- 查看对应进程名称（进程id步骤2能查到）：`tasklist |findstr "进程id号"`
- 杀死进程：`taskkill /f /t /im "进程id或者进程名称"`