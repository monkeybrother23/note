# 安装

## 检查内核

```shell
# 检查内核版本，必须是3.10及以上
uname -r
```

## remove

```shell
yum remove docker \
           docker-client \
           docker-client-lastest \
           docker-common \
           docker-latest \
           docker-laster-logrotate \
           docker-logrotate \
           docker-engine
```

## yum

```shell
# 安装依赖包
yum install -y yum-utils \
               device-mapper-persistent-data \
               lvm2
```

## config

```shell
# 配置docker仓库
yum-config-manager --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## install

```shell
#
yum list docker-ce --showduplicates | sort -r
# 安装docker
yum install docker-ce-17.12.0.ce
yum install docker-ce docker-ce-cli contained.io
```

## start

```shell
# 启动docker
systemctl start docker
# 设置开机启动docker服务
systemctl enable docker
# 停止docker
systemctl stop docker
```

## info

```shell
# 查看
docker -v

docker pull library/hello-world
docker images
docker run hello-world
```

## 卸载

```shell
systemctl diable docker
systemctl stop docker

yum list installed | grep docker
# 删除相关安装包
yum -y remove docker-ce.x86_64
yum -y remove docker-ce-selinux.noarch
# 删除相关的镜像与容器
rm -rf /var/lib/docke
```

# 镜像

## search

```shell
# 搜索镜像
docker search elasticsearch
```

## pull

```shell
# 下载镜像
# docker pull [options] NAME[:TAG]
  docker pull ubuntu:13.10
```

## images

```shell
# 查看镜像
# docker images [options] [REPOSITORY[:TAG]]
  docker images 
```

## run

```shell
# docker run [options] IMAGE[:TAG] [COMMAND] [ARG..]
  docker run hello-world
```

## rmi

```shell
# 删除镜像
docker rmi d23bdf5b1b1b
# 删除所有镜像
docker rmi $(docker images -q) 
```

# 容器

```shell
# 删除容器
docker rm d23bdf5b1b1b
# 查看运行容器
docker ps
# 查看所有容器
docker ps -a
# stop停止所有容器
docker stop $(docker ps -a -q)
# 删除所有容器
docker rm $(docker ps -a -q) 
#start启动指定容器
docker start mysql01
# 进入容器
docker exec -it mysql01 bash
```

# 日志

```shell
# 实时查看docker容器日志
docker logs -f -t --tail 行数 容器id
# 查看指定时间后的日志，只显示最后100行：
docker logs -f -t --since="2018-02-08" --tail=100 CONTAINER_ID
# 查看最近30分钟的日志:
docker logs --since 30m CONTAINER_ID
```

# 示例

## mysql

```shell
# 下载镜像
docker pull mysql:8
# 运行容器
docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8

