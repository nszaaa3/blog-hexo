---
title: Docker-1.1-基础命令之容器生命周期管理

date: 2022-03-01 10:18:48

categories:

- Docker

tags:

- Docker

---

## run

docker run ：创建一个新的容器并运行一个命令

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

相关参数：

- -a stdin: 指定标准输入输出内容类型 可选 STDIN/STDOUT/STDERR 三项
- -d: 后台运行容器 并返回容器ID
- -i: 以交互模式运行容器 通常与 -t 同时使用
- -P: 随机端口映射 容器内部端口随机映射到主机的端口
- -p: 指定端口映射 格式为 主机(宿主)端口:容器端口
- -t: 为容器重新分配一个伪输入终端 通常与 -i 同时使用
- --name="nginx-lb": 为容器指定一个名称
- --dns 8.8.8.8: 指定容器使用的DNS服务器 默认和宿主一致
- --dns-search example.com: 指定容器DNS搜索域名 默认和宿主一致
- -h "mars": 指定容器的hostname
- -e username="ritchie": 设置环境变量
- --env-file=[]: 从指定文件读入环境变量
- --cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行
- -m :设置容器使用内存最大值
- --net="bridge": 指定容器的网络连接类型 支持 bridge/host/none/container: 四种类型
- --link=[]: 添加链接到另一个容器
- --expose=[]: 开放一个端口或一组端口
- --volume , -v: 绑定一个卷

实例：

使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。

```shell
docker run --name nginx -d nginx:latest
```

使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口。

```shell
docker run -P -d nginx:latest
```

使用镜像 nginx:latest 以后台模式启动一个容器,将容器的 80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data。

```shell
docker run -p 80:80 -v /data:/data -d nginx:latest
```

绑定容器的 8080 端口 并将其映射到本地主机 127.0.0.1 的 80 端口上。

```shell
docker run -p 127.0.0.1:80:8080/tcp ubuntu bash
```

使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。

```shell
docker run -it nginx:latest /bin/bash root@b6253313d675:/#
```

## start/stop/restart

docker start :启动一个或多个已经被停止的容器

docker stop :停止一个运行中的容器

docker restart :重启容器 语法

```shell
docker start [OPTIONS] CONTAINER [CONTAINER...]
```

```shell
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

```shell
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

实例

启动已被停止的容器mysql57

```shell
docker start mysql57
```

停止运行中的容器mysql57

```shell
docker stop mysql57
```

重启容器mysql57

```shell
docker restart mysql57
```

## kill

docker kill :杀掉一个运行中的容器。

语法

```shell
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

相关参数：

- -s :向容器发送一个信号

实例

杀掉运行中的容器mysql57

```shell
docker kill mysql57
```

## rm

docker rm ：删除一个或多个容器。 语法

```shell
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

相关参数：

- -f :通过 SIGKILL 信号强制删除一个运行中的容器
- -l :移除容器间的网络连接 而非容器本身
- -v :删除与容器关联的卷

实例

强制删除容器 db01、db02：

```shell
docker rm -f db01 db02
```

移除容器 nginx01 对容器 db01 的连接 连接名 db：

```shell
docker rm -l db
```

删除容器 nginx01, 并删除容器挂载的数据卷：

```shell
docker rm -v nginx01
```

删除所有已经停止的容器：

```shell
docker rm $(docker ps -a -q)
```

## pause/unpause

docker pause :暂停容器中所有的进程。

docker unpause :恢复容器中所有的进程。 语法

```shell
docker pause CONTAINER [CONTAINER...]
```

```shell
docker unpause CONTAINER [CONTAINER...]
```

实例

暂停数据库容器db01提供服务。

```shell
docker pause db01
```

恢复数据库容器 db01 提供服务。

```shell
docker unpause db01
```

## create

docker create ：创建一个新的容器但不启动它

用法同 docker run 语法

```shell
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

语法同 docker run 实例

使用docker镜像nginx:latest创建一个容器,并将容器命名为nginx

```shell
docker create --name nginx1 nginx:latest
```

## exec

docker exec ：在运行的容器中执行命令

语法

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

相关参数：

- -d :分离模式: 在后台运行
- -i :即使没有附加也保持STDIN 打开
- -t :分配一个伪终端

实例

在指定容器名 nginx1 中以交互模式执行容器内 /root/xxx.sh 脚本:

```shell
docker exec -it mynginx /bin/sh /root/xxx.sh
```

在指定容器名 mynginx 中开启一个交互模式的终端:

```shell
docker exec -i -t mynginx /bin/bash 
```

也可以通过 docker ps -a 命令查看已经在运行的容器 然后使用容器 ID 进入容器。

查看已经在运行的容器 ID：

```shell
docker ps -a
```

... a710c90e4c70 hub.c.163.com/library/mariadb             "docker-entrypoint.sh…"  ...

第一列的 a710c90e4c70 就是容器 ID。

通过 exec 命令对指定的容器执行 bash:

```shell
docker exec -it a710c90e4c70 /bin/bash
```
