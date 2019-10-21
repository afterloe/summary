Docker 命令进阶版使用docker部署node开发环境
===
  
> Copyright(c) afterloe. MIT Licensed  
> Version: V0.0.2  
> ModifyTime: 2017-5-4 09:27:05  
> Authors: afterloe <lm6289511@gmail.com> (https://github.com/afterloe)
    
### 登陆容器
```bash
$ docker exec -it container-name/container-id bash
```
### 查看docker容器日志
```bash
$ docker logs container-name/container-id
```

### 采用非rootu用户启动docker
```bash
$ su -

# 添加docker用户组
$ groupadd docker

# 将用户加入到docker用户组
$ gpasswd -a ${USER} docker

# 重启docker服务
$ systemctl restart docker.service
```

### 启动一个docker容器然后输出一句话 ###

```bash
$ sudo docker run ubuntu /bin/echo "hello afterloe! "
hello afterloe!
```

### 挂载数据目录到容器中 ###
--rm = true 表示这个容器运行结束后自动删除  
-v localPath:targetPath 挂载一个本地目录到容器的指定目录  
-w 指定docker运行时的工作目录
```bash
$ sudo docker run --rm=true -i -t --name=ls-volume -v /etc/:/data/ ubuntu ls /data
Hbase.thrift  bin  conf  core  gen-nodejs  handler  hbase  node_modules  unit  view
```
-v localPath:targetPath:ro 设置挂载后的文件是只读 ro-read only
```bash
$ sudo docker run --rm=true -i -t --name=ls-volume-read -v /etc/:/data/:ro ubuntu ls -la /data
total 76
drwxr-xr-x 12 1000 1000  4096 Jan  3  2016 .
drwxr-xr-x 34 root root  4096 Aug 24 13:24 ..
drwxrwxrwx  2 1000 1000  4096 Jan  1  2016 .idea
-rwxrwxrwx  1 1000 1000 24870 Dec 30  2015 Hbase.thrift
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 bin
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 conf
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 core
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 gen-nodejs
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 handler
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 hbase
drwxr-xr-x 35 1000 1000  4096 Jan  3  2016 node_modules
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 unit
drwxr-xr-x  2 1000 1000  4096 Jan  3  2016 view
```

### 将容器内的文件系统共享给其他的容器 ###
--volumes-from containerName 获取指定容器名的目录在新的容器中使用，则可以在新的容器中获取上一个容器的共享目录
```bash
$ sudo docker run -i -t -p 1337:1337 --name=etc_share -v /etc/:/data/ ubuntu mkdir /data/ubuntu_share && /bin/sh -c "while true;do echo hello word; sleep 1; done"
$ sudo docker run --rm=true -i -t --volumes-from ubuntu_share --name=ls_data ubuntu ls -la /data
total 76
drwxr-xr-x 12 1000 1000  4096 Jan  3  2016 .
drwxr-xr-x 34 root root  4096 Aug 24 13:24 ..
drwxrwxrwx  2 1000 1000  4096 Jan  1  2016 .idea
-rwxrwxrwx  1 1000 1000 24870 Dec 30  2015 Hbase.thrift
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 bin
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 conf
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 core
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 gen-nodejs
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 handler
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 hbase
drwxr-xr-x 35 1000 1000  4096 Jan  3  2016 node_modules
drwxr-xr-x  2 root root  4096 Jan  3  2016 ubuntu_share
drwxr-xr-x  2 1000 1000  4096 Jan  1  2016 unit
drwxr-xr-x  2 1000 1000  4096 Jan  3  2016 view
```

### 将容器redis依附到其他容器之上
```bash
$ sudo docker run --name redis-server -d redis redis-server --appendonly yes
f8b58b6dff270427ed3da05068f7a15edb54447892d9b206413344442af0b745
$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
f8b58b6dff27        redis               "docker-entrypoint.sh"   5 minutes ago       Up 5 minutes        6379/tcp            redis-server
$ sudo docker run --rm=true -i -t --link redis-server:redis redis /bin/bash
root@fafb08292e54:/data$ ls
root@fafb08292e54:/data$ env
REDIS_ENV_REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-3.2.3.tar.gz
REDIS_PORT_6379_TCP_PROTO=tcp
HOSTNAME=fafb08292e54
REDIS_ENV_REDIS_DOWNLOAD_SHA1=92d6d93ef2efc91e595c8bf578bf72baff397507
REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-3.2.3.tar.gz
TERM=xterm
REDIS_NAME=/infallible_booth/redis
REDIS_PORT_6379_TCP_ADDR=172.17.0.2
REDIS_PORT_6379_TCP_PORT=6379
REDIS_ENV_GOSU_VERSION=1.7
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/data
REDIS_PORT_6379_TCP=tcp://172.17.0.2:6379
SHLVL=1
HOME=/root
REDIS_PORT=tcp://172.17.0.2:6379
REDIS_VERSION=3.2.3
REDIS_DOWNLOAD_SHA1=92d6d93ef2efc91e595c8bf578bf72baff397507
REDIS_ENV_REDIS_VERSION=3.2.3
GOSU_VERSION=1.7
_=/usr/bin/env

root@fafb08292e54:/data# redis-cli -h "$REDIS_PORT_6379_TCP_ADDR" -p "$REDIS_PORT_6379_TCP_PORT"
172.17.0.2:6379> set a 1
OK
172.17.0.2:6379> get a
"1"
```

### 将redis作为系统公共服务
```bash
$ docker run -p 172.17.0.1:6379:6379 --name redis-server -d redis redis-server --appendonly yes
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                       NAMES
5b1fea790e8d        redis               "docker-entrypoint.sh"   6 minutes ago       Up 6 minutes        172.17.0.1:6379->6379/tcp   redis-server
$ docker run --name mongo-server -d mongo
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                       NAMES
2e673610a26b        mongo               "/entrypoint.sh mongo"   2 minutes ago       Up 2 minutes        27017/tcp                   mongo-server
5b1fea790e8d        redis               "docker-entrypoint.sh"   6 minutes ago       Up 6 minutes        172.17.0.1:6379->6379/tcp   redis-server
$ docker run --name test_link --rm=true --link mongo-server:mongo -i -t node:v6.4.0 /bin/bash
root@b14ae73bdec7:/# node -v
v6.4.0
root@b14ae73bdec7:/# npm -v
3.10.3
root@b14ae73bdec7:/# env
NODE_VERSION=6.4.0
MONGO_ENV_MONGO_MAJOR=3.2
HOSTNAME=b14ae73bdec7
TERM=xterm
MONGO_PORT=tcp://172.17.0.3:27017
NPM_CONFIG_LOGLEVEL=info
MONGO_ENV_MONGO_VERSION=3.2.9
MONGO_PORT_27017_TCP=tcp://172.17.0.3:27017
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/
MONGO_PORT_27017_TCP_PROTO=tcp
MONGO_ENV_GPG_KEYS=DFFA3DCF326E302C4787673A01C4E7FAAAB2461C     42F3E95A2C4F08279C4960ADD68FA50FEA312927
MONGO_ENV_GOSU_VERSION=1.7
SHLVL=1
HOME=/root
MONGO_PORT_27017_TCP_ADDR=172.17.0.3
MONGO_NAME=/test_link/mongo
MONGO_PORT_27017_TCP_PORT=27017
_=/usr/bin/env
```

这样node的app就可以使用mongodb 和 redis进行操作了。  
```bash
$ redis-cli -h 172.17.0.1 -p 6379 
$ mongo -h $MONGO_PORT_27017_TCP_ADDR -p MONGO_PORT_27017_TCP_PORT
```
