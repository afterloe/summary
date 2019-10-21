# centeros7 网络相关

> create by afterloe <liumin@ascs.tech>  
> version 2.3  
> MIT License

最近使用centos7 关于网络探坑比较多，故写下这篇文档提供以后查看和使用

## 防火墙
### 临时路由管理
- 添加路由
```sbtshell
$ sudo route add -net 10.227.97.11 netmask 255.255.255.0 gw 10.227.96.22
```
- 删除路由
```sbtshell
$ sudo route delete -net 10.227.97.11 netmask 255.255.255.0 gw 10.227.96.22
```

### 永久路由
修改 `/etc/sysconfig/network-scripts/route-ens33` 
其中 ens33 应该是当前服务器的网卡名称 
```
# 风格一

192.168.0.0/24 via 172.16.0.1
0.0.0.0/0 via 172.16.10.2 dev eth0

# 风格二
# 每三行定义一条路由
#     ADDRESS#=TARGET   #表示数字
#     NETMASK#=mask
#     GATEWAY#=GW

ADDRESS0=192.16.20.0
NETMASK0=255.255.255.0
GATEWAY0=172.16.0.1
```
2种风格不能混合使用，都要`systemctl restart network.service`重启网络服务后生效

### 查看所有开放端口

```sbtshell
$ sudo firewall-cmd --list-ports

4789/udp 2377/tcp 7946/udp 7946/tcp 4789/tcp 8761/tcp
```

### 开放端口

#### 开放tcp端口

```sbtshell
$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent

success
```

#### 删除端口
```sbtshell
$ sudo firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

#### 开放udp端口

```sbtshell
$ sudo firewall-cmd --zone=public --add-port=7946/udp --permanent

success
```

> 命令含义：  
> --zone #作用域  
> --add-port=80/tcp  #添加端口，格式为：端口/通讯协议  
> --permanent   #永久生效，没有此参数重启后失效  
 
### 重启防火墙
 
```sbtshell
$ sudo firewall-cmd --reload

success
```

### 参看防火墙状态
```sbtshell
$ sudo firewall-cmd --state

success
```

### 防火墙相关服务

```sbtshell
$ sudo systemctl stop firewalld.service
$ sudo systemctl start firewalld.service
$ sudo systemctl restart firewalld.service
$ sudo systemctl disable firewalld.service
$ sudo systemctl enabl firewalld.service
```

### selinux 端口处理
xw
查询selinux 关于 sshd 的端口列表
```sbtshell
$ semanage port -l | grep ssh
ssh_port_t                     tcp      1822, 22
```

添加端口到 sshd
```sbtshell
$ semanage port -a -t ssh_port_t -p tcp 1822
$ semanage port -a -t http_port_t -p tcp 801 
```

## 静态ip & 设置dns

### 查询本机网卡和网络连接信息
```sbtshell
$ sudo nmcli connection show
NAME    UUID                                  TYPE            DEVICE 
ens160  6fe650e9-7240-4d5c-8aab-ed05db26c68f  802-3-ethernet  ens160 
virbr0  a96edd6c-9aa1-49de-990b-767819271d16  bridge          virbr0 
```

### 设置网卡dns
```sbtshell
$ sudo nmcli con mod ens160 ipv4.dns "114.114.114.114 8.8.8.8"
```

### 重启网卡
```sbtshell
$sudo nmcli con up ens160
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/3)
```

### 静态ip
```sbtshell
$ cd /etc/sysconfig/network-scripts/
$ vim ifcfg-${network-name}

BOOTPROTO=static #链接方式 dhcp改成static
DEVICE=ens160::q:A
ONBOOT=yes # 开机自连
IPADDR0=192.168.2.21 # 静态ip
GATEWAY0=192.168.2.1 # 网关
DNS1=192.168.2.1 # dns信息
NETMASK=255.255.255.0 # 子网掩码

$ sudo ifup ifcfg-${network-name}
$ sudo ifdown ifcfg-${network-name}
```
