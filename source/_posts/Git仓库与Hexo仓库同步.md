---
title: Git仓库与Hexo仓库同步
author: Nothing2say
date: 2023-08-03 16:38:33
tags:
    - 博客
    - Git
categories: 
    - 8月日志
---
# Git分支的操作总结
1. 切换分支，比如`git checkout master`是切换到master分支
``` bash
$ git checkout 任意分支      
```
2. 创建并切换到新分支
``` bash
$ git checkout -b 新建分支      
```
3. 更新分支代码`并`提交
``` bash
$ git add *       
$ git commit -m "新建分支"      
$ git push origin 新建分支       
```
4. 查看所有分支 和 查看当前分支
``` bash
$ git branch -a 
$ git branch     
```
# 使用git分支保存hexo博客源码到github
主要是新建一个新的分支，然后将本地Hexo目录保存在新分支中，然后与远程仓库关联。

后续即便你再发布博客时，deploy生成public文件，在提交博客源码时，也不会将其带上去，因为有.gitignore将其忽略了。

然后默认分支改为保存博客源码的source分支，但是depoly的发布分支设为master。

以后每次修改了主题或者新增了博文，按照以下步骤同步：
``` bash
$ git add .
$ git commit -m "new modifed"
$ git push origin Hexo
$ hexo g -d
```