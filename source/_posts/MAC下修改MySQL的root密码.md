---
title: MAC下修改MySQL的root密码
date: 2016-04-19 10:39:20
tags: MySQL mac
categories: 技术
---
mac下面安装MySQL可以通过二进制包（DMG）安装文件或手动编译安装（TAR）的方式进行，推荐使用二进制包的安装方式，因为其比较简便，提供了图形安装界面，只要一直点同意就可以了。安装时MySQL会默认创建root账号，并提供一个随机的密码，记住随机的密码可以在安装完成后root账号登陆系统，通过常规方式修改。如果忘记或忽略了随机生成的密码，如何进系统修改root密码呢？可以通过如下方式进行： 
<!--more--> 
1. 关闭mysql服务：mac下安装完成MySQL后会在“系统偏好设置”里面生成一个MySQL应用的图标，点开后会看见mysql服务的状态： 
	![MySQL](http://i4.piimg.com/ebf13577edcd596b.jpg) 
如果像图中一样是“running”状态，点击面板里面的“Stop MySQL Server”来关闭mysql服务。

2. 禁止mysql验证功能：打开shell终端（或iterm等），进入mysql服务所在文件夹（一般为/usr/local/mysql/bin）执行如下命令：
```sh
cd /usr/local/mysql/bin
sudo su    #切换到超级管理员账号，回车
#此处会要求输入密码
./mysqld_safe --skip-grant-tables & #禁止mysql验证，注意后面的&字符串不要漏了，回车
```
	进行上述操作后，mysql会自动重启，此时可以查看偏好设置中的mysql的状态，会发现已经变成running了。 
3. 登陆mysql，更改密码：新开shell终端，进入到mysql文件夹下，登陆mysql，更改密码。
```sh
cd /usr/local/mysql/bin
./mysql #回车后会直接登陆进mysql
mysql> FLUSH PRIVILEGES; #mysql界面的操作，刷新权限
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass'; #更改密码，适用于MySQL 5.7.6或以上版本
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('MyNewPass'); #更改密码，适用于MySQL 5.7.5或更早版本
```
至此，密码修改完成，退出后可使用修改后的密码登陆系统。
