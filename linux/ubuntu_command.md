# Ubuntu 日常操作

> create by afterloe <lm6289511@gmail.com>  
> https://github.com/afterloe  
> Apache 2.0 license  

sudo命令执行缓慢
```
$ hostname
$ sudo vim /etc/hosts

# 将hostname输出的内容放在 /etc/hosts中第一行，即 127.0.0.1 localhost
127.0.0.1 hostname.domain hostname xxx
```
> 由于ubuntu等linux内核支持远程调用，会和网络有一定管理，sudo执行缓慢的原因可能为近期修改过hostname有关

启动网卡
```
$ sudo ifconfig wlan 0 up 
```

更新ubuntu中的软件信息
```sbtshell
$ sudo apt-get update
$ sudo apt-get dist-upgrade -y
```

设置静态ip
```sbtshell
$ sudo vim /etc/network/interfaces

auto eth0
iface eth0 static
address 192.168.200.100
netmask 255.255.255.128
gateway 192.168.3.1
dns-nameserver 218.85.157.99

$ sudo vim /etc/resolvconf/resolv.conf.d/base

nameserver 218.85.157.99

$ sudo /etc/init.d/networking restart
```

禁用用户桌面
```sbtshell
sudo systemctl set-default multi-user.target
sudo reboot
```

开启用户图形界面
```sbtshell
sudo systemctl set-default graphical.target
sudo reboot
```

查看分区
```sbtshell
sudo fdisk -l
```

查询挂载硬盘信息
```sbtshell
sudo blkid /dev/sda*
```

自动挂载硬盘
```sbtshell
sudo vim /etc/fstab
#<设备文件名称> <挂载目录> <文件系统类型> <文件系统参数><是否备份><开机时自检>
UUID=88069947069936E2 /mnt/data ntfs defaults  0  2 1
sudo reboot
```
* 第一个数字：0表示开机不检查磁盘，1表示开机检查磁盘； 
* 第二个数字：0表示交换分区，1代表启动分区（Linux），2表示普通分区 
* 挂载方式默认 defaults

ubuntu 强制关机导致了系统重新启动进入 initramfs> 界面 ，按照提示修复启动盘
```sbtshell
fsck /dev/sdb1
```
> 一路按y 进行修复然后`reboot`即可。    

ubuntu设置静态ip, 在`/etc/netplan/50-cloud-init.yaml`进行配置
```yml
network:
    version: 2
        ethernets:
                wlp3s0:
                        addresses: [192.168.2.114/24]
                        gateway4: 192.168.2.1
                        dhcp4: no
                        nameservers:
                                addresses: [192.168.2.1, 8.8.8.8, 114.114.114.114]
```
使用`netplan apply`重启网络服务即可  

ubuntu 的systemd 配置在/lib/systemd/system目录下创建服务启动脚本testservice.service

防火墙设置
```sbtshell
# sudo apt-get install ufw -y
# sudo ufw enable
# sudo ufw default deny

# sudo ufw allow 22/tcp
# sudo ufw deny 80/tcp
# sudo ufw deny 82/udp
```

防火墙管理
```sbtshell
# sudo ufw status
# sudo ufw delete allow smtp # 删除允许
```
