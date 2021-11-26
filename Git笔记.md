### 常用git命令

| 命令                        | 描述                                 |
| --------------------------- | ------------------------------------ |
| git remote                  | 查看远程仓库名，加-v可以显示详细信息 |
| git branch                  | 查看当前分支名                       |
| git checkout branch1        | 切换到branch1分支                    |
| git branch branch1          | 创建分支branch1                      |
| git checkout -b dev         | 创建dev分支并切换                    |
| branch                      | 分支                                 |
| master                      | 主分支                               |
| origin                      | 仓库名，repository                   |
| git push <仓库名> <分支>    | 格式                                 |
| git init --bare             | 创建一个git裸服务器                  |
| git pull                    | 拉取版本                             |
| git clone <地址>            | 克隆                                 |
| git log                     | 查看提交日志                         |
| git log --pretty==oneline   | 查看日志，以单行显示                 |
| git reflog                  | 回退到未来，撤销回退。查看操作记录   |
| git status                  | git状态                              |
| git diff <文件名.后缀>      | 查看修改内容                         |
| git mv 原文件名  新文件名   | 修改文件名                           |
| gitk                        | 可视化工具                           |
| git reset --hard head       | 回退到上一个版本                     |
| git reset --hard 版本号     | 回退到之前版本                       |
| git restore 文件名          | 撤销文件操作                         |
| git restore --staged 文件名 | 如果已经add到暂存区，撤销            |
| git rm -f 文件名            | 删除文件                             |
|                             |                                      |



### 多人协作

git checkout -b 分支名 origin/分支名，在本地创建和远程分支对应的分支，名称最好一致

git branch --set-upstream-to=origin/dev dev，建立本地分支和远程分支的关联

git pull，先抓取远程的更新，如果有冲突，手动解决冲突

git push origin 分支名，解决冲突后推送



分支基本操作有如下几个：

1. 查看当前分支 （git branch）

2. 创建分支 （git branch 分支名）创建后并不是立即切换到新分支，需要使用命令切换
3. 切换分支（git checkout 分支名）
4. 分支上的常规操作
5. 分支的合并 （git checkout master + git merge 分支名）
6. 分支的删除（git branch -d 分支名）



分支的合并，一定是在 主分支上进行的。

只能在主分支合并其它分支。

需要两步：

1） 切换到主分支

2） 使用git merge 分支名 进行合并	只能用master主分支下才能合并其他分支



查看分支合并情况

git log --graph --pretty=oneline --abbrev-commit



开发环境在dev分支下，bug修复是提交在master中，如何快速合并至dev下：转移至dev分支下，执行下面命令

```bash
git cherry-pick bug分支的提交版本号
```



```bash
git push A B:C
A是仓库名，比如origin
B是本地端的branch
C是仓库里的一个分支branch
意思就是把本地的分支B,推送到仓库A下的C分支中
```

若有人想加入你的远程仓库

```
git remote add upstream https://xxxxxxxxxxxxxxxxxx.git
```



### 本地建仓库方法

1、git工具，在文件夹下，打开命令窗口

```shell
git init	创建本地代码仓库
```

2、同步本地和远程仓库，进行托管

```shell
git remote add origin https://gitee.com/pex123/bei-wang.git
```

origin可以改为项目名

3、拉去远程代码库，会让你输入账户和密码

```
git push origin master
```

由于在创建远程仓库时会初始化一个README.md文件，而本地仓库里没有，所以需要先执行pull操作将远程仓库拉取合并到本地仓库，否则会出错

格式```git pull <remote> <branch>```

```
git branch --set-upstream-to origin master	
```

当你从远程分支上checkout一个本地分支，这个时候，你去pull代码会出现报错

上面的指令可以关联分支，关联后，通过指令可以pull代码，不需要指定从哪个分支pull

4、在写完本地代码后，向远程仓库推送文件

```
git add .
git commit -m "对该操作进行相关描述"
```

```
git push	会提示出现同样的问题，需要使用下面的命令
git push --set-upstream origin master
```



```bash
创建实例
   99  git init
  100  git remote add origin https://gitee.com/pex123/bei-wang.git
  101  git pull origin master
  102  git branch --set-upstream origin master			//这里错误了一次
  103  git branch --set-upstream-to origin master	
  104  git add .
  105  git commit -m "提交测试"
  106  git push											//这里错误了一次			
  107  git push --set-upstream origin master
  108  git add .
  109  git commit -m "java基础"
  110  git push
```







### 远程仓库克隆方法（简单）

1、先建立一个远程仓库

复制地址

在本地文件夹下，打开git命令窗口

```
git clone https://gitee.com/pex123/bei-wang.git
```

可能提示输入账户密码

2、在新建文件后，同步到远程仓库

```
git add .
git commit -m "对该操作进行描述"
```

```
git push
```



