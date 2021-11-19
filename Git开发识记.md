项目开始阶段，初始化项目（init），提交本地的代码到仓库，将本地仓库的代码推送到远端库（push）；

项目开发人员从远端库克隆代码到本机（clone），此时本地仅有一个master分支；新建dev分支并切换、在Dev分支中进行开发工作，其实就是修改并提交代码（add+commit）；当开发的dev分支的代码没问题时，将dev分支合并（merge）到master；将master推送到远端分支，至此，其他的项目开发人员就可以查看到你提交的代码了！

dev分支也可以是修复某个bug或者为了开发某个issue建立的，当bug已经修复或issue开发完成时，把dev合并到master之后，就可以把它删除了



每次提交commit，提交的之前add暂存到缓存区的文件，若add之后修改了数据，commit则不会提交最新修改的

可以用命令	git diff HEAD -- filename	查看工作区和提交后的代码的区别

#### 基础命令

| 命令                   | 描述                                                  |
| ---------------------- | ----------------------------------------------------- |
| git init               | 创建一个git仓库，生成.git文件                         |
| git add filename       | 添加文件到缓冲区                                      |
| git add .              | 添加所有文件                                          |
| get add --all          |                                                       |
| git fm filename        | 删除文件                                              |
| git rm -r --cached .   | 从缓冲区删除文件                                      |
| git commit -m "说明"   | 提交                                                  |
| git status             | 查看 文件状态                                         |
| git log                | 查看日志                                              |
| git reset              | 版本回退                                              |
| git reset --hard HEAD^ | 回退到上一个版本，HEAD代表当前版本，一个^代表一个版本 |
| git reset --hard xxxx  | 回退到指定版本，xxxx表示版本号的前几位                |
| git reflog             | 查看历史命令                                          |



#### 分支管理

| 命令                   | 描述                                   |
| ---------------------- | -------------------------------------- |
| git branch             | 查看分支情况                           |
| git branch 分支名      | 创建分支                               |
| git checkout 分支名    | 切换分支                               |
| git checkout -b 分支名 | 创建分支，并切换                       |
| git branch -d 分支名   | 删除分支                               |
| git log --graph        | 查看分支合并图                         |
| git tag 标签名 版本号  | 新增标签，默认最新，后面版本号指定版本 |
| git tag                | 查看所有标签                           |
| git push origin --tags | 提交tag到远端仓库                      |
| git show 标签名        | 查看标签详细信息                       |
| git push origin 标签名 | 推送 指定标签                          |



#### 远端库相关

| 命令                       | 描述                 |
| -------------------------- | -------------------- |
| git remote add origin 地址 | 增加远端仓库         |
| git remote remove origin   | 移除远端仓库         |
| git push -u origin master  | 第一次提交到远端仓库 |
| git pull                   | 从远端仓库更新到本地 |
|                            |                      |
|                            |                      |
|                            |                      |
|                            |                      |
|                            |                      |

#### 小备注

记住用户名和密码，避免频繁push时输入信息

```
[credential]
     helper = store
```

忽略 部分IDEA产生的文件

在根目录下创建.gitignore文件

```tex
/**/target
/**/.project
/**/.classpath
/**/.settings
```

git rm -r --cached .
git add .
git commit -m 'update .gitignore'

查看某段时间的代码更新量

```bash
git log --since=2018-09.01 --until=2018-09.27 --author="$(git config --get user.name)"
```

