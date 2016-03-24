---
title: Hexo 更改默认Google 字体库
date: 2016-03-11 11:23:46
categories: hexo
tags: hexo
---

因为一些国内的客观原因，google 的访问速度一直很慢，而hexo是由台湾人开发的系统，里面用到了google>    字体库。所以生成页面后，访问系统总是会耗费一大部分的时间在加载google字体库上，而且经常加载不成功。      
解决的办法是可以用国内的CDN库来替代主题中的google字体库，更改方法如下：      
<!--more-->
shell中运行如下命令：      
```
grep -ir fonts.google themes/
```      
找到对应的google字体库地方，用国内的CDN字体库替换，如360字体库：      
360前端公共库CDN：<http://libs.useso.com/>
