---
title: mac下访问国内网络不走vpn路由设置
date: 2016-04-07 17:58:38
tags: mac
categories: mac
---
vpn是程序员的一个标配，vpn能提高程序员50%的效率不过分吧。使用vpn时，默认流量都走vpn，这样访问国内网络就会变得很慢。这样自然会让人产生国内网站不适用vpn，访问不了的国外网站使用vpn的需求。对于普通人可能不会路由表配置，而且选项也很多。  
**chnroute**这个项目，能够满足这种需求，使用简单，同时支持多平台mac、win、linux都可以: 
<!--more--> 
* 项目地址：<https://github.com/jimmyxu/chnroutes>
* 下载地址：<https://raw.githubusercontent.com/jimmyxu/chnroutes/master/chnroutes.py>

#### mac使用办法
***
1. 下载chnroute
2. 终端执行如下命令
```  
python chnroutes.py -p mac  
```  
这会在当前文件夹下生成两个文件：ip-up和ip-down
3. 将这两个文件移入/etc/ppp 文件夹下：
```  
cp ip-* /etc/ppp/
```  
4. 为ip-up和ip-down文件增加可执行权限：
```  
chmod +x /etc/ppp/*
```  
5. 至此完成设置，可重新链接VPN进行观察。如果以后不想使用了，删掉ip-up和ip-down这两个文件就可以了。

转载至：<http://www.jianshu.com/p/38b1ff09d226>
