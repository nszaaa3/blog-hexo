---
title: Docker-1-4-基础命令之Dockerhub

date: 2022-03-01 10:21:48

categories:

- Docker

tags:

- Docker

---

## login

docker login : 登录到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

docker logout : 登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

语法

```shell
docker login [OPTIONS] [SERVER]
```

```shell
docker logout [OPTIONS] [SERVER]
```

参数说明：

- -u :登录的用户名
- -p :登录的密码

实例

登录到Docker Hub

```shell
docker login -u 用户名 -p 密码
```

登出Docker Hub

```shell
docker logout
```

## pull

docker pull : 从镜像仓库中拉取或者更新指定镜像 语法

```shell
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

参数说明：

- -a :拉取所有 tagged 镜像

- --disable-content-trust :忽略镜像的校验,默认开启

实例

从Docker Hub下载nginx最新版镜像。

```shell
docker pull nginx
```

从Docker Hub下载REPOSITORY为nginx的所有镜像。

```shell
docker pull -a nginx
```

## push

docker push : 将本地的镜像上传到镜像仓库,要先登录到镜像仓库

语法

```shell
docker push [OPTIONS] NAME[:TAG]
```

参数说明：

- --disable-content-trust :忽略镜像的校验,默认开启

实例

上传本地镜像mysql57:v1到镜像仓库中。

```shell
docker push mysql57:v1
```

## search

docker search : 从Docker Hub查找镜像 语法

```shell
docker search [OPTIONS] TERM
```

参数说明：

- --automated :只列出 automated build类型的镜像；
- --no-trunc :显示完整的镜像描述；
- -f <过滤条件>:列出收藏数不小于指定值的镜像。

实例

从 Docker Hub 查找所有镜像名包含 java，并且收藏数大于 10 的镜像

```shell
docker search -f stars=10 java
```

参数说明：

- NAME: 镜像仓库源的名称
- DESCRIPTION: 镜像的描述
- OFFICIAL: 是否 docker 官方发布
- stars: 类似 Github 里面的 star，表示点赞、喜欢的意思。
- AUTOMATED: 自动构建。