### 配置

##### 配置用户名和邮箱

git config --global [user.name](http://user.name) ‘自己的名字’
git config --global [user.name](http://user.name) ‘自己的邮箱’



local 只对当前仓库有效
global 所有仓库有效
system 对系统所有用户有效



##### 查看配置

git config --local -l
git config --list --global
git config --list --system

##### 清除配置

git config --unset --local [user.name](http://user.name)
git config --unset --global [user.name](http://user.name)
git config --unset --system [user.name](http://user.name)





### 管理命令



```
git add "文件名"				提交单独文件，添加至暂存器
git add .	git add -A		  提交所有改动
```

#####  修改仓库名

一般来讲，默认情况下，在执行clone或者其他操作时，仓库名都是 origin 如果说我们想给他改改名字，比如我不喜欢origin这个名字，想改为 oschina 那么就要在仓库目录下执行命令:

```
git remote rename origin oschina
```

这样 你的远程仓库名字就改成了oschina，同样，以后推送时执行的命令就不再是 git push origin master 而是 git push oschina master 拉取也是一样的

#####  添加一个仓库

在不执行克隆操作时，如果想将一个远程仓库添加到本地的仓库中，可以执行

```
git remote add origin  仓库地址
```

注意: 1.origin是你的仓库的别名 可以随便改，但请务必不要与已有的仓库别名冲突 2. 仓库地址一般来讲支持 http/https/ssh/git协议，其他协议地址请勿添加

##### 查看当前仓库对应的远程仓库地址

```
git remote -v
```

这条命令能显示你当前仓库中已经添加了的仓库名和对应的仓库地址，通常来讲，会有两条一模一样的记录，分别是fetch和push，其中fetch是用来从远程同步 push是用来推送到远程

##### 修改仓库对应的远程仓库地址

```
git remote set-url origin 仓库地址
```



### 标签

##### 基本操作

标签的作用可以简单理解为给版本起名字

查看所有标签
git tag

把当前分支的最新提交打上标签，标签名字自己起
git tag 标签名

把某个版本号的提交打上标签
git tag 标签名 对应commit版本号

可以用这种方式给标签增加说明，-a对应标签名，-m对应描述信息
git tag -a v0.1 -m “描述信息” 版本号

查看标签具体信息
git show 标签名

删除标签
git tag -d 标签名


##### 推送标签

推送某个标签到远程
git push origin 标签名

推送所有标签到远程
git push origin --tags

删除远程标签：
先删除本地标签
git tag -d 标签名
	
然后从远程删除
git push origin: refs/tags/标签名



原文链接：https://blog.csdn.net/l1028386804/article/details/121280437

###  暂存

这个既是一个概念也是一个命令，其含义就是字面上的，作用就是可以将你当前正在进行的工作暂时存起来，然后在此基础上干别的事情，等你别的事情干完后，再转回来继续，注意，暂存只是针对你最后一次改动而言，即针对当前所在的版本的所有改动都算具体执行命令为:

##### 将当前改动暂存起来:

```
git stash
```

##### 恢复最后一次暂存的改动

```
git stash pop
```

##### 查看有多少暂存

```
git stash list
```

###  撤销

撤销命令使用是非常频繁的，因为某些原因，我们不再需要我们的改动或者新的改动有点问题，我们需要回退到某个版本，这时就需要用到撤销命令，或者说这个应该翻译成重置更加恰当。具体命令如下:

##### 撤销当前的修改:

```
git reset --hard
```

请注意:以上命令会完全重置你的修改，如果你想保留某些文件，请使用checkout +文件路径 命令来逐一撤销修改

**如果你想重置到某一版本，可以将 `--hard` 改为具体的Commit的id如:**

```
git reset 1d7f5d89346
```

请注意，这时你的修改仍然存在，只是你的最近一次提交的版本号变成了你要重置的版本，如果说你想完全丢弃修改，只需要加上 --hard参数就可以



### Gitee帮助地址

https://gitee.com/help/categories/43





### SSH

为什么要创建SSH公钥呢，使用SSH公钥可以让你在你的电脑和码云通讯的时候使用安全连接（git的remote要使用SSH地址），注意clone时只能对于自己的仓库使用自己的ssh哦，clone别人的仓库还是要使用http。

**一、clone工程有两种：**

1）HTTPS (pull和push的时候需要密码)

2）SSH （不需要密码，但是需要创建公钥）



##### GUI乱码

在乱码的区域点击鼠标右键，选择Encoding，然后选择Unicode（UTF-8）

### 报错

```bash
warning: LF will be replaced by CRLF in 
The file will have its original line endings in your working directory
```



原因是存在符号转义问题

windows中的换行符为 CRLF， 而在linux下的换行符为LF，所以在执行add . 时出现提示，解决办法

git config --global core.autocrlf false



