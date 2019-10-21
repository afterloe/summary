# docker swarm 探坑
> create by afterloe <lm6289511@gmail.com>  
> version 1.0  
> MIT License  
> https://www.centos.bz/2017/01/docker-service-create/

## docker swarm 不停机维护
```sbtshell
$ systemclt restart docker.service # 重启docker服务
$ docker service update --image nginx:alpine nginx # 更新nginx的镜像服务
```

## docker swarm 目录挂载

bind 模式

```sbtshell
--mount type=bind,source=/afterloe/neo4j/database-permission,destination=/data \
```

volume 模式

```sbtshell
 --mount type=volume,destination=/path/in/container \
```

## docker swarm 搭建
> 主要解决跨主机容器之间通讯的问题

### 确定docker 版本
```sbtshell
$ ansible -m command -a "docker info" cynomys
namo | SUCCESS | rc=0 >>
Containers: 3
 Running: 2
 Paused: 0
 Stopped: 1
Images: 3
Server Version: 17.10.0-ce-rc1
```

### 加入docker集群

`docker swarm init`

```
docker swarm join --token SWMTKN-1-1c0s0lwpxpvu8u9ja3xx1u7k62lwtk8nvm27imsjpe22oadihx-3xp6jti7nyuotihml6gyjbv8a 10.10.7.4:2377
```
`ansible -m command -a "docker swarm join --token SWMTKN-1-1c0s0lwpxpvu8u9ja3xx1u7k62lwtk8nvm27imsjpe22oadihx-3xp6jti7nyuotihml6gyjbv8a 10.10.7.4:2377"
`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

`docker node ls`

```
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
nvkfdjjupsu81ev5jkbzqy9lh     grace.localdomain   Ready               Active
sa48ujxu4aj7qm4n61xkkkkp4     joe.localdomain     Ready               Active
tapl6ybe99vfnsq3zfknpoka2     kini.localdomain    Ready               Active
ov84hda1zayqcb9ehs29zc6os     namo.localdomain    Ready               Active
phcegz5070snq2cfb9hhxoz1z *   yyy.localdomain     Ready               Active              Leader
```

### 创建网卡

```sbtshell
$ docker network create --driver=overlay --subnet=132.6.0.0/16 --gateway=132.6.1.1 soa

$ docker network ls

NETWORK ID          NAME                DRIVER              SCOPE
5bc9771efc77        bridge              bridge              local
7ebd27e29da0        docker_gwbridge     bridge              local
4c9005a211f0        host                host                local
xl5y4ylvxd7u        ingress             overlay             swarm
3564a5a56850        none                null                local
gznneqfe6wln        soa                 overlay             swarm
```

### 开放docker 端口
docker 的官方文档相当的坑爹，如果没有一定的实践经验会被坑死。docker swarm 要想做到每个单独的容器能够用dns+端口的形式来访问，就要开启3个端口  

- 2377 /tcp 负责组建swarm集群网络
- 4789 /tcp /udp 负责swarm容器内网络通讯, 不开则会导致容器端口访问失败的问题
- 7946 /tcp /udp 负责swarm容器内网络通讯，不开则会导致容器无法ping通

#### 开放swarm 集群端口

`ansible -m command -a "firewall-cmd --zone=public --add-port=2377/tcp --permanent" cynomys`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

#### 开放swarm dns端口

`ansible -m command -a "firewall-cmd --zone=public --add-port=7946/tcp --permanent" cynomys`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

`ansible -m command -a "firewall-cmd --zone=public --add-port=4789/tcp --permanent" cynomys`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

#### 开放swarm route端口

`ansible -m command -a "firewall-cmd --zone=public --add-port=7946/udp --permanent" cynomys`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

`ansible -m command -a "firewall-cmd --zone=public --add-port=4789/udp --permanent" cynomys`

```
namo | SUCCESS | rc=0 >>
success

joe | SUCCESS | rc=0 >>
success
```

