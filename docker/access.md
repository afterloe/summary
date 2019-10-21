# docker daemon 远程访问

> create by afterloe <lm6289511@gmail.com>  
> version 1.0  
> apache License  
> https://forums.docker.com/t/expose-the-docker-remote-api-on-centos-7/26022

## 修改systemd 配置文件
* Ubuntu: vim /lib/systemd/system/docker.service
* Centos: vim /etc/systemd/system/docker.service.d/docker.conf

```sbtshell
...
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:12375 -H unix://
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always
...
```

## 重新启动docker.service
```sbtshell
systemctl daemon-reload
systemctl restart docker.service
```

## 测试
```sbtshell
curl -X GET http://127.0.0.1:12375/images/json
```
```json
      
[
    {
        "Containers":-1,
        "Created":1541202637,
        "Id":"sha256:d817ad5b9beb8ed09a78819c7d7627679c89a4aca36a3b2d47760695d49d09a0",
        "Labels":null,
        "ParentId":"",
        "RepoDigests":[
            "golang@sha256:05f8812f1a3e1c9ce44c5a0ba462d1a6d75a0b89abbb2f86b2e02efeda85ce1e"
        ],
        "RepoTags":[
            "golang:latest"
        ],
        "SharedSize":-1,
        "Size":774299167,
        "VirtualSize":774299167
    },
    {
        "Containers":-1,
        "Created":1539653939,
        "Id":"sha256:269ffb1d6de06ceee9dc67f21ce4c9a98c72c64ae75beb717cd3891f6f17c24b",
        "Labels":null,
        "ParentId":"",
        "RepoDigests":[
            "couchdb@sha256:7d60986a0b43015711121909c50b94dd6a1a6520f8f4bc72f52109b9ea8a17ad"
        ],
        "RepoTags":[
            "couchdb:latest"
        ],
        "SharedSize":-1,
        "Size":252239692,
        "VirtualSize":252239692
    }
]
```
