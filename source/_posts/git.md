---
title: git随手
date: 2017-11-22 09:21:37
categories:
 - essay
 - tec
tags:
- tools
- git
---
----------
最近在家里和公司切换需要开始维护自己的作品相关，不管是博客还是代码，之前有用过github作为中转站进行操作，后面还是决定简单的过一遍git。系统的记录一下。
ps.本文是随手篇，随手篇同笔记类似，不追求大家能看懂，只是一个自己的学习记录以及之后的复习材料而已。
下面是学习中的一些记录，本文跟随[廖雪峰老师的git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)学习。

<!-- more -->
## 创建版本库 ##
    git init
创建一个empty Git repository.
  
    git init <repository name>
创建一个 名叫（文件名为 repository name）的Git repository

    git add xxx.xxx
将xxx.xxx文件添加到缓存区

    git commit -m "wrote a readme file"
提交这个文件，并添加描述信息 "wrote a readme file"

	初始化一个Git仓库，使用git init命令。
	添加文件到Git仓库，分两步：
    第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；
    第二步，使用命令git commit，完成。

----------

## 预提交 ##

    git status
查看git工作区的状态，那些文件没有被add,哪些文件有修改

    git diff 
查看git缓存区中修改的具体内容

----------

## 回退以及恢复 ##

    git log 
查看版本log（只显示当前版本之前的log）
> 
> 注意：假如现有ABCD四个版本，你从D版本回退git reset到C 版本，现在只显示ABC，而D版本会不显示


    git reset --hard commit_id
    git reset --hard HEAD~
恢复到 commitId 对应的版本 或上一个版本 HEAD只当前版本，HEAD~上版本 HEAD~200 上200版本

    git reflog
查看操作log（可以查询所有版本ID，可以根据ID恢复到上述的D版本)

----------

## 理解工作区 暂存区 版本库 ##
工作区修改 -> add -> 暂存区 -> commit -> 版本库

----------
## 删除文件 ##

误删

    git checkout -- filename
可以回复最新版本库中代码

真实删除

    git rm filename
	git commit -m

版本库中删除文件

----------


## 关联远程库 ##

    git remote add origin git@server-name:path/repo-name.git
关联远程库,比如github,或自己搭建的服务器

    git push -u origin master
第一次推送，将master和远程库的master关联

    git push origin master
之后的每次推送

----------

## 分支 ##

git 分支原理：指针变化

O - O - O （<-master<-HEAD）

添加分支，添加dev指针，HEAD指针指向dev

O - O - O（<-master） - O (dev<-HEAD)

合并后,HEAD指向->dev和master

O - O - O - O (dev/master<-HEAD)

    git branch 
查看分支

    git branch xxx
添加xxx分支

    git checkout xxx
切换到xxx分支

    git checkout -b xxx
创建并切换到xxx分支

    git merge xxx
将目标分支合并到当前分支

    git barnch -d xxx
删除某分支


----------

## git 解决冲突 ##
当两条分支都有各自的提交后，使用快速merge，很可能造成conflict。

O - O （master commit something） - O(dev1 commit something)

merge cause conflict, fix the conflict in the file.
add and commit.

    git log --graph --pretty=oneline --abbrev-commit
可以查看分支合并图

**可以删除dev1分支*

----------

## git 分支管理策略 ##
git 使用Fast forward 模式时，删除分支后，会丢失分支信息
Fast forward 模式简写ff,git 支持 --no--ff 方式做git merge操作

**简单说明**

图中是为了尝试 noff 方式以及 ff 方式的不同。
首先 新建dev分支，merge到master 可以看到`4d42b40`提交后，其实是有一个merge动作的，但那是并没有显示。
删除dev并重新新建dev后，使用--no-ff 方式 merge 后则出现了分支信息。
也就是说 使用noff方式merge后，分支信息并没有丢失

![merge](https://raw.githubusercontent.com/CapDuan/MarkDownImg/master/test.png)

----------

## bug分支 ##

bug分支场景如下：

1. dev分之上正在工作且无法提交更改
2. 突发bug issue-101,需要拉分支修改bug
3. 对dev分之上可以使用 `git stash` 保存现场
4. 再去master或其他分支上拉一条bug分支，然后进行修复
5. 修复完成后，回到dev分支使用`git stash list`查看现场
6. `git stash pop` 恢复现场并删除stash list
7. `git stash apply` 仅恢复现场并不会删除stash list


    git stash

保存现场

    git stash list
查看所有现场

    git stash pop
恢复现场并删除list

    git stash apply
恢复现场并不会删除list

    git stash drop
删除现场记录

    git stash apply stash@{0}
恢复到指定现场

----------

## Feature 分支 ##

建立分支来开发新功能
但是当没有merge却要抛弃新功能分支时
使用-D取代-d

    git branch -D <name>
强行删除一个未merge的分支

----------

## 多人协作 ##
多人协作需要将本地分支推送到远程版本库 remote repository，
在推送分支时出现推送异常需要 pull 最新代码到本地编辑冲突，在pull时，如果遇到pull失败则需要绑定本地分支和远程分支的关联



    git remote
查看远程库的信息

    git remote -v 
查看远程库的详细信息

    git push origin master
推送master分支（默认 git push 可以省略后面master）

    git push origin xxx
推送xxx分支到远程库

    git checkout -b xxx origin/xxx
在本地远程xxx分支到本地

    git pull
拉取远程库到本地

    git branch --set-upstream xxx origin/<branch>
关联本地xxx分支和<branch>



多人协作流程：
> 
1. 首先，可以试图用git push origin branch-name推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！
5. 
如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。


----------



## 标签管理 ##

标签就是一个易读的cimmit号，每一个标签都可以对应一个commit


    git tag xxx yyyyyyyy
用于新建一个标签，这个标签默认为`HEAD`，也可以制定一个commitId `yyyyyyyy`

    git tag -a <tagname> -m "taginfo message"
可以制定标签的描述信息

    git tag 
查看所有标签

    git push origin <tagname>
推送一个标签到远程库

    git push origin --tags
可以将本地标签tags都推送到远程库

    git tag -d <tagname>
删除一个本地标签

    git push origin :refs/tags/<tagname>
删除一个远程标签

## git 忽略文件 ##
有些文件自动生成的非代码类的文件，当 `git status` 时各种提示，有点烦，新建一个 `.gitignore` 文件放到版本库中，进行忽略管理

## 为命令配置别名 ##
可以使用 `git config --global alias. xx XXXXX` 
为XXXXX配置xx别名，今后的XXXXX命令都可以使用xx代替


## 后记 ##

因为目前使用http所以对ssh那里的公钥私钥没有进行记录，以后看需求学习，还有配置别名以及搭建Git服务器目前暂无需求，今后看需求学习。