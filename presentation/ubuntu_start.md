# 关于Ubuntu 启动失败的问题报告

> create by afterloe <afterloe@qq.com>  
> 2019-9-18  
> version is 1.2  
> MIT License

## 现状描述
Ubuntu提示/tmp为只读目录，无法进行文件创建等操作，重启电脑后电脑进入Ubuntu安全模式，并在首行提示`/dev/sda1 contains a file system with errors, check forced`

## 原因分析
对提示内容描述，初步预计为启动盘或挂载时出现问题，开始检查硬盘
```bash
$ mount
```
检查后发现磁盘没问题。考虑到出现问题的前一段时间，曾使用`sudo apt-get dist-upgrade -y`对Ubuntu进行升级操作，再次判断**因某些程序升级过程中出现问题（如驱动之类的），导致关键目录出现问题**，遂对文件系统进行检查与修复。  

## 解决过程

根据提示，问题出现在`/dev/sda1`，使用`fsck`命令对目标磁盘路径进行检查，过程如下：
```bash
$ fsck -yv /dev/sda1
$ reboot
```
> 提示的是/dev/sdax,则输入fsck -yv /dev/sdax。  

-v 表示显示修复过程和结果，执行结束后重启电脑，成功进入系统。

## 问题总结
fsck（file system check）用来检查和维护不一致的文件系统。若系统掉电或磁盘发生问题，可在安全模式等命令行下利用fsck命令对文件系统进行检查。

fsck常见命令参数如下：
```
-a：自动修复文件系统，不询问任何问题；
-A：依照/etc/fstab配置文件的内容，检查文件内所列的全部文件系统；
-N：不执行指令，仅列出实际执行会进行的动作；
-P：当搭配"-A"参数使用时，则会同时检查所有的文件系统；
-r：采用互动模式，在执行修复时询问问题，让用户得以确认并决定处理方式；
-R：当搭配"-A"参数使用时，则会略过/目录的文件系统不予检查；
-s：依序执行检查作业，而非同时执行；
-t<文件系统类型>：指定要检查的文件系统类型；
-T：执行fsck指令时，不显示标题信息；
-V：显示指令执行过程。
```

全硬盘自检可以直接使用`fsck`即可，若指定修复盘，可用`fsck $path` 即可。