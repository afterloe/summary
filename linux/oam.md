# Linux 运维命令

> create by afterloe <lm6289511@gmail.com>  
> version 3.2  
> MIT License

## 挂载硬盘
* 查看硬盘 `fdisk -l`
* 格式化/dev/sda `[root@linuxidc ~]# mkfs.ext4 /dev/sda`
* 新建目录并挂载
```sbt shell
[root@linuxidc ~]# mkdir /hhd
[root@linuxidc ~]# mount /dev/sda /hhd
[root@linuxidc ~]# df -h
```
设置开机时自动挂载:
```sbtshell
[root@linuxidc ~]# blkid # 查看磁盘UUID信息
[root@linuxidc ~]# vi /etc/fstab
UUID=d03dad1a-797d-47a5-92c8-5ec5836d35e2  /hhd  ext4    defaults 0 0
```

## 端口检查命令
`sudo lsof -i:port`  检测端口是否被占用  
eg : `sudo lsof -i:3389`

## ssl
`ssh -NfL 127.0.0.1:8080:172.18.0.4:8080 kini` 打洞链接本地访问172.18.0.4  
`ssh -NfR 172.18.0.4:8080:yyy:8080 kini` 打洞链接172.18.0.访问本地8080服务  
`ssh -NfL 3389:11.1.200.30:3389 root@11.23.3.94 -p 1822`  
`ssh -T git@git.oschina.net` ssh测试  
`ssh-keygen -t rsa -b 4096 -C "lm6289511@gmail.com"` 生成公钥      
`ssh-copy-id -i ~/.ssh/id_rsa.pub afterloe@namo` 复制公钥  
`keytool -genkeypair -alias mytest -keyalg RSA -keypass mypass -keystore mytest.jks -storepass mypass` 生成JKS java 秘钥文件  

## 用户
`usermod -a -G groupA username` 将用户追加进某个组[添加完之后当前窗口需要重新登录刷新session]  
`useradd -s /bin/bash -d /home/afterloe -m afterloe` 创建名为afterloe的用户  
`passwd afterloe` 修改afterloe的密码  

## 给用户添加sudo权限
```
cd /etc
su
chmod u+w ./sudoers
vim ./sudoers

## Allow root to run any commands anywhere
username ALL=(ALL) ALL

chmod u-w ./sudoers
exit
```

## 压缩&解压
### tar.xz
#### 方法一（推荐）
```bash
tar -Jxvf 解压 *.tar.xz -> **
tar -Jcvf 压缩 ** -> *.tar.xz
```
#### 方法二（稳定）
`xz -d *.tar.xz` 解压  
`tar -xf *.tar.xz` 解压  
`tar -xvJf *.tar.xz` 解压  
### rar
`rar x *.rar` 解压  
`apt-get install rar` 安装rar命令  
### zip
`unzip *.zip` 解压  
`zip -q -r -e -m -o yourName.zip zipfile` 压缩
> -q ：不显示压缩进度状态  
-r ：子目录子文件全部压缩为zip  //不然的话只有"zipfile list''文件夹被压缩，里面内容没有被压缩进去  
-e ：压缩文件需要加密，终端会提示你输入密码的 //zip -r -P test password.zip "zipfile list'' 直接用'test'来加密password.zip 。  
-m ：压缩完删除原文件  
-o ：设置所有被压缩文件的最后修改时间为当前压缩时间

### gzip
`gzip -d ` 压缩  
`gzip -r ` 解压  
### tar
`tar -czvf sub.tar.gz -C ./ $(ls)` 压缩当前目录
`tar -czvf yourName.tar.gz tarfile` 压缩  
`tar -xzvf yourName.tar.gz` 解压  
```WORKPATH=$PWD && tar -czvf $(basename `pwd`).tar.gz -C $WORKPATH $(ls $WORKPATH)``` 压缩当前目录下的所有文件到改名字下的tar包  
```WORKPATH=$PWD && tar -czvf $(basename `pwd`).tar.gz -C $WORKPATH $(ls $WORKPATH) && mv $(basename `pwd`).tar.gz ../../```  
`tar -tvf ${tar name}` 显示tar包下的内容  

## 服务
`systemctl enable docker.service` 服务自启动   
`systemctl disable docker.service` 关闭服务自启动  

## 作业
> 语法：nohup Command [ Arg … ] [　& ]  

`nohup /root/start.sh &` 脚本后台运行  
`nohup command > myout.file 2>&1 &` 提交作业输出日志  

## 线程
`ps aux` 查询所有线程  
`ps aux | grep name` 查询指定名字的线程  
`ps aux | grep pid` 查询指定pid的线程  
`pgrep -u afterloe name` 查询指定用户的线程  
`pgrep name` 查询所有用户的指定名字的线程  
`ps -u afterloe` 查询指定用户的所有线程  
`ps -U root -u root -N` 查询所有非root用户创建的线程  
`ps aux | less | grep name` 查询指定名字的线程
## 系统
`grep "model name" /proc/cpuinfo` 查看cpu信息  
`grep MemTotal /proc/meminfo` 查看内存信息  
`sudo apt-get install speedtest-cli` 网速测试命令安装  
`speedtest-cli` 网速测试  
`chmod abc file` 权限设置  
> 其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。  
r=4，w=2，x=1  
若要rwx属性则4+2+1=7  
若要rw-属性则4+2=6  
若要r-x属性则4+1=5   
 
`vim /etc/sudoers` 添加sudo用户[root 用wq!来退出]  
`netstat -lntp` 查看活动的tcp端口   
`netstat -lnup` 查看活动的udp端口   
## 文件系统
> awk 详情 https://www.cnblogs.com/xudong-bupt/p/3721210.html  

`ln -s {target_path} {save_path}` 创建软链接  
`tail -f file` 查看动态日志文件  
`touch file` 创建文件  
`history` 查看使用过的命令  
`who` 查看所有连接的用户  
`wc file` 输出文件的字节数，单词数  
`wc -l file` 输出文件的行数  
`du -sh ./` 输出目录或文件当前目录大小  
`awk '{print$1}'` 提取字段  

批量替换
```sbtshell
sed -i "s/download.ascs.tech/download.cityworks.cn/g" `grep download.ascs.tech -rl ./`
```

## utf-8字符出错问题
```sbtshell
$ cat>/etc/environment<<EOF
LC_ALL=en_US.UTF-8
LANG=en_US.UTF-8
EOF
```
