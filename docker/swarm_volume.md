# Docker swarm volume

## install plugin
```sbtshell
$ docker plugin install --grant-all-permissions vieux/sshfs
```

## create volume
```sbtshell
$ docker volume create --driver vieux/sshfs \
-o sshcmd=root@10.10.7.4:/home/volume-docker/db-one \
-o password=0415 \
-o allow_other \
db-one

$ docker volume ls
vieux/sshfs:latest   ssh-volume

$ docker volume inspect ssh-volume
[
    {
        "CreatedAt": "0001-01-01T00:00:00Z",
        "Driver": "vieux/sshfs:latest",
        "Labels": {},
        "Mountpoint": "/mnt/volumes/6209e331848f8ecd07cea15065a5c221",
        "Name": "ssh-volume",
        "Options": {
            "password": "ascs.tech",
            "sshcmd": "dataer@10.10.7.4:/home/volume-docker"
        },
        "Scope": "local"
    }
]
```

## use volume
- postgres

```sbtshell
docker run -d \
--name db-test \
--publish 5432:5432 \
--env POSTGRES_USER=centos \
--env POSTGRES_PASSWORD=user123 \
--env POSTGRES_INITDB_ARGS="max_connections=1000" \
--mount src=db-test,target=/var/lib/postgresql,volume-opt=allow_other \
public/postgres:9.4.5

docker run --rm \
--name db-test \
--volume-driver vieux/sshfs \
--mount src=db-one,target=/var/lib/postgresql,volume-opt=sshcmd=root@10.10.7.4:/home/volume-docker/one,volume-opt=password=0415,volume-opt=allow_other \
postgres:9.6.2 -c 'max_connections=1000'

docker run --rm \
--name db-test \
--volume-driver vieux/sshfs \
--mount src=db-one,target=/var/lib/postgresql,volume-opt=sshcmd=root@10.10.7.4:/home/volume-docker/one,volume-opt=password=0415,volume-opt=allow_other \
postgres:9.4.5 postgres -c 'max_connections=1000'

docker service create \
--replicas 1 \
--name db-two \
--detach=false \
--network soa \
-e POSTGRES_DB=centos \
-e POSTGRES_USER=centos \
-e POSTGRES_PASSWORD=user123 \
--mount type=volume,source=db-two,destination=/var/lib/postgresql/data,volume-driver=vieux/sshfs,volume-opt=sshcmd=root@10.10.7.4:/home/volume-docker/db-two,volume-opt=password=0415,volume-opt=allow_other \
postgres:9.6.2 -c 'max_connections=1000'
```

- fileSystem

```sbtshell
docker service create \
--replicas 1 \
--network cw \
--detach=false \
--name file-system \
--mount type=volume,source=file-system,destination=/tmp/file,volume-driver=vieux/sshfs,volume-opt=sshcmd=root@11.1.1.9:/home/volume-docker/file-system,volume-opt=password=ascs.tech,volume-opt=allow_other \
-e SERVICE_REGISTER_USER=user \
-e SERVICE_REGISTER_PWD=register \
-e SERVICE_DEFAULT_ZONE='http://${developer.user}:${developer.password}@server-center-1:8761/eureka/,http://${developer.user}:${developer.password}@server-center-2:8761/eureka/' \
-e SERVICE_DATA_IP=11.1.1.9 \
-e SERVICE_DATA_NAME=filesystem \
-e SERVICE_DATA_UNAME=ascs \
-e SERVICE_DATA_PWD=ascs.tech \
ascs/file-system:0.1.5
```

## use python plugin to controll docker service

```sbtshell
docker run -d \
--restart=always \
-p 80:5000 \
-v /opt/data/registry:/tmp/registry \
registry
```

```sbtshell
cd ~/deploy
make service args='--service access-control --remove' # 停止服务
make docker-clean
v'--base s --name access-control' # build 镜像 , 不加 --base s 为用 tar包 部署
make service args='--service access-control' # 启动服务
```
