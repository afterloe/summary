# Git 常用命令
> create by [afterloe](605728727@qq.com)  
> on 2019-10-21  v1.1  
> MIT License  

## 子模块

当关联的子模块项目有新的提交之后，可以cd切换到相应模块目录执行 git pull来拉取当前模块最新的代码一次性拉取所有子模块最新代码的命令是在最外层的项目目录执行:
```
git submodule foreach git pull
```

如果子模块有最新的提交，那么继续执行后续的
```
git add commit push
```
即可拉取所有子模块的最新提交。

给项目添加子模块:
```
git submodule add git@github.com:huiGod/git_child.git mymodule  
```
创建子模块

对于有submodule的git项目，对应的clone需要有一定的改变，因为同时需要clone对应的子模块：
第一种方法(初始化)：
```
git submodule init
git submodule update --recursive
```
第二种方法(已有)：
```
git clone git@github.com:huiGod/git_parent.git  --recursive
```

## 日常使用方面:
```
git reset --soft HEAD^ # 撤回commit操作(代码仍然保留)
git add XXX  #将文件或者文件夹加入到git管理的仓库。前提是该文件已近在目录下
git commit -m "说明"  #将文件提交到仓库，说明可以写任意内容，当然有意义的最好
git status #可以实时查看项目的当前状态，如果有状态变化则显示
git diff fileName #查看文件的修改内容
git show uuid  #查看某次提交的修改内容
git clone ssh://xxxx -b       #克隆远程库指定分支
git tag  #输出当前仓库所有标签
git tag -l '2.*'  #列出符合模式的标签
git tag -a {tagName} -m {message}  #创建标签
git show {tagName}  #显示标签信息
git tag -d {tagName}  #删除标签
git checkout [tagName] #切换到标签
git push {remote} {tagName}  #推送标签
git init  #初始化git项目
git merge xxx --no-ff // merge #自动合并
git remote add origin ssh://git@github.com:afterloe/summary.git #增加远程库
git remote set-url origin http:/git@github.com:afterloe/summary.git #设置origin新
git push origin master #提交代码到远程库
git log --pretty=oneline #查看历史版本信息,后面的参数是一条线查看
git log --graph --pretty=oneline --abbrev-commit #查看版本分支合并线
git reset --hard HEAD^ #切换到上一个版本
git reset --hard 版本id  #切换到指定id的版本
git reflog #查看修改的历史版本的信息
git pull orgin master #从服务器上下载代码
git push orgin master #将本机上的代码传上服务器
git config –global user.email “605728727@qq.com”  #设置用户信息，只需要设置一次
git config –global user.name #设置用户信息，只需要设置一次
git checkout -b dev  # dev 为分支名
git branch  #查看分支
git checkout master  # 切换分支，master代表的是主分支
git merge dev  # 合并dev分支
git branch -d dev  # 删除dev 分支
git log --graph  # 查看分支合并图
git rm -r --cached ignore_file #删除缓存
```

## git ssh 免密码
```
$ ssh-keygen -t rsa -b 4096 -C "afterloe@foxmail.com"
cd ~/.ssh
more ./id_rsa.pub
```
复制内部内容 在gitlab或服务器粘贴，即可免ssh密码登录

## git忽略掉某个文件
修改项目下`vim .gitignore`的文件。  
这个文件每一行保存了一个匹配的规则例如：
```
# 此为注释 – 将被 Git 忽略
            *.a       # 忽略所有 .a 结尾的文件
            !lib.a    # 但 lib.a 除外
            /TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
            build/    # 忽略 build/ 目录下的所有文件
            doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
另外 git 提供了一个全局的 `.gitignore`，你可以在你的用户目录下创建 `~/.gitignoreglobal` 文件，以同样的规则来划定哪些文件是不需要版本控制的。
需要执行
```
git config --global core.excludesfile` ~/.gitignoreglobal
``` 
使其生效。
> 其他的一些过滤条件  
    * ？：代表任意的一个字符  
    * ＊：代表任意数目的字符  
    * {!ab}：必须不是此类型  
    * {ab,bb,cx}：代表ab,bb,cx中任一类型即可  
    * [abc]：代表a,b,c中任一字符即可  
    * [ ^abc]：代表必须不是a,b,c中任一字符  

由于git不会加入空目录，所以下面做法会导致tmp不会存在 `tmp/* #忽略tmp文件夹所有文件`改下方法，在tmp下也加一个.gitignore,内容为
```
    *
    !.gitignore
```
还有一种情况，就是已经commit了，再加入gitignore是无效的，所以需要删除下缓存。
