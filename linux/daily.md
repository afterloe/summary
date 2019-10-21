# 日常篇

> create by afterloe <lm6289511@gmail.com>  
> version 2.3  
> MIT License

## centos

`yum list installed | grep docker` 列出安装过的包  
`yum -y remove docker-engine.x86_64` 卸载指定安装包  

## docker swarm
> https://www.centos.bz/2017/01/docker-service-create/ 

`docker ps -a -q | xargs --no-run-if-empty docker stop` 递归停止服务  
`rm -rf /var/lib/docker` 删除镜像、容器  
`docker service ps --format "{{.Name}}:{{.Error}}" --no-trunc api-gateway` 查看service 异常关闭的原因  

## postgres
`docker run --rm -it docker.ascs.tech/public/postgres:latest /bin/bash` 交互式方式进入docker容器
> psql -d 数据库名 -h ip地址 -p 数据库端口 -U 用户名 -f 文件地址

`pg_dump timeandspace > timeandspace.sql -h 172.19.128.2 -U centos` 导出数据库   
`CREATE DATABASE appmanager OWNER centos;` 创建数据库  

常用命令

```sbtshell
docker run --rm -it docker.ascs.tech/public/postgres:9.6.2 psql -h 172.19.128.2 -U centos
docker run --rm -it docker.ascs.tech/public/postgres:latest psql -h 10.10.6.6 -U sde
```
## mysql
`show variables like 'character%';` 显示字符集  
`show databases;` 显示数据库列表  
`describe 表名;` 显示数据表的结构  
`create database 库名;` 建库  
`mysql -u jwis(用户名) -p(密码)` 登陆mysql  
`alter table test auto_increment = 0` 设置mysql表的自动增长id从0开始  
`mysqldump -u 用户名 -p 数据库名 > 导出的文件名` 导出完整数据库结构和数据  
`mysqldump -u 用户名 -p -d 数据库名 > 导出的文件名` 导出数据库结构  

导入数据

```sbtshell
$ mysql -u afterloe -p
$ mysql> use dapeng
$ mysql> source sqlPath
``` 
## redis
`redis-cli keys "*"| xargs redis-cli del` 删除所有key 无密码  
`redis-cli -a password keys "*"| xargs redis-cli -a password del` 删除所有key 有密码  
## 更新ubuntu中的软件信息
```sbtshell
$ sudo apt-get update
$ sudo apt-get dist-upgrade -y
```
## oracle
管理员登陆

```sbtshell
$ oracle sqlplus /nolog
$ conn /as sysdba
```

```CREATE DATABASE `Insight` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;``` 创建数据库  
`lsnrctl start` 启动监听  
`select *(name) from v$database` 查看当前数据库名称  
`select username from dba_users` 查看数据库用户名  
`select table_name from user_tables;` 当前用户的表  
`select table_name from all_tables;` 所有用户的表  
`select table_name from dba_tables;` 系统表  
`select table_name from dba_tables where owner='用户名'` 指定用户的表  

登陆

```sbtshell
$ su - oracle 
$ sqlplus /nolog
$ conn / as sysdba
$ sqlplus 账号名/密码 as 角色
```

修改open\_cursors

```sbtshell
$ show parameter open_cursors
$ alter system set open_cursors=4000;
```

创建用户和表空间

```sbtshell
$ create temporary tablespace mks_temp tempfile '/home/oracle/u01/oracle/oradata/ora/mks_temp01.dbf' size 32m autoextend on next 32m maxsize 2048m extent management local; 
$ create tablespace mks_data datafile '/home/oracle/u01/oracle/oradata/ora/mks_data01.dbf' size 32m autoextend on next 32m maxsize 2048m extent management local;
$ create user c##mks identified by mKs#123 default tablespace mks_data temporary tablespace mks_temp;
$ grant connect,resource to c##mks; 
$ grant CTXAPP to c##mks;
$ grant CREATE PROCEDURE, CREATE TABLE, CREATE TYPE, CREATE SEQUENCE, CREATE VIEW to c##mks;
$ grant select on ctxsys.CTX_INDEXES to c##mks;
```

## couchdb
`sudo -i -u couchdb nohup couchdb/bin/couchdb > /tmp/couchdb.log 2>&1 &` 后台运行couchdb  
## 卸载openJdk

```sbtshell
$ rpm -qa | grep jdk
$ sudo yum -y remove ${openjdk-version}
$ source ~/.bash_profile
```

## windchill 10.2
给Windchill创建用户表空间

```sbtshell
sqlpush > create tablespace BLOBS datafile '/home/oracle/u01/oracle/oradata/ora/BLOBS01.dbf' size 32m autoextend on next 32m maxsize 2048m extent management local;
sqlpush > create tablespace INDX datafile '/home/oracle/u01/oracle/oradata/ora/INDX01.dbf' size 32m autoextend on next 32m maxsize 2048m extent management local;
sqlpush > create tablespace WCAUDIT datafile '/home/oracle/u01/oracle/oradata/ora/WCAUDIT01.dbf' size 32m autoextend on next 32m maxsize 2048m extent management local;
```
