# Docker 命令
> create by [afterloe](605728727@qq.com)  
> version is 1.5  
> MIT License  

## 容器与本地系统的文件传递
```
[root@localhost ~]# docker cp --help

Usage:  docker cp [OPTIONS] CONTAINER:PATH LOCALPATH|-
        docker cp [OPTIONS] LOCALPATH|- CONTAINER:PATH

Copy files/folders between a container and the local filesystem
Use '-' as the source to read a tar archive from stdin            [当成流打印]
and extract it to a directory destination in a container.
Use '-' as the destination to stream a tar archive of a            [当成流打印]
container source to stdout.

  --help=false       Print usage
```
### 文件导入
```
[root@localhost ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
24ebe359eb96        26ba6ceb62bf        "/bin/bash"         51 minutes ago      Exited (0) 8 seconds ago                       kickass_ride
[root@localhost ~]# docker start 24ebe359eb96
24ebe359eb96
[root@localhost ~]# docker cp ./anaconda-ks.cfg 24ebe359eb96:/home
[root@localhost ~]# docker attach 24ebe359eb96
ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@24ebe359eb96 /]# cd home/
[root@24ebe359eb96 home]# ls
afterloe  anaconda-ks.cfg
[root@24ebe359eb96 home]#
```
### 文件导出
```
[root@localhost ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
24ebe359eb96        26ba6ceb62bf        "/bin/bash"         51 minutes ago      Exited (0) 8 seconds ago                       kickass_ride
[root@localhost ~]# ls
anaconda-ks.cfg  windchillContainer.tar.gz
[root@localhost ~]# docker cp 24ebe359eb96:/root/foo.js ./
[root@localhost ~]# ls
anaconda-ks.cfg  foo.js  windchillContainer.tar.gz
[root@localhost ~]#
```

### -的用法
```
[root@localhost ~]# docker cp 24ebe359eb96:/root/foo.js -
foo.js0100644000000000000000000000003512627337467010405 0ustar0000000000000000console.log("Hello world!");
```

## 将容器固化成一个镜像
```
[root@docker ~]# docker commit --help

Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

Create a new image from a container's changes

  -a, --author=       Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -c, --change=[]     Apply Dockerfile instruction to the created image
  --help=false        Print usage
  -m, --message=      Commit message
  -p, --pause=true    Pause container during commit
```
```
[root@docker ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
24cd05cf988e        ubuntu              "/bin/bash"         10 seconds ago      Exited (0) 4 seconds ago                       determined_poincare
[root@docker ~]# docker commit -a "afterloe <605728727@qq.cn>" 24cd05cf988e ubuntu:afterloe_nodejs
84a4c05d8bcec5a60b3e77a62ab59ee0346dc051b95467e8bd7d857186e6c7f0
[root@docker ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu              afterloe_nodejs     84a4c05d8bce        7 seconds ago       416 MB
centos              latest              495ee12fbf75        About an hour ago   289.7 MB
ubuntu              latest              e7c599e5c552        41 hours ago        416 MB
<none>              <none>              975b84d108f1        6 weeks ago         960 B
<none>              <none>              fa5be2806d4c        11 weeks ago        0 B
```

## 容器/镜像 导出

### 镜像导出
```
[root@docker ~]# docker save --help

Usage:  docker save [OPTIONS] IMAGE [IMAGE...]

Save an image(s) to a tar archive (streamed to STDOUT by default)

  --help=false       Print usage
  -o, --output=      Write to a file, instead of STDOUT

[root@docker ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              latest              baeba3bfe56c        18 minutes ago      289.7 MB
ubuntu              afterloe_nodejs     84a4c05d8bce        3 days ago          416 MB
ubuntu              latest              e7c599e5c552        4 days ago          416 MB
<none>              <none>              975b84d108f1        6 weeks ago         960 B
<none>              <none>              fa5be2806d4c        11 weeks ago        0 B
```
假设我们要导出的镜像名为centos
```
[root@docker ~]# docker save -o windchillRehose.tar.gz centos
[root@docker ~]# ls
anaconda-ks.cfg  nodejsDevelop.tar.gz  nodejsDockerDev.tar.gz  windchillRehose.tar.gz
```

