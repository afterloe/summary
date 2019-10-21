# centos7 mysql 安装指南

> author: afterloe  
> mail: lm6289511@gmail.com  
> version: 0.0.1

## Centos7 rpm 安装 mysql 5.7

### 下载mysql 5.7 rpm包
```bash
$ wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
$ sudo yum localinstall mysql57-community-release-el7-8.noarch.rpm
```

### 检测mysql源是否安装成功
```bash
$ sudo yum repolist enabled | grep "mysql.*-community.*"

mysql-connectors-community/x86_64        MySQL Connectors Community           36
mysql-tools-community/x86_64             MySQL Tools Community                47
mysql57-community/x86_64                 MySQL 5.7 Community Server          187

```

## 安装mysql 5.7
```bash
$ sudo yum install mysql-community-server -y
$ sudo systemctl start mysqld.service
$ sudo systemctl status mysqld.service
```

### 配置开机自启动
```bash
$ sudo systemctl enable mysqld
$ sudo systemctl daemon-reload
```

## 使用
mysql 5.7 rpm安装之后root是会有随机密码的，所以进一步配置之前，线修改root的密码

### 获取root 默认密码
```bash
$ grep 'temporary password' /var/log/mysqld.log
2017-05-05T01:42:04.813554Z 1 [Note] A temporary password is generated for root@localhost: UogDi0sl54=A
```

### 修改root 密码
```bash
$ mysql -u root -p
$ alter user 'root'@'localhost' identified by 'MyNewPass1!';
```
mysql5.7默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。创建用户的时候也会因为密码位数不够而提示错误，详细的可以参考[这里](http://www.centoscn.com/mysql/2016/0626/7537.html)

## 配置mysql 5.7

### 修改mysql的默认字符集
```bash
$ sudo vim /etc/my.cnf
```
在[mysqld] 下添加以下内容

```
character_set_server=utf8
init_connect='SET NAMES utf8'
validate_password = off
```

内容是设置默认字符集和 创建数据库时默认的字符集，已经关闭用户密码策略.接下来重启服务即可生效
```bash
$ sudo systemctl restart mysqld.service
$ sudo systemctl status mysqld.service
```

### 创建用户和数据库并授权

#### 创建数据库
```bash
$ mysql -u root -p

mysql > CREATE DATABASE `ldap_test` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

#### 创建用户(所有网络都可访问)
```bash
mysql > use mysql;
mysql > create user afterloe;
Query OK, 0 rows affected (0.00 sec)

mysql > alter user 'afterloe'@'%' identified by '123456';
Query OK, 1 row affected, 1 warning (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 1
```

#### 授权
```bash
mysql > grant all on ldap_test.* to 'afterloe'@'%';
Query OK, 1 row affected, 1 warning (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 1
```

#### 刷新
```bash
mysql > flush privileges;
```

#### 测试
```bash
mysql > exit
$ mysql -u afterloe -p

Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 18
Server version: 5.7.18 MySQL Community Server (GPL)
```
