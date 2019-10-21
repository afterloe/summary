# Linux下安装node、java
> create by afterloe  
> MIT License

介于很多用户都修改了ubuntu的源，导致openjdk安装失败，现写下该教程该教程的目的是从官网下载java然后部署到你
的linux虚拟机中。首先确定环境
```
root@afterloe:/# uname -a
Linux afterloe.jwis.cn 3.16.0-30-generic #40~14.04.1-Ubuntu SMP Thu Jan 15 17:43:14 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```

没有安装java的输出为
```
root@afterloe:/# java
bash: java: command not found
root@afterloe:/# java -version
bash: java: command not found
root@afterloe:/# which java
```

从官网下载java的运行环境
`http://download.oracle.com/otn-pub/java/jdk/8u65-b17/jre-8u65-linux-x64.tar.gz?AuthParam=1449542130_770b56e2b43de30bd7733d2cf510743a`

下载之后内容如下
```
root@afterloe:/home/afterloe/下载# ls -l
总用量 70032
-rw-rw-r-- 1 afterloe afterloe     6263 12月  2 17:40 install.sh
-rw-rw-r-- 1 afterloe afterloe 71703881 12月  8 10:50 jre-8u65-linux-x64.tar.gz

root@afterloe:~# ls
jre-8u65-linux-x64.tar.gz
root@afterloe:~# chmod +x jre-8u65-linux-x64.tar.gz
root@afterloe:~# tar -xzf jre-8u65-linux-x64.tar.gz
root@afterloe:~# ls
jre-8u65-linux-x64.tar.gz  jre1.8.0_65
```
下载下来的java是能够直接使用的
```
root@afterloe:~# cd jre1.8.0_65/
root@afterloe:~/jre1.8.0_65# ls
COPYRIGHT  LICENSE  README  THIRDPARTYLICENSEREADME-JAVAFX.txt  THIRDPARTYLICENSEREADME.txt  Welcome.html  bin  lib  man  plugin  release
root@afterloe:~/jre1.8.0_65# cd bin/
root@afterloe:~/jre1.8.0_65/bin# ls
ControlPanel  java  javaws  jcontrol  jjs  keytool  orbd  pack200  policytool  rmid  rmiregistry  servertool  tnameserv  unpack200
root@afterloe:~/jre1.8.0_65/bin# ./java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```
接下啦就是配置环境变量了
```
root@afterloe:~/jre1.8.0_65# pwd
/root/jre1.8.0_65


root@afterloe:~/jre1.8.0_65# nano ~/.profile
```
在文件末尾添加如下内容，`#JAVA_HOME`为你解压后的文件的位置，我的解压后放在`/root/jre1.8.0_65`下
```
export JAVA_HOME=/root/jre1.8.0_65
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
刷新环境变量
```
root@afterloe:~/jre1.8.0_65# source ~/.profile
```
接着就是测试java了
```
root@afterloe:~/jre1.8.0_65# java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```
到这里java的安装和配置就结束了。其他的版本都是一样的操作，这是在局部用户下有，如果需要全局使用，可以在/etc/profile的最后
添加上面的内容，然后刷新结果是一样的
```
root@afterloe:~/jre1.8.0_65# more ~/.profile
# ~/.profile: executed by Bourne-compatible login shells.

if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

mesg n

export JAVA_HOME=/root/jre1.8.0_65
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
