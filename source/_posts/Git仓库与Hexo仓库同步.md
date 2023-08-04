---
title: Git仓库与Hexo仓库同步
author: Nothing2say
date: 2023-08-03 16:38:33
tags:
    - 博客
    - Git
categories: 
    - 2023年8月日志
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
# Github文件夹出现箭头，且打不开文件
原因：GitHub将他视为一个子系统模块，因此在上传代码时隔绝了这个文件夹，上传失败。

{% asset_img image_right_arrow.png 显示页面 %}

解决：
``` bash
1. 删除子文件夹里面的.git文件
2. 执行git rm --cached [文件夹名]
3. 执行git add .  (或是把 . 换成自己要传的文件夹）
4. 执行git commit -m "commit messge"
5. 执行git push origin [branch_name]
```
# 利用.gitignore来忽略关键文件和文件夹：
## Git 忽略规则优先级
在 .gitingore 文件中，每一行指定一个忽略规则，Git 检查忽略规则的时候有多个来源，它的优先级如下（由高到低）：
``` bash
从命令行中读取可用的忽略规则
当前目录定义的规则
父级目录定义的规则，依次递推
$GIT_DIR/info/exclude 文件中定义的规则
core.excludesfile中定义的全局规则
```
## Git 忽略规则匹配语法
在 .gitignore 文件中，每一行的忽略规则的语法如下：
``` bash
空格不匹配任意文件，可作为分隔符，可用反斜杠转义
开头的文件标识注释，可以使用反斜杠进行转义
! 开头的模式标识否定，该文件将会再次被包含，如果排除了该文件的父级目录，则使用 ! 也不会再次被包含。可以使用反斜杠进行转义
/ 结束的模式只匹配文件夹以及在该文件夹路径下的内容，但是不匹配该文件
/ 开始的模式匹配项目跟目录
如果一个模式不包含斜杠，则它匹配相对于当前 .gitignore 文件路径的内容，如果该模式不在 .gitignore 文件中，则相对于项目根目录
** 匹配多级目录，可在开始，中间，结束
? 通用匹配单个字符
* 通用匹配零个或多个字符
[] 通用匹配单个字符列表
```
## 常用匹配示例
``` bash
bin/: 忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin: 忽略根目录下的bin文件
/*.c: 忽略 cat.c，不忽略 build/cat.c
debug/*.obj: 忽略 debug/io.obj，不忽略 debug/common/io.obj 和 tools/debug/io.obj
**/foo: 忽略/foo, a/foo, a/b/foo等
a/**/b: 忽略a/b, a/x/b, a/x/y/b等
!/bin/run.sh: 不忽略 bin 目录下的 run.sh 文件
*.log: 忽略所有 .log 文件
config.php: 忽略当前路径的 config.php 文件
```
## .gitignore规则不生效
gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

解决方法就是先把本地缓存删除（改变成未track状态），然后再提交:
``` bash
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```
而正常需要忽略一个文件，则是在.gitignore中添加文件后，再提交。然后继续在本地添加此文件，在提交到git时发现提交不了，证明已经成功被忽略了。
``` bash
$ git add key.txt
The following paths are ignored by one of your .gitignore files:
key.txt
hint: Use -f if you really want to add them.
hint: Turn this message off by running
hint: "git config advice.addIgnoredFile false"
```
可以用git check-ignore命令检查.gitignore文件中是否有规则写错了:
``` bash
$ git check-ignore -v key.txt
.gitignore:9:key.txt    key.txt
```