#### 重启生效
```
ansible -i hostlist docker -m command -a "firewall-cmd --reload"
ansible -i hostlist docker -m command -a "firewall-cmd --list-ports"

192.168.3.23 | SUCCESS | rc=0 >>
7946/tcp 7946/udp 4789/udp 4789/tcp 2377/tcp

192.168.3.24 | SUCCESS | rc=0 >>
7946/tcp 7946/udp 4789/udp 4789/tcp 2377/tcp

192.168.3.22 | SUCCESS | rc=0 >>
7946/tcp 7946/udp 4789/udp 4789/tcp 2377/tcp
```
> 后记

这三个端口发现的过程如下
启动容器后，发现容器之间无法通过dns来ping通，而且每个容器的ip都不是自定义的vip，使用`ansible -m command -a "netstat -lntp" cynomys` 查看每个节点。都有返回了7946这个端口，查询官方文档

```
Port 7946 TCP/UDP for container network discovery.
Port 4789 UDP for the container ingress network.
```

得知这两个端口负责容器的连接，然后开启这两个端口的tcp连接，再次测试后发现swarm容器内能够通过dns来ping通。
```
$ docker exec 3b2cec4b2fe7 ping centos-release
PING centos-release (132.6.0.13): 56 data bytes
64 bytes from 132.6.0.13: seq=0 ttl=64 time=0.090 ms
64 bytes from 132.6.0.13: seq=1 ttl=64 time=0.103 ms
```

但是使用`telnet`是无法通讯的，但是同一台物理机内却是可以通讯，所以判断为端口问题，但是tcp端口都已经开启，所以判断为udp端口没有开放
```
$ telnet server-center-1 8761
Trying 132.6.0.11...
^C
```

使用`ansible -m command -a "netstat -lnup" cynomys`查看各个容器里udp的活动端口

```
namo | SUCCESS | rc=0 >>
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
udp        0      0 127.0.0.1:323           0.0.0.0:*                           866/chronyd
udp        0      0 0.0.0.0:4789            0.0.0.0:*                           -
udp6       0      0 :::7946                 :::*                                29801/dockerd
udp6       0      0 ::1:323                 :::*                                866/chronyd

joe | SUCCESS | rc=0 >>
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
udp        0      0 127.0.0.1:323           0.0.0.0:*                           859/chronyd
udp        0      0 0.0.0.0:4789            0.0.0.0:*                           -
udp6       0      0 :::7946                 :::*                                32842/dockerd
udp6       0      0 ::1:323                 :::*                                859/chronyd
```

发现7946、4789 udp端口也是活动的，也就是说这两个端口和官方文档上描述是不一致的，这两个端口都走tcp 和 udp，所以要在防火墙开放这两个端口的tcp 、udp 规则，最后全部ok

##### 查看Service 启动异常日志
查看日志可以参考 [这里](http://www.bubuko.com/infodetail-2366991.html) 。需要使用两种方式一起，第一中是配合`--no-trunc` 进行不截断展示。再加上`--format`就可以了.
> docker service ps --format "{{.Name}}: {{.Image}}" top

###### --format 参数如下


Placheholder | Description
------------ | ------------
.ID | Task ID
.Name | Task Name
.Image | Task Image
.Node | Task run Node ID
.DesiredState | Desired state of the task (running,?shutdown, or?accepted)
.CurrentState | Current state of the task
.Error | Error
.Ports | Task published ports

使用如下  
```sbtshell
$ docker service ps --no-trunc --format "{{.ID}}: {{.Error}}" vckky1aala48l5970exma5x4l

ea0hawq7fkz8ggoogc89pnojd: "VolumeDriver.Mount: exit status 1%!(EXTRA []interface {}=[])"
z1ifov4pkfjyrtsclcas46qjg: "VolumeDriver.Mount: exit status 1%!(EXTRA []interface {}=[])"
znxh9btixlqd0n28vxno7qt1n: "VolumeDriver.Mount: exit status 1%!(EXTRA []interface {}=[])"
zifbtaf4gsdu50gmag1rlo1fm: "VolumeDriver.Mount: exit status 1%!(EXTRA []interface {}=[])"
ypmwuv72v1bfug65f1r8h4pp3: "VolumeDriver.Mount: exit status 1%!(EXTRA []interface {}=[])"
```
