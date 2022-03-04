---
title: Docker-3-容器安装之Nginx并反代Tomcat伪集群实现负载均衡

date: 2022-03-04 11:41:46

categories:

- Docker

tags:

- Docker
- Nginx
- Apache Tomcat

---

## Tomcat

### 创建单机Tomcat

创建一个163默认配置Tomcat并映射到宿主机的8080端口。

```shell
docker run --name tomcat8080 \
-p 8080:8080 \
-v /opt/docker/tomcat8080/:/usr/local/tomcat/webapps \
-d hub.c.163.com/library/tomcat
```

但因为容器启动的顺序不同，会导致容器的IP地址有变动，这样会导致nginx反代不到，因此我们需要给每一个Tomcat容器设置固定IP地址。

### 创建自定义网络类型

本机的Docker默认的网段是`172.17.0.*`，为区别，自定义网段为`172.18.0.*`

```shell
sudo docker network create --subnet=172.18.0.0/16 mynetwork
```

查看网段

```shell
sudo docker network ls
```

![result](/img/docker-network.png)

### 创建多个Tomcat并指定同一网段

```shell
sudo docker run --name tomcat8080 \
-p 8080:8080 \
-v /opt/docker/tomcat8080/webapps:/usr/local/tomcat/webapps \
-d --net mynetwork --ip 172.18.0.100 hub.c.163.com/library/tomcat
```

```shell
sudo docker run --name tomcat8081 \
-p 8081:8080 \
-v /opt/docker/tomcat8081/webapps/:/usr/local/tomcat/webapps \
-d --net mynetwork --ip 172.18.0.101 hub.c.163.com/library/tomcat
 ```

### 拿到两个tomcat容器的IP

```shell
sudo docker inspect tomcat8080 | grep IP
```

```shell
sudo docker inspect tomcat8081 | grep IP
```


TODO
