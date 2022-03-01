---
title: Docker入门之基础概念

date: 2022-02-20 11:30:04

tags:

- Docker

category:

- docker

---

## Before

最近入职了新公司，公司大发慈悲给配了一台电脑；

——"那么古尔丹，代价是什么？"

——"这一切……"

电脑上的所有操作全部被监控了，一堆软件不让装，相应的也就不能用Google搜索了，我打开了熟悉的度娘，那一刻，人类终于回想起了被[CSDN](/hexo/img/bazinga.gif)支配的恐怖。

抛开充满抄袭狗不说，被抄袭的文章还是十年前东拼西凑的东西，单说Docker相关的文章，不知道这些CSDN作者是商量好了还是集体缺心眼，所有文章的第一步都是：

`docker pull centos`

或者是：

`docker run centos`

这样操作是通过Docker创建一个~~CentOS~~虚拟机，再在虚拟机里装需要的软件。

我完全没有嘲笑这样操作的意思，我意思是看不起这种操作，连看一眼的价值都没有，这些人是想告诉你：我会创建虚拟机，并且会通过虚拟机安装软件。这样做完全违背了Docker的便捷性，
如果说这样操作有一点点符合Docker精神，那就是你可以很方便地通过一个命令删掉所有你用的软件：

`docker rm centos`

## What

为什么这样说，我们先来看看Docker的标识，如下图：

![docker](img/docker.gif)

我们再来学习几个英语单词：

|     英文    |   中文   |
| :-------: | :----: |
|   docker  |  码头工人  |
| container | 集装箱/容器 |

官方构图很形象，每一个容器就是一个集装箱，集装箱里是你需要的内容；

那么码头工人的任务是：创建、搬运、打开、操作、关闭、移除集装箱（容器）；

由此可见，每一个容器应当只盛装特定的某一类或某一种的内容，如此说来，我为上面说的看不起 `docker pull centos` 道歉，因为~~CentOS~~的确盛装了很多东西，这些东西统称为S-H-I-T。

## Why

因为Docker发展到今天，市场上已经有无数可以直接拉取直接使用的Docker容器，对比以下两段代码：

``` shell
# Installing MySQL in a CentOS container
sudo docker pull centos
sudo docker run -it --name centos7 -p 3306:3306 --privileged=true centos:latest /usr/sbin/init
sudo docker exec -it centos7 bash
yum update
yum install mysql-server
```

```shell
# Installing a MySQL container
docker run -p 3306:3306 --name mysql57 mysql
```

这两段代码完成了同样的工作——创建一个docker容器，并安装好MySQL数据库，安装下载MySQL的时间还不计在内。

## How

下面是Docker的安装教程：

### Linux

```shell
# step1: Download Installing script
curl -fsSL get.docker.com -o get-docker.sh
# step2: Install
sh get-docker.sh --mirror Aliyun
```

### Docker-desktop

适配于Windows系统和Mac系统的桌面版Docker的官方下载地址：

[Docker-desktop Download](https://www.docker.com/products/docker-desktop)

手动安装完成之后会自动配置系统环境变量。

现在可以拉取一个Hello-World镜像来试验Docker是否安装完成:

```shell
docker run hello-world
```



