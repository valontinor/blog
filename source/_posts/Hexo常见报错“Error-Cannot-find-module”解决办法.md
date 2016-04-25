---
title: 'Hexo常见报错“Error: Cannot find module”解决办法'
date: 2016-04-25 23:59:05
tags: hexo
categories: 技术
---
#### 错误描述
***
Hexo是一个非常好用的静态博客生成器，但是由于很多方面的原因，导致在使用过程中经常出现错误。这些错误中，有些是因为自己的设置不当，导致程序报错；有些是因为版本更迭，导致原有的设置失效；而有些，则是Hexo程序本身的BUG。本文解决安装完毕后，启动server时报“Error: Cannot find module”错误的解决办法。
<!--more-->
#### 解决方法
***
安装完hexo，启动server时，有时候hexo会报如下错误：
```sh
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```
虽然server也可以正常启动，但每次都出现这样的错误总归是不舒服。解决方法如下：
1. 删除原有的hexo版本
```sh
which hexo
rm -rf /path/to/hexo
```
2. 更新npm到最新版本（可选）
```sh
npm install npm -g 
```
3. 安装全局hexo
```sh
npm install hexo-cli -g --no-optional
```
4. 初始化目录
```sh
hexo init blog
cd blog
npm install
```
5. 运行hexo server
```sh
hexo s    //或hexo server
```
6. 使用--no-optional选项安装hexo（可选）
如果运行server后，仍然报“Error: Cannot find module”之类的错误，可以再运行如下命令：
```sh
npm install hexo --no-optional
```
一般进行到第5步就可以正常运行了，如果第5步仍然报错再进行第6步，然后启动server，问题解决。

**本文欢迎转载，但是恳请保留贡献者信息，谢谢！**