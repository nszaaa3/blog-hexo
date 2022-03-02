---
title: Docker-1.3-基础命令之rootfs根文件系统

date: 2022-03-01 10:20:48

categories:

- Docker

tags:

- Docker

---

## commit

docker commit :从容器创建一个新的镜像。

语法

```shell
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

相关参数：

- -a :提交的镜像作者；

- -c :使用Dockerfile指令来创建镜像；

- -m :提交时的说明文字；

- -p :在commit时，将容器暂停。

实例

将容器a710c90e4c70 保存为新的镜像,并添加提交人信息和说明信息。

```shell
docker commit -a "YourName" -m "ContainerName" a710c90e4c70 mysql57:v1
```

## cp

docker cp :用于容器与主机之间的数据拷贝。

语法

```shell
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
```

```shell
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

相关参数：

- -L :保持源目标中的链接

实例

将主机/www/work目录拷贝到容器02e9b5358cc5的/www目录下。

```shell
docker cp /www/work 02e9b5358cc5:/www/
```

将主机/www/work目录拷贝到容器02e9b5358cc5中，目录重命名为www。

```shell
docker cp /www/work 02e9b5358cc5:/www
```

将容器02e9b5358cc5的/www目录拷贝到主机的/tmp目录中。

```shell
docker cp  02e9b5358cc5:/www /tmp/
```

## diff

docker diff : 检查容器里文件结构的更改。 语法

```shell
docker diff [OPTIONS] CONTAINER
```

实例

查看容器mysql57的文件结构更改。

```shell
docker diff mysql57
```