#无法远程连接问题
#进入docker mysql数据库修改  
#mysql01是我启动的mysql服务的name
docker exec -it mysql01 bash
mysql -uroot -p123456
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
flush privileges;
```

## redis

```shell
#安装redis
[root@albert ~]# docker pull redis:5
#查看安装镜像
[root@albert ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/redis     5                   a55fbf438dfd        2 weeks ago         95 MB
#使用镜像a55fbf438dfd
[root@albert ~]# docker run -d -p 6379:6379 --name redis01 a55fbf438dfd
```

## rabbitmq

```shell
#下载镜像
docker pull rabbitmq:3-management
#查看镜像
docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
docker.io/rabbitmq        3-management        ac01c753758c        3 days ago          213 MB
# 运行镜像ac01c753758c
docker run -d -p 5672:5672 -p 15672:15672 --name rabbitmq01 ac01c753758c
# 后台测试端口120.79.15.29:15672
```

## elasticsearch

```shell
#下载镜像
docker pull elasticsearch:6.8.0
#查看镜像
docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
docker.io/elasticsearch   6.8.0               d0b291d7093b        7 days ago          895 MB
# 启动镜像d0b291d7093b
[root@albert ~]# docker run -d --name elasticsearch --net albert -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" d0b291d7093b
#测试 localhost:9200
#文档地址
https://www.elastic.co/guide/cn/elasticsearch/guide/current/index-doc.html
```

## postgres

### 安装下载

```shell
# 拉取镜像
[root@VM_0_14_centos ~]# docker pull postgres:11.4
# 启动容器
[root@VM_0_14_centos ~]# docker run --name postgres_demo -e POSTGRES_PASSWORD=postgres -p 54321:5432 -d postgres:11.4
# 进入容器
[root@VM_0_14_centos ~]# docker exec -it postgres_demo  psql -U postgres -d postgres
```

### 基本使用

```shell
--创建用户、数据库、授权
postgres=# create user test_user with password '123456';
postgres=# create database test_db owner test_user;
postgres=# grant all on database test_db to test_user;
--
postgres=# revoke all on database test_db from test_user;
postgres=# drop role test_user;
postgres=# drop database test_db


regexp_replace(trim(to_char(a.mark_no, '999D99')) ,'(?<=\.\d*)0+$|\.0*$','')


-- DROP TABLE cp20.cp_cur_cay_sch_asmt_frame_dtls;

CREATE TABLE cp20.cp_cur_cay_sch_asmt_frame_dtls (
	id bpchar(32) NOT NULL,
	framework_sys_code varchar(14) NOT NULL,
	tier_id varchar(3) NOT NULL,
	item_id varchar(10) NOT NULL,
	item_name varchar(30) NULL,
	term_id varchar(30) NULL,
	parent_item_id varchar(10) NULL,
	version_no int4 NOT NULL,
	created_ts timestamp NOT NULL,
	created_by varchar(14) NOT NULL,
	updated_ts timestamp NOT NULL,
	updated_by varchar(14) NOT NULL,
	delete_ind varchar(1) NOT NULL DEFAULT 'N'::character varying,
	order_no int2 NOT NULL,
	assessment_type varchar(10) NULL,
	percent_no numeric(5,2) NULL,
	tier2_mg_display_ind varchar(1) NULL,
	CONSTRAINT pk_cp_cay_sch_asmt_frame_dtls PRIMARY KEY (id),
	CONSTRAINT fk_cp_cay_sch_asmt_frame_dtls FOREIGN KEY (framework_sys_code) REFERENCES cp20.cp_cur_cay_sch_asmt_frame(framework_sys_code)
);
CREATE UNIQUE INDEX uq_cp_cay_sch_asmt_frame_dtls ON cp20.cp_cur_cay_sch_asmt_frame_dtls USING btree (framework_sys_code, tier_id, item_id, (
CASE
    WHEN ((delete_ind)::text = 'N'::text) THEN '1'::bpchar
    ELSE id
END));
```

# 部署

## java

```shell
docker search java8
docker plull williamyeh/java8
```

## Dockerfile

```shell
# Docker image for springboot file run
# VERSION 0.0.1
# Author: eangulee
# 基础镜像使用java
FROM java:8
# 作者
MAINTAINER eangulee <eangulee@gmail.com>
# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp
VOLUME /tmp 
# 将jar包添加到容器中并更名为app.jar
ADD demo-0.0.1-SNAPSHOT.jar app.jar 
# 运行jar包
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]


# VOLUME 指定了临时文件目录为/tmp。其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp。该步骤是可选的，如果涉及到文件系统的应用就很有必要了。/tmp目录用来持久化到 Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用/tmp作为工作目录
#项目的 jar 文件作为 “app.jar” 添加到容器的
#ENTRYPOINT 执行项目 app.jar。为了缩短 Tomcat 启动时间，添加一个系统属性指向 “/dev/./urandom” 作为 Entropy Source

```

## 制作镜像

```shell
#执行下面命令， 看好，最后面有个"."点！
docker build -t springbootdemo4docker .
docker build -t springbootdemo4docker:latest .
```

## 启动容器

```shell
docker run -d -p 8080:8085 springbootdemo4docke
```

