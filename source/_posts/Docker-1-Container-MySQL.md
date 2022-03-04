---
title: Docker-1、容器安装之MySQL(5.7/8.0)

date: 2022-03-01 11:38:04

categories:

- Docker

tags:

- Docker
- MySQL
- Database

---

OK，我已经把我抄的教程全部删掉了，再从自己在用的容器中摘几个新鲜的出来。 用的源几乎都是国内的docker镜像，所以安装起来飞快。

## MySQL5.7

```shell
docker run -p 3306:3306 \
--name mysql57 \
-v /opt/docker/mysql57/conf.d:/etc/mysql/conf.d \
-v /opt/docker/mysql57/mysql.conf.d:/etc/mysql/mysql.conf.d \
-v /opt/docker/mysql57/logs:/logs \
-v /opt/docker/mysql57/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d hub.c.163.com/library/mysql:5.7
```

- -p [xxxx]:3306 将容器中的3306端口映射到宿主机的xxxx端口
    - p - port 端口
- -v [/xxx/xxx]: /etc/mysql/conf.d 将容器中的/etc/mysql/conf.d目录映射到宿主机的/xxx/xxx目录，在这里我映射到宿主机的/opt/docker/mysql57/conf.d
    - v - volume 卷
- -e xxx=xxx 设定环境变量
    - e - environment 环境
- -d 后台运行容器，并返回容器ID
    - d - detach 分离

在上面有4个 -v 参数，分别将容器中的两个配置目录、一个日志目录、 一个数据目录映射到宿主机指定的目录里，这样即使容器因为环境崩溃时，我们也可以在这里修改、查看、操作容器的配置、数据

通过此法创建的MySQL创建完成后，还有两个后续操作：

- 修改全局默认编码方式为utf8
- 取消大小写敏感

因为已经有上面的映射操作，我们就可以在本地直接修改

- 用vim编辑mysql.cnf文件
  ```bash
  vim /opt/docker/mysql57/conf.d/mysql.cnf
  ```

  添加如下内容

  ```shell
  [mysql]
  default-character-set=utf8
  ```

- 用vim编辑mysqld.cnf文件 添加如下内容
  ```shell
  [mysqld]
  character-set-server=utf8
  lower_case_table_names=1
  ```

## MySQL8.0

```shell
docker run -p 3306:3306 \
--name mysql80 \
-v /opt/docker/mysql80/conf:/etc/mysql/conf.d \
-v /opt/docker/mysql80/logs:/logs \
-v /opt/docker/mysql80/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d hub.c.163.com/library/mysql:8.0 \
--lower_case_table_names=1 \
--character-set-server=utf8
```

Well，创建MySQL8.0容器的时候可以直接指定大小写敏感和默认编码方式，更多的是因为MySQL8.0我玩得还不是很溜，按照MySQL5.7那样修改时，不会生效，所以找到了这个方法。我在创建MySQL5.7容器的时候咋没看到这样的好方法？

## 后续操作

因为Linux上的MySQL5.7和MySQL8.0的root账户默认不允许远程连接，也不建议远程连接，所以需要进入到MySQL容器内部去创建一个新用户并授权远程操作。

```shell
sudo docker exec -it mysql57 bash
#或者
sudo docker exec -it mysql80 bash
```

```shell
#登录MySQL
mysql -u root -p123456
#修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
#添加远程登录用户
CREATE USER 'new_user'@'%' IDENTIFIED WITH mysql_native_password BY 'new_password';
#给予权限
#MySQL 5.7版本：
GRANT ALL PRIVILEGES ON *.* TO 'new_user'@'%';
#MySQL 8.0版本：
GRANT ALL PRIVILEGES on *.* to 'new_user'@'%' WITH GRANT OPTION;
```

修改保存完成之后，重启容器即可生效。

现在可以用远程工具来连接MySQL了。

如果宿主机没有安装MySQL，所以需要安装mysql-client或者mycli。

