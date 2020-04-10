# Docker 容器

## Docker 安装

1.  [官方文档](https://docs.docker.com/install/linux/docker-ce/centos/)

2.  检查内核版本，必须是 3.10 及以上  
    `uname -r`

3.  安装 docker  
    `yum install docker`

4.  启动 docker  
    `systemctl start docker`

5.  查看 docker 版本  
    `docker -v`

6.  开机启动 docker  
     `systemctl enable docker.service`

7.  停止 docker  
    `systemctl stop docker`

## Docker 常用操作&命令

1. [官方文档](https://docs.docker.com/engine/reference/commandline/docker/)

2. 检索镜像：`docker search 关键字`  
   eg:`docker search redis`  
   搜索镜像信息：[https://hub.docker.com/](https://hub.docker.com/)

3. 拉取镜像：`docker pull 镜像名:tag`  
   eg:`docker pull redis`  
   不写版本号 tag 则默认为最新版本 latest

4. 查看本地镜像：`docker images`

5. 删除指定镜像：`docker rmi image-id`

6. 根据镜像启动容器：`docker run --name mytomcat -d tomcat:latest`

7. 查看运行中的容器：`docker ps`

8. 停止运行中的容器：`docker stop 容器的id`

9. 查看所有容器：`docker ps -a`

10. 启动容器：`docker start 容器id`

11. 删除容器：`docker rm 容器id`

12. 启动一个做了端口映射的 tomcat： `docker run -d -p 8888:8080 tomcat`

    1. `-d`：后台运行
    2. `-p`: Publish a container's port(s) to the host（将主机的端口映射到容器的一个端口）
    3. `8888:8080`：主机端口:容器内部的端口
    4. `-e`：Set environment variables
    5. `-P`：Publish all exposed ports to random ports
    6. `-v`：Bind mount a volume（将 docker 中的配置文件挂在到外部）
    7. `docker run --help`：以上命令都是通过这个命令查看的。

13. 查看容器日志：`docker logs container-name/container-id`

## Docker 之 mysql 镜像

1.  [官方文档](https://hub.docker.com/_/mysql)

2.  根据镜像正常启动 mysql 容器  
    `docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=root -e TZ=Asia/Shanghai -d mysql`

3.  常见问题一：navicat 连接时出现 2059 错误
    1.  `docker exec -it 63c9e29aelef bash`63c9e29aelef 为 mysql 容器 id
    2.  `mysql --user=root --password`
    3.  输入密码 root
    4.  `ALTER USER 'root' IDENTIFIED WITH mysql_native_password BY 'root';`
    5.  `exit` 退出 mysql
    6.  `exit` 退出 bash

## Docker 之 redis 镜像

1.  [官方文档](https://hub.docker.com/_/redis)
