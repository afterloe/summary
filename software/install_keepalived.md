# Keepalived 安装与配置

```
sudo yum install -y keepalived:
```

主机配置
```
! Configuration File for keepalived

global_defs {
    notification_email {
        docker@cityworks.cn
    }
    notification_email_from docker@cityworks.cn
    smtp_server 127.0.0.1
    smtp_connect_timeout 30
    router_id docker-keepalived
}

vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 20
    priority 150
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass keepalived.awppas.cn
    }

    virtual_ipaddress {
        192.168.3.20/24
    }
}
```

备选机配置
```
! Configuration File for keepalived

global_defs {
    notification_email {
        docker@cityworks.cn
    }
    notification_email_from docker@cityworks.cn
    smtp_server 127.0.0.1
    smtp_connect_timeout 30
    router_id docker-keepalived
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 20
    priority 100
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass keepalived.awppas.cn
    }

    virtual_ipaddress {
        192.168.3.20/24
    }
}
```

双vip配置
```sbtshell
! Configuration File for keepalived

global_defs {
    notification_email {
        docker@cityworks.cn
    }
    notification_email_from docker@cityworks.cn
    smtp_server 127.0.0.1
    smtp_connect_timeout 30
    router_id docker-keepalived
}

!vrrp_sync_group VG_1 {
!    group {
!        VI_1
!    }
!}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 20
    priority 150
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass keepalived.cityworks.cn
    }

    virtual_ipaddress {
        11.1.1.10/16
    }
}

vrrp_instance VI_2 {
    state MASTER
    interface eth1
    virtual_router_id 20
    priority 150
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass keepalived.cityworks.cn
    }

    virtual_ipaddress {
        192.11.3.7/24
    }
}

```