### 容器导出
```
[root@docker ~]# docker export --help

Usage:  docker export [OPTIONS] CONTAINER

Export a container's filesystem as a tar archive

  --help=false       Print usage
  -o, --output=      Write to a file, instead of STDOUT

[root@docker ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
2f2daa4ba9b8        centos              "/bin/bash"         18 minutes ago      Exited (0) 4 minutes ago                       distracted_hypatia

[root@docker ~]# docker export 2f2daa4ba9b8 > ./windchillContainer.tar.gz
[root@docker ~]# ls -l
```
两个镜像文件不一样大。
```
[root@docker ~]# ls -l
-rw-r--r--. 1 root root 299392512 11月 30 15:48 windchillRehose.tar.gz             【save操作的结果】
-rw-r--r--. 1 root root 245997056 11月 30 15:50 windchillContainer.tar.gz        【export操作的结果】
```
> save     保存的是所有这个镜像的版本记录，包括一些历史数据.[更大]  
> export     导出的是容器当用所用的镜像内容.仅仅只是容器

## 容器/镜像 导入
```
[root@docker ~]# docker import --help
Usage:  docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]

Import the contents from a tarball to create a filesystem image

  -c, --change=[]    Apply Dockerfile instruction to the created image
  --help=false       Print usage
  -m, --message=     Set commit message for imported image

[root@localhost ~]# ls
anaconda-ks.cfg  nodejsDockerDev.tar.gz
[root@localhost ~]# chmod +x nodejsDockerDev.tar.gz
[root@localhost ~]# chmod 777 nodejsDockerDev.tar.gz
[root@localhost ~]# ls
anaconda-ks.cfg  nodejsDockerDev.tar.gz
[root@localhost ~]# docker import -m "nodejs rehose" windchillContainer.tar.gz centos:windchill
a34c303af7ead355c6d34c7a98ae525caa8d530cd17cfe3c0e0242d5f86c47bd
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              windchill           a34c303af7ea        2 minutes ago       238.1 MB
<none>              <none>              d1a04734ca30        About an hour ago   386.7 MB
<none>              <none>              0a85502c06c9        2 weeks ago         187.7 MB
hello-world         latest              975b84d108f1        6 weeks ago         960 B
```

## 进入运行的容器内
```
[root@docker ~]# docker attach --help
Usage:  docker attach [OPTIONS] CONTAINER

Attach to a running container

  --help=false        Print usage
  --no-stdin=false    Do not attach STDIN
  --sig-proxy=true    Proxy all received signals to the process

[root@docker ~]# docker ps -l
CONTAINER ID        IMAGE                    COMMAND             CREATED             STATUS                   PORTS               NAMES
48e6de411476        ubuntu:afterloe_nodejs   "/bin/bash"         2 hours ago         Exited (0) 2 hours ago                       backstabbing_hamilton
[root@docker ~]# docker start 48e6de411476
48e6de411476
[root@docker ~]# docker ps
CONTAINER ID        IMAGE                    COMMAND             CREATED             STATUS              PORTS               NAMES
48e6de411476        ubuntu:afterloe_nodejs   "/bin/bash"         2 hours ago         Up 12 seconds       22/tcp, 8080/tcp    backstabbing_hamilton
[root@docker ~]# docker attach 48e6de411476
root@48e6de411476:/#
```

