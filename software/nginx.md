# centos7 nginx

> create by afterloe<lm6289511@gmail.com>  
> version: 1.0  
> MIT License  

## 导入 Nginx yum 源

```sbtshell
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

## yum 安装

```sbtshell
sudo yum install nginx -y
```

## 开机启动
```sbtshell
sudo systemctl enable nginx.serivce
sudo systemctl start nginx.service
```

Bug && 使用
-----

# Socket 转发
可以在 `/etc/nginx/conf/nginx.conf` 里进行配置。需要注意的是不能在http进行配置，如果在http进行配置是需要安装额外的插件的。所以在这个文件进行配置

```sbtshell
stream {
    server{
        listen 8040;
        proxy_connect_timeout 20s;
        proxy_timeout 5m;
        proxy_pass 11.23.3.102:8040;
    }
}
```

# Permission denied
在nginx 使用`systemctl start nginx.service`的时候出现权限失败的问题，首先检查一下文件的属性，最少是`600`以上，同时用户需要需要`root`毕竟`443`和`80`是要权限启动的。如果还是出现权限问题可以检查下`selinux`问题。

```sbtshell
$ sudo setsebool -P httpd_can_network_connect 1
```
如果还是不行，就采用终极一点的方案
```sbtshell
$ sudo setenforce 0
$ sudo systemctl start nginx.service
```

# Systemctl 中对 nginx的限制
有的时候要控制ngnix在linux启动的时候自动启动，通常会使用`systemctl`来控制。但是如果做了自己自定义的配置后，使用这种方式启动就会出现`Permission denied`的提示，所以处理这种异常除了上面哪一种还有一种方式就是修改selinux的配置，让他能够支持我们的nginx自定义配置。

- 配置nginx开机启动
```sbtshell
$ sudo systemctl enable nginx.service
```

- 如果nginx中的监听的端口不是80的话需要再selinux的端口配置中加上端口
```sbtshell
$ sudo semanage porl -l | grep http_port
http_port_t                    tcp      80
$ sudo semanage port -a -t http_port_t -p tcp 8040
success
$ sudo semanage porl -l | grep http_port
http_port_t                    tcp      80, 8040
```

- 端口配置完成之后，再次启动nginx的时候就不会提示端口开启权限失败的问题了。
```sbtshell
$ sudo systemctl start nginx.service
```

# 配置SSL
```sbtshell
# openssl genrsa -out ryans-key.pem 2048
# openssl req -new -sha256 -key ryans-key.pem -out ryans-csr.pem
# openssl x509 -req -days 3650 -in ryans-csr.pem -signkey ryans-key.pem -out ryans-cert.pem

# vim nginx.conf

...
    listen       443 ssl;
    server_name  cw.dnfy.cn;

    ssl_certificate /data/data-1/cw.dnfy.com/ssl-key/ryans-cert.pem;
    ssl_certificate_key /data/data-1/cw.dnfy.com/ssl-key/ryans-key.pem;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    charset utf-8;
...

# vim default.conf

...
    listen       80;
    server_name  cw.dnfy.cn;
    rewrite ^(.*) https://$server_name$1 permanent;
...
```
