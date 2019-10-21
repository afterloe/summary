# ansible 探坑指南

> create by afterloe <lm6289511@gmail.com>  
> version 0.0.1  
> MIT License

## ansible 命令构成

**ansible -m 模块名字 -a "模块参数" "执行模块的组"**  
默认 all

> 模块可以在 http://docs.ansible.com/ansible/latest/modules_by_category.html 看到

## 执行命令

ansible -m command -a "命令" "组"

```sbtshell
$ ansible -m command -a "ls ~" "cynomy"

namo | SUCCESS | rc=0 >>
jdk1.8.0_112
service-center.tar.gz

joe | SUCCESS | rc=0 >>
jdk1.8.0_112
service-center.tar.gz

kini | SUCCESS | rc=0 >>
jdk1.8.0_112
service-center.tar.gz

grace | SUCCESS | rc=0 >>
jdk1.8.0_112
service-center.tar.gz
```

### 增加用户

```sbtshell
$ ansible -m command -a "useradd mark"'test-servers'
$ ansible -m command -a "grep mark /etc/passwd"'test-servers'
```

### 重定向输出到文件

```sbtshell
ansible -m command -a "df -Th"'test-servers'>/tmp/command-output.txt
```

## 发送文件

ansible -m copy -a "src= dest= " "组"
> http://docs.ansible.com/ansible/latest/copy_module.html#examples

```sbtshell
$ ansible -m copy -a "src=~/service-center.tar.gz dest=~/" "cynomy"

namo | SUCCESS => {
    "changed": true,
    "checksum": "0cede0f1998aff6bf1a3393f70cbe67931b08f74",
    "dest": "/home/afterloe/service-center.tar.gz",
    "failed": false,
    "gid": 1000,
    "group": "afterloe",
    "md5sum": "b07b8e1b07836a73978c7c82e24f6e92",
    "mode": "0664",
    "owner": "afterloe",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 126686720,
    "src": "/home/afterloe/.ansible/tmp/ansible-tmp-1510044099.5-203158584438509/source",
    "state": "file",
    "uid": 1000
}
```
