这是java 中 soa 通用包模块的Dockerfile

```
FROM java:8-jre
MAINTAINER afterloe <lm6289511@gmail.com>

EXPOSE 8080
VOLUME /opt/ascs-soa
COPY . /opt/ascs-soa/
COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh / # backwards compat

WORKDIR /opt/ascs-soa
ENTRYPOINT ["docker-entrypoint.sh"]
```

使用脚本入手，可以控制传入传入传出参数。使得docker image 更加灵活。

## USER

```sbtshell
USER <user>[:<group>] or
USER <UID>[:<GID>]
```
指定运行时的用户名或UID，后续的RUN也会使用指定的用户。  
当服务不需要管理权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户  
要临时获取管理权限可以使用gosu，而不推荐sudo。

## WORKDIR

```sbtshell
WORKDIR /path/to/workdir
```
为后续的RUN、CMD、ENTRYPOINT指令配置工作目录。  
可以使用多个WORKDIR指令，后续命令如果参数时相对路径，则会基于之前命令指定的路径。例如  
```
WORKDIR /a  
WORKDIR b  
WORKDIR c  
RUN pwd  
```
则最终路径为/a/b/c  
