# Docker 几种通用镜像启动方式

> create by afterloe <lm6289511@gmail.com>  
> version 1.0  
> apache License  
> Docker images hub 官网 https://hub.docker.com/

- **Use account**  `$ docker login docker.ascs.tech`
- **Pull docker images by personal cloud** `$ docker pull docker.ascs.tech/redis`

## image start way

### Start a redis instance
```sbtshell
$ docker run -d \
--name db-redis \
--restart=always \
--publish 127.0.0.1:6379:6379 \
--volume redis-data:/data \
redis
```
> redis-cli command https://www.cnblogs.com/silent2012/p/5368925.html   
*link redis* `$ docker exec -it db-redis redis-cli`

### Start a neo4j instance
```sbtshell
$ docker run -d \
--name db-neo4j \
--restart=always \
--publish 127.0.0.1:7474:7474 \
--publish 127.0.0.1:7687:7687 \
-v /home/afterloe/data-1/neo4j-data:/data \
neo4j
```

### Start a postgresql instance  
```sbtshll
$ docker run -d \
--name db-postgres \
--restart=always \
--publish 127.0.0.1:5432:5432 \
--env POSTGRES_USER=ascs \
--env POSTGRES_PASSWORD=ascs.tech \
-v /home/afterloe/data-1/postgres-db:/var/lib/postgresql \
postgres -c max_connections=1000
```

### Start a CouchDB instance  

```sbtshell
$ docker run -d \
--name db-couchdb \
--restart=always \
--publish 127.0.0.1:5984:5984 \
-v /home/afterloe/data-1/couchdb-data:/opt/couchdb/data \
couchdb
```

> Using the CouchDB instance  
  管理界面 http://127.0.0.1:5984/_utils  
`$ docker run --name my-couchdb-app --link my-couchdb:couch couchdb`

### Start a Mongo instance  
```sbtshell
$ docker run -d \
--name mongodb \
--restart=always \
--publish 127.0.0.1:27017:27017 \
-v /home/afterloe/data-1/mongodb:/data/db \
mongo
```

### Start a Jenkins instance  
```sbtshell
$ docker run -d \
--name jenkins \
--restart=always \
--publish 8080:8080 \
--publish 50000:50000 \
-v /home/afterloe/data-1/jenkins_home:/var/jenkins_home \
jenkins
```

### Start a mysql instance  
```sbtshell
$ docker run -d \
--name db-mysql \
--restart=always \
--env MYSQL_ROOT_PASSWORD=ascs \
--publishp 3306:3306 \
-v /home/afterloe/data-1/mysql:/var/lib/mysql \
mysql
```
> **using** `docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'`  
> **link mysql**`$ docker run --name some-app --link some-mysql:mysql -d application-that-uses-mysql`

### Start a registry instance  
using -v
```sbtshell
$ docker run -d \
--name docker-registry \
--restart=always \
--publish 80:5000 \
-v /home/afterloe/data-1/docker-registry:/var/lib/registry/ \
registry
```
using volume
```sbtshell
$ docker volume crearte registry
$ docker run -d \
--name docker-registry
--restart=always \
--publish 80:5000 \
-v registry:/var/lib/registry/ \
registry
```
