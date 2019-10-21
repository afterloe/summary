# ansible centos7 安装指南

> create by afterloe <lm6289511@gmail.com>  
> version 0.0.1  
> MIT License

## ansible 安装

```sbtshell
$ sudo yum install ansible -y
$ ansible --version

ansible 2.4.0.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/afterloe/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Nov  6 2016, 00:28:07) [GCC 4.8.5 20150623 (Red Hat 4.8.5-11)]
```

## ansible 配置

### 配置ansible 节点
```sbtshell
$ sudo vim /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.10.7.1		namo
10.10.7.2		kini
10.10.7.3		joe
10.10.7.4		yyy
10.10.7.5		grace

$ sudo vim /etc/ansible/hosts

[cynomy]
namo
kini
joe
grace

[cynomys]
namo
kini
joe
grace
yyy
```

### 生成ansible 公钥

```sbtshell
$ ssh-keygen -t rsa -b 4096 -C "lm6289511@gmail.com"
```

### 复制公钥到节点机上

```sbtshell
$ ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@namo
$ ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@kini
$ ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@joe
$ ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@grace
$ ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@yyy
```

### 测试

```sbtshell
$ ansible all -m ping

namo | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
joe | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
kini | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
grace | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
yyy | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```
