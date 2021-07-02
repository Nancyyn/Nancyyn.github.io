---
title: Git命令
date: 2019-12-21 15:55:22
categories:
  - Git命令
tags:
  - Git命令
  - 技术总结
---

### 1.初次运行 Git 前的配置

- git config --global user.name "Nancy" #设置用户名
- git config --global user.email "Nancy@163.com" #设置邮件地址
- git config --list #检查配置信息

```javascript
user.name=Nancy
user.email=Nancy@163.com
color.status=auto
...
```

<!--more-->

### 2.获取 Git 仓库

通常有两种获取 Git 项目仓库的方式：

- 将尚未进行版本控制的本地目录转换为 Git 仓库；

```javascript
如果你有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，那么首先需要进入该项目目录中：$ cd /c/user/my_project

之后执行：$ git init

通过 git add 命令来指定所需的文件来进行追踪，然后执行 git commit ：
$ git add *.c
$ git add LICENSE
$ git commit -m 'initial project version'
```

- 从其它服务器 克隆 一个已存在的 Git 仓库。

```javascript
克隆仓库的命令是 git clone <url> 。 比如，要克隆 Git 的链接库p8v3.0文件夹下的p8-vue-platform，可以用下面的命令：
$ git clone http://github.com/p8v3.0/p8-vue-platform.git

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：
>$ git clone http://github.com/p8v3.0/p8-vue-platform.git mylibgit
```

### 3.记录每次更新到仓库

- git status #检查当前文件状态
- git status -s 命令或 git status --short 命令 #状态简览
- 忽略文件，创建一个名为.gitignore 的文件

```javascript
$ cat .gitignore
# 忽略所有以 .o 或 .a 结尾的文件
*.[oa]

# 忽略所有名字以波浪符（~）结尾的文件
*~

# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

- git diff #查看已暂存和未暂存的修改
- git commit #提交更新

```javascript
git commit 加上 -a 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤
```

- git rm #移除文件
- git mv #移动文件

```javascript
要在 Git 中对文件改名，可以这么做：

$ git mv file_from file_to

关于重命名操作：$ git mv README.md README

其实，运行 git mv 就相当于运行了下面三条命令：

$ mv README.md README
$ git rm README.md
$ git add README
```

### 4.查看提交历史 git log

常用选项：

- -p 或 -patch，它会显示每次提交所引入的差异（按 补丁 的格式输出）。 你也可以限制显示的日志条目数量，例如使用 -2 选项来只显示最近的两次提交：$ git log -p -2

### 5.撤销操作

- git commit --amend #撤销操作

```javascript
有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 --amend 选项的提交命令来重新提交
```

- git reset HEAD <file>... #取消暂存的文件

### 6.远程仓库的使用

- git remote #查看远程仓库
- git remote -v #会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL
- git remote add <shortname> <url> #添加远程仓库

```javascript
$ git remote
origin
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	https://github.com/paulboone/ticgit (fetch)
pb	https://github.com/paulboone/ticgit (push)
```

- git fetch <remote> #从远程仓库中抓取与拉取
- git push <remote> <branch> #推送到远程仓库，当你想要将 master 分支推送到 origin 服务器时，执行命令：git push origin master
- git remote show <remote> #查看某个远程仓库
- git remote rename #修改一个远程仓库的简写名

```javascript
想要将 pb 重命名为 paul，可以用 git remote rename 这样做：

$ git remote rename pb paul
$ git remote
origin
paul
```

- git remote remove 或 git remote rm #移除一个远程仓库

### 7.打标签

- git tag #列出标签

```javascript
$ git tag
v1.0
v2.0
```

- git tag -l 或 -list #按照通配符列出标签需要 -l 或 --list 选项

```javascript
Git 自身的源代码仓库包含标签的数量超过 500 个。 如果只对 1.8.5 系列感兴趣，可以运行：

$ git tag -l "v1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
```

- 创建标签 Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）

```javascript
1.附注标签，是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，最简单的方式是当你在运行 tag 命令时指定 -a 选项
$ git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4

2.轻量标签，本质上是将提交校验和存储到一个文件中——没有保存任何其他信息
$ git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
```

- git tag -a v1.2 9fceb02 #后期打标签
- git push origin <tagname> #在创建完标签后你必须显式地推送标签到共享服务器上
- git tag -d <tagname> #删除掉你本地仓库上的标签
- git push origin --delete <tagname> #删除远程标签
- git checkout <tagname> #检出标签

### 8.分支

- git branch #获取所有分支的一个列表
- git branch xxx #创建分支
- git checkout xxx #切换分支
- git merge xxx(eg:dev) #合并分支（将代码从一个分支转移到另一个分支，另一个分支的所有代码变动时采用合并）

```javascript
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

- git cherry-pick <commitHash> #合并指定的提交到某个分支
- git push <remote> <branch> #本地推送到远程仓库中
- git pull #从服务器上抓取数据 === 一个 git fetch 紧接着一个 git merge 命令
- git push <remote> --delete <branch> #删除远程分支
