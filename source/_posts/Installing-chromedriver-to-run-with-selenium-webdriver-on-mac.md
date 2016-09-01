---
title: Installing chromedriver to run with selenium webdriver on mac
date: 2016-09-01 17:48:15
tags: selenium chromedriver
categories: 技术
---
最近在研究selenium时，在mac下用selenium打开chrome时要先安装chromedriver，有些费周折，特记录如下，供参考：  
<!--more-->
* 下载[chromedriver](http://chromedriver.storage.googleapis.com/index.html)
* 解压文件
* 把它加到全局路径（一般为/usr/bin）：一般情况下在finder里面是看不见/usr/bin文件夹的，需要先打开finder，在“前往”菜单中选择“前往文件夹”菜单，然后在弹出框里面输入/usr/bin按确认就可以打开/usr/bin文件夹了，然后拖拽刚才的解压文件到/usr/bin文件夹中就可以了。如果你无法将文件加入到/usr/bin文件夹，可能是权限不够，执行sudo或者更改权限把文件加到目录下再改回来即可。
* 运行chromedriver：有的时候mac会觉得文件不可信，不允许运行，这时可以按住ctrl右键点击文件，选择打开即可，会弹出框让你确认运行文件。


后面发现还有更方便的办法：brew安装即可。运行：brew install chromedriver，将自动为你安装到系统目录下，运行也很方便：  
brew services start chromedriver  
或者：  
chromedriver


Enjoying！