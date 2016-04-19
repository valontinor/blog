---
title: Python包管理器pip使用
date: 2016-04-19 15:09:26
tags: python
categories: 技术
---

pip是Python语言的一个包管理器，类似于CentOS里面的yum，Python里面类似的包管理器还有easy_install等，但没有pip使用方便和广泛。
<!--more-->
#### 安装pip
***
pip可以在Unix/Linux，OS X和Windows系统中运行。  
pip可以在以下版本的CPython下运行：2.6，2.7，3.1，3.2，3.3，3.4和pypy。如果你使用的是从 <python.org> 上下载的python且python版本 Python 2 >=2.7.9 or Python 3 >=3.4，pip已经安装在python里面了，但你可能需要更新一下pip的版本。  
安装pip，安全的方法是通过官方脚本[get-pip.py](https://bootstrap.pypa.io/get-pip.py)，下载get-pip.py脚本到本地，运行以下命令：
```sh
python get-pip.py
```
也可以通过系统的包管理器来安装pip：  
* CentOS/RHEL
```sh
sudo yum install python-pip
```
* Debian/Ubuntu
```sh
sudo apt-get install python-pip
```
* Fedora
```sh
sudo yum|dnf install python-pip python-wheel
```
#### 更新pip
***
* Linux或者OS X：
```sh
pip install -U pip
```
* Windows:
```sh
python -m pip install -U pip
```
#### pip的使用
***
* 安装python包
```sh
$ pip install SomePackage            # latest version
$ pip install SomePackage==1.0.4     # specific version
$ pip install 'SomePackage>=1.0.4'     # minimum version
```
* Requirements文件安装和生成
“Requirements files”是一个文件，类似于配置文件，里面列出了所有python包的版本，可以通过pip安装requirements文件里面所有的python包而不必一个个的执行安装命令。可以通过如下命令安装requirements文件的python包：
```sh
pip install -r requirments.txt
```
	当部署python程序时，需要把所需要的python包也一并打包部署到生产环境中，如果一项项的查看所需要的包耗时耗力，也不准确。可以通过pip将程序所需要的python包打包生成一个requirements.txt文件，部署的时候直接导入。可以通过如下命令生成requirements.txt文件：
```sh
pip freeze > requiremnts.txt
```
	文件的命令可以随意，但循例一般使用requirments.txt。
* 卸载python包
```sh
pip uninstall SomePackage
```
* 列出已安装的python包
```sh
pip list
pip list --outdated    #列出过时的python包
pip show SomePackage   #显示已安装python包的详细信息
```
