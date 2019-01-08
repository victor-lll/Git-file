# Git

## Git简介

​    Git是目前世界上最先进的分布式版本控制系统，CVS和SVN，这些集中式的版本控制系统不但速度慢，而且必须联网才能使用，如果在局域网内还好，带宽够大，速度够快，可如果在互联网上，遇到网速慢的话，可能提交一个10M的文件就需要5分钟，分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

## Git安装

### 1.安装需要环境

```bash
$ yum -y install lrzsz
$ yum -y install gcc
$ yum -y install zlib-devel
$ yum -y install wget
$ systemctl stop firewalld
$ stenforce 0
```

### 2.下载Git安装包

```bash
$ wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.20.1.tar.gz
```

### 3.解压Git源码包

```bash
$ tar -zxf git-2.20.1.tar.gz -C /opt/
```

### 4.配置

```bash
$ cd git-2.20.1
$ ./configure
```

### 5.编译

```bash
$ make
```

### 6.安装

```bash
$ make install
```

## 创建版本库

### 1.创建新的目录

```bash
$ mkdir Gittest
$ cd Gittest
$ pwd
$ git init
```

### 2.添加文件到版本仓库

```bash
$ git add readme.txt
$ git config --global user.name "victor"
$ git config --global user.email "13663690680@163.com"
$ git commit -m "文件"
```

## 时光穿梭机

### 1.查看工作状态

```bash
# 要随时掌握工作区的状态，使用git status命令。
# 如果git status告诉你有文件被修改过，用git diff可以查看修改内容。
$ git status
$ git diff readme.txt
```

### 2.版本回退

```bash
# HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
# 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
# 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

$ git log
$ git log --pretty=oneline
$ git reset --hard HEAD^
$ git reset --hard HEAD~1
$ git reset --hard commit ID
$ git reflog
```

### 3.工作区和缓存区

```bash
# 工作区：就是你在电脑里能看到的目录，比如我的Gittest文件夹就是一个工作区
# 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD

$ git status
$ git add .
$ git status
$ git commit -m "understand how stage works"
$ git status
```

### 4.管理修改

```bash
# 每次修改，如果不用 git add 到暂存区，那就不会加入到 commit 中。

$ cat readme.txt
$ git add readme.txt
$ git status
$ cat readme.txt
$ git commit -m "git change"
$ git diff HEAD -- readme.txt
$ git add readme.txt
$ git commit -m "change"
$ cat readme.txt
```

### 5.撤销修改

```bash
# 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
# 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
# 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

$ vi readme.txt
  My stupid boss still prefers SVN
$ git status
$ git checkout -- readme.txt
$ cat readme.txt
$ vi readme.txt
  My stupid boss still prefers SVN
$ git add readme.txt
$ git status
$ git rest HEAD readme.txt
$ git status
$ git checkout -- readme.txt
$ git status
```

### 6.删除文件

```bash
# 命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

$ git add test.txt
$ git commit -m "new file"
$ rm test.txt
$ git status
$ git rm test.txt
$ git commit -m "remove test.txt"
$ git checkout -- test.txt
```

## 远程仓库

### 1.创建远程仓库

```bash
# “有了远程仓库，妈妈再也不用担心我的硬盘了。”——Git点读机

$ ssh-keygen -t rsa -C "962045837@qq.com"
$ cd .ssh/
$ cat id_rsa.pub
```

### 2.添加远程仓库

```bash
# 要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
# 关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
# 此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
# 分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步。

$ git remote add origin git@github.com:victor-lll/Gittest.git
$ git push -u origin master
$ git push origin master
```

### 3.从远程库克隆

```bash
# 要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆;
# Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

$ git clone git@github.com:victor-lll/Git.git
$ cd Git/
$ ls
  README.md
```

## 分支管理

  有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作，Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成。

### 1.创建与合并分支

```bash
# 查看分支
$ git branch
# 创建分支
$ git branch <name>
# 切换分支
$ git checkout <name>
# 创建+切换分支
$ git checkout -b <name>
# 合并某分支到当前分支
$ git merge <name>
# 删除分支
$ git branch -d <name>
```









