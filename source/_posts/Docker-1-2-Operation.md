---
title: Docker-1.2-基础命令之容器操作

date: 2022-03-01 10:19:48

categories:

- Docker

tags:

- Docker

---

## ps

docker ps : 列出容器 语法

```shell
docker ps [OPTIONS]
```

相关参数：

- -a :显示所有的容器 包括未运行的
- -f :根据条件过滤显示的内容
- --format :指定返回值的模板文件
- -l :显示最近创建的容器
- -n :列出最近创建的n个容器
- --no-trunc :不截断输出
- -q :静默模式 只显示容器编号
- -s :显示总的文件大小

实例

列出所有在运行的容器信息

```shell
docker ps 
```

输出详情介绍：

- CONTAINER ID: 容器 ID
- IMAGE: 使用的镜像
- COMMAND: 启动容器时运行的命令
- CREATED: 容器的创建时间
- PORTS: 容器的端口信息和使用的连接类型（tcp\udp）
- NAMES: 自动分配的容器名称
- STATUS: 容器状态
    - created（已创建）
    - restarting（重启中）
    - running（运行中）
    - removing（迁移中）
    - paused（暂停）
    - exited（停止）
    - dead（死亡）

列出最近创建的5个容器信息

```shell
docker ps -n 5
```

列出所有创建的容器ID

```shell
docker ps -a -q
```

## inspect

docker inspect : 获取容器/镜像的元数据。 语法

```shell
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```

相关参数：

- -f :指定返回值的模板文件
- -s :显示总的文件大小
- --type :为指定类型返回JSON

实例

获取镜像mysql:5.6的元信息。

```shell
docker inspect mysql:5.7
```

获取正在运行的容器mysql57的 IP。

```shell
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysql57
```

## top

docker top :查看容器中运行的进程信息，支持 ps 命令参数。

语法

```shell
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```

容器运行时不一定有/bin/bash终端来交互执行top命令，而且容器还不一定有top命令，可以使用docker top来实现查看container中正在运行的进程。 实例

查看容器mysql57的进程信息。

```shell
docker top mysql57  
```

查看所有运行容器的进程信息。

```shell
for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done
```

## attach

docker attach :连接到正在运行中的容器。

语法

```shell
docker attach [OPTIONS] CONTAINER
```

要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）。

官方文档中说attach后可以通过CTRL-C来detach，但实际上经过我的测试，如果container当前在运行bash，CTRL-C自然是当前行的输入，没有退出；如果container当前正在前台运行进程，如输出nginx的access.log日志，CTRL-C不仅会导致退出容器，而且还stop了。这不是我们想要的，detach的意思按理应该是脱离容器终端，但容器依然运行。好在attach是可以带上--sig-proxy=false来确保CTRL-D或CTRL-C不会关闭容器。
实例

容器mysql57将访问日志指到标准输出，连接到容器查看访问信息。

```shell
docker attach --sig-proxy=false mysql57
```

## events

docker events : 从服务器获取实时事件 语法

```shell
docker events [OPTIONS]
```

相关参数：

- -f ：根据条件过滤事件；
- --since ：从指定的时间戳后显示所有事件;
- --until ：流水时间显示到指定的时间为止；

实例

显示docker 2022年02月02日后的所有事件。

```shell
docker events  --since="2022-02-02"
```

显示docker 镜像为mysql:5.7 2022年02月02日后的相关事件。

```shell
docker events -f "image"="mysql:5.7" --since="2022-02-02"
```

## logs

docker logs : 获取容器的日志 语法

```shell
docker logs [OPTIONS] CONTAINER
```

相关参数：

- -f : 跟踪日志输出
- --since :显示某个开始时间的所有日志
- -t : 显示时间戳
- --tail :仅列出最新N条容器日志

实例

跟踪查看容器mysql57的日志输出。

```shell
docker logs -f mysql57
```

查看容器mysql57从2022年02月02日后的最新10条日志。

```shell
docker logs --since="2022-02-02" --tail=10 mysql57
```

## wait

docker wait : 阻塞运行直到容器停止，然后打印出它的退出代码。

语法

```shell
docker wait [OPTIONS] CONTAINER [CONTAINER...]
```

实例

```shell
docker wait CONTAINER
```

## export

docker export :将文件系统作为一个tar归档文件导出到STDOUT。

语法

```shell
docker export [OPTIONS] CONTAINER
```

相关参数：

- -o :将输入内容写到文件。

实例

将id为a710c90e4c70的容器按日期保存为tar文件。

```shell
docker export -o mysql-`date +%Y%m%d`.tar a710c90e4c70
```

## port

docker port :列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。

语法

```shell
docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]
```

实例

查看容器mynginx的端口映射情况。

```shell
docker port mymysql
```

