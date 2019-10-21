# Linux 机器时间

> create by afterloe <liumin@ascs.tech>  
> version 2.3  
> MIT License  
> https://blog.csdn.net/u011391839/article/details/62892020/

* 系统时间指的是 Linux Kernel中的时间
* 硬件时间指的是 主板上电池的供电时间

### 查看系统时间的命令
```sbtshell
$ date
Thu Nov 15 16:16:40 CST 2018
```

### 设置系统时间的命令
```sbtshell
# date –set（月/日/年 时：分：秒）
```

### 查看硬件时间命令
```sbtshell
$ hwclock
Thu 15 Nov 2018 08:16:45 AM CST  -0.329713 seconds
```

### 设置硬件时间命令
```sbtshell
# hwclock –set –date = （月/日/年 时：分：秒）
```

> 安装ntpdate工具 设置网络时间
```sbtshell
$ yum -y install ntp ntpdate
```

### 设置系统时间与网络时间同步
```sbtshll
$ ntpdate cn.pool.ntp.org
```

### 将系统时间写入硬件时间
```sbtshell
$ hwclock --systohc
```

关于ntp服务器的问题，可以点击链接查看。
