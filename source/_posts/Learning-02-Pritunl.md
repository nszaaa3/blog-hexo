---
title: 学习-02-使用Docker搭建容器化Pritunl+RustDesk私人远程桌面（上）

date: 2026-06-25 21:36:00

categories:

- Learning

tags:

- Docker
- Pritunl
- VPN


---

## 目的
首先感谢`TeamView`,`向日葵`,`ToDesk`等一系列优秀的远程控制系统，随着这些优秀软件的免费额度的大幅下降，已经不能满足我个人的远程桌面需求，而我们这个年龄的人，又都是用盗版软件或者白嫖软件涨起来的，故而寻求免费开源的替代方案，本系列分上下两篇，本篇介绍的是开源VPN软件`Pritunl`的使用方法，下篇将介绍开源远程桌面软件`RustDesk`的使用方法；

## 名词解释
- `VPN` 即`Virtual Private Network`，意为虚拟私人网络

## 正篇

### 服务端环境需求
- 公网IP
- 内存 越大越好
- 磁盘空间 越大越好
- CPU核数 越多越好
- Docker

### Pritunl 服务端安装

1. 修改下面文件中你需要修改的端口号和映射地址后，保存到本地，并命名为 docker-compose.yml

```yaml
version: '3.8'

services:
  # 1. MongoDB 数据库服务
  mongo:
    image: mongo:4.4
    container_name: pritunl-mongo
    restart: always
    # 核心：因为 pritunl 共享了 mongo 的网络，所有的网络端口映射必须全部统一写在 mongo 这里
    ports:
      - "27017:27017"       # MongoDB 默认端口
      - "18128:51194/udp"   # Pritunl VPN UDP 端口
      - "18128:51194/tcp"   # Pritunl VPN TCP 端口
      - "18125:80/tcp"      # Pritunl Web HTTP 界面
      - "18126:443/tcp"     # Pritunl Web HTTPS 界面
      - "18127:9700/tcp"    
    volumes:
#      由于我服务器是Win Server，所以这里用的是Windows 结构 
      - E:\Workspace\Docker\containers\pritunl\mongodb:/data/db
#      Linux 系统请切换至下面结构 
#      - /opt/docker/containers/pritunl/mongodb:/data/db

  # 2. Pritunl 服务
  pritunl:
    image: jippi/pritunl:latest
    container_name: pritunl
    restart: always
    privileged: true
    # 核心：让 pritunl 容器与 mongo 容器融合成同一个网络空间，让 localhost 互通
    network_mode: "service:mongo"
    depends_on:
      - mongo
    volumes:
#      由于我服务器是Win Server，所以这里用的是Windows 结构 
      - E:\Workspace\Docker\containers\pritunl\pritunl:/var/lib/pritunl
#      Linux 系统请切换至下面结构 
#      - /opt/docker/containers/pritunl/pritunl:/var/lib/pritunl
```
2. 执行docker-compose

```shell
# 无论是Windows还是Linux，均通过命令行或终端进入到docker-compose.yml 所在的目录
docker compose up -d
```

如果执行失败，请为docker设置代理或者配置国内镜像源

3. 验证安装结果
```shell
docker ps 
```

### Pritunl 服务端配置
用浏览器打开你的 Pritunl 管理页面： https://[你的公网IP]:18126，首次登录后需要配置用户名和密码。
1. 配置服务器
   1. 点击顶部`Users`，页面切换后，先新增一个`Organization`，再新增一个`User`，新增用户时需要指定组织，且配置密码。完成后，在该页面下载该配置文件
      1. 仅一个用户时，通过VPN连接至服务器后，并不能直接访问服务器资源，所以也得为服务器新增一个用户
   2. 点击顶部`Servers`，页面切换后，点击 `Add Server`，自定`Name`后，将端口号设为51194，即为上面yml里配置的端口号，注意这里的端口号对应docker容器内部，而不是宿主机。服务器创建完成后为该服务器绑定组织
2. 修改配置文件
   1. 打开刚才下载的用户配置文件，修改第58行的`remote`，并保存退出，因为51194是在容器内部的端口，已映射到宿主机的18128端口上
```shell
# 修改前
remote [你的IP] 51194 udp
修改后
remote [你的IP] 18128 udp
```
### Pritunl 客户端配置（Windows）
1. 下载安装[客户端](https://client.pritunl.com/#install)
2. 安装后打开，导入配置文件即可

至此已完成整个安装过程，Pritunl的客户端所在电脑直接已实现服务互访，如nacos配置、数据库等。
