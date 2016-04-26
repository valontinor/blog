---
title: Git常用操作总结
date: 2016-04-19 17:08:22
tags: git
categories: 技术
---
文自  [凹凸实验室](http://aotu.io/notes/2015/11/17/Git-Commands/)。  
Git是目前世界上最先进的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。下面是一些Git常用方法的总结，以便回顾和查询。
![Git](http://7xte88.com2.z0.glb.clouddn.com/git-flow.jpg)
<!--more-->
#### 配置
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
#### 提交
Git 追踪的（tracked）的是修改，而不是文件。
![Commit](http://7xte88.com2.z0.glb.clouddn.com/git-trees.jpg)
```
#将“当前修改”移动到暂存区(stage)
$ git add somfile.txt
#将暂存区修改提交
$ git commit -m "Add somfile.txt."
```
#### 状态
```
$ git status
$ git diff
```
#### 回退

```
# 放弃工作区修改
$ git checkout -- file.name
$ git checkout -- .


# 取消commit(比如需要重写commit信息)
$ git reset --soft HEAD


# 取消commit、add(重新提交代码和commit)
$ git reset HEAD
$ git reset --mixed HEAD


# 取消commit、add、工作区修改(需要完全重置)
$ git reset --hard HEAD
```

#### 记录
```
$ git reflog
$ git log
```
#### 删除
```
$ rm file.name
$ git rm file.name
$ git commit -m "Del"
```
#### 远程操作
```
$ git remote add origin git@github.com:michaelliao/learngit.git
# 第一次推送，-u(--set-upstream)指定默认上游
$ git push -u origin master
$ git push origin master
```
#### 克隆
```
$ git clone https://github.com/Yikun/yikun.github.com.git path
$ git clone git@github.com:Yikun/yikun.github.com.git path
```
#### 分支操作
![Branch](http://7xte88.com2.z0.glb.clouddn.com/git-merge.png)
```
# 查看当前分支
$ git branch

# 创建分支
$ git branch dev
# 切换分支
$ git checkout dev

# 创建并checkout分支
$ git checkout -b dev

# 合并分支
$ git merge dev

# 删除分支
$ git branch -d dev
```

#### 标签
```
$ git tag 0.1.1
$ git push origin --tags
```