## 容器试运行
```
[root@docker ~]# docker run --help
Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

  -a, --attach=[]                 Attach to STDIN, STDOUT or STDERR                            #    追加输出，输入，错误输入流
  --add-host=[]                   Add a custom host-to-IP mapping (host:ip)                    #    添加hosts映射
  --blkio-weight=0                Block IO (relative weight), between 10 and 1000            #    IO阻塞大小
  --cpu-shares=0                  CPU shares (relative weight)                                #    cpu占用率
  --cap-add=[]                    Add Linux capabilities                                    #    增加linux 性能
  --cap-drop=[]                   Drop Linux capabilities                                    #    删除部分linux性能
  --cgroup-parent=                Optional parent cgroup for the container                    #    父容器
  --cidfile=                      Write the container ID to the file                        #    将运行之后的容器id写入到文件中
  --cpu-period=0                  Limit CPU CFS (Completely Fair Scheduler) period            #    调整CPU调度时间
  --cpu-quota=0                   Limit CPU CFS (Completely Fair Scheduler) quota            #    调整单块cpu限额
  --cpuset-cpus=                  CPUs in which to allow execution (0-3, 0,1)                #    cpu核数
  --cpuset-mems=                  MEMs in which to allow execution (0-3, 0,1)                #    cpu存储器可使用的数量
  -d, --detach=false              Run container in background and print container ID        #    后台守护运行
  --device=[]                     Add a host device to the container                        #    给容器增加一个主机装置
  --disable-content-trust=true    Skip image verification                                    #    跳过镜像验证
  --dns=[]                        Set custom DNS servers                                    #    设置DNS服务
  --dns-opt=[]                    Set DNS options                                            #    DNS服务选项
  --dns-search=[]                 Set custom DNS search domains                                #    设置DNS搜索域
  -e, --env=[]                    Set environment variables                                    #    设置环境变量
  --entrypoint=                   Overwrite the default ENTRYPOINT of the image                #    重写默认镜像记录点
  --env-file=[]                   Read in a file of environment variables                    #    读入多个环境变量文件
  --expose=[]                     Expose a port or a range of ports                            #    开放多个端口
  --group-add=[]                  Add additional groups to join                                #    加入组进行管理
  -h, --hostname=                 Container host name                                        #    设置容器主机名
  --help=false                    Print usage
  -i, --interactive=false         Keep STDIN open even if not attached                        #    交互模式
  --ipc=                          IPC namespace to use                                        #    使用IPC命名空间
  --kernel-memory=                Kernel memory limit                                        #    调整内核缓存大小
  -l, --label=[]                  Set meta data on a container                                #    在容器内设置源数据
  --label-file=[]                 Read in a line delimited file of labels
  --link=[]                       Add link to another container                                #    增加一个连接到其他的容器
  --log-driver=                   Logging driver for container
  --log-opt=[]                    Log driver options
  --lxc-conf=[]                   Add custom lxc options
  -m, --memory=                   Memory limit                                                #    调整内存大小
  --mac-address=                  Container MAC address (e.g. 92:d0:c6:0a:29:33)            #    容器mac地址
  --memory-reservation=           Memory soft limit
  --memory-swap=                  Total memory (memory + swap), '-1' to disable swap
  --memory-swappiness=-1          Tuning container memory swappiness (0 to 100)
  --name=                         Assign a name to the container
  --net=default                   Set the Network for the container
  --oom-kill-disable=false        Disable OOM Killer
  -P, --publish-all=false         Publish all exposed ports to random ports
  -p, --publish=[]                Publish a container's port(s) to the host
  --pid=                          PID namespace to use
  --privileged=false              Give extended privileges to this container
  --read-only=false               Mount the container's root filesystem as read only
  --restart=no                    Restart policy to apply when a container exits
  --rm=false                      Automatically remove the container when it exits
  --security-opt=[]               Security Options
  --sig-proxy=true                Proxy received signals to the process
  --stop-signal=SIGTERM           Signal to stop a container, SIGTERM by default
  -t, --tty=false                 Allocate a pseudo-TTY
  -u, --user=                     Username or UID (format: <name|uid>[:<group|gid>])
  --ulimit=[]                     Ulimit options
  --uts=                          UTS namespace to use
  -v, --volume=[]                 Bind mount a volume                                        #    容器体积【硬盘大小】
  --volume-driver=                Optional volume driver for the container
  --volumes-from=[]               Mount volumes from the specified container(s)
  -w, --workdir=                  Working directory inside the container                    #    进入容器时所在的路径
```

### 创建指定的hostname的运行容器
```   
[root@localhost ~]# docker run -t -i -h afterloe.cn 8e8e8792a061
```
或
```
[root@localhost ~]# docker run -t -i --hostname=afterloe.cn 8e8e8792a061
[root@afterloe /]# hostname
afterloe.cn
```
### 添加hosts映射
``` 
[root@localhost ~]# docker run -t -i --add-host=market.cn:192.168.1.131 --hostname=afterloe.cn 8e8e8792a061
[root@afterloe /]# more /etc/hosts
172.17.0.2      afterloe.cn afterloe
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.1.131   market.cn
```

### 进入指定的工作目录
```
[root@localhost ~]# docker run -t -i --add-host=market.cn:192.168.1.131 --hostname=afterloe.cn -w /home/afterloe 8e8e8792a061
[root@afterloe afterloe]# pwd
/home/afterloe
[root@afterloe afterloe]#
```
