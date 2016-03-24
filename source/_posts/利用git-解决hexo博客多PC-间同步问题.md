---
title: 利用git 解决hexo博客多PC 间同步问题
date: 2016-03-13 00:12:09
categories: hexo
tags: hexo
---
&nbsp;&nbsp;&nbsp;&nbsp;单位和家里两PC，同时都想更新blog。而由于hexo没有后台，而且全部文件都在本地生成，所以如果公司电脑上发表了A文章后回家又写了篇B文章，在家里上传后你会发现只有B文章而A文章没了（因为家里的PC上没有A文章的md文件），所以多台电脑同时用来写文章的时候，需要解决备份问题。      
&nbsp;&nbsp;&nbsp;&nbsp;备份一般有云盘备份和第三方托管备份的方式，云盘备份将相关文件打包传到云盘里面，在另一台电脑访问时下载到本地即可，在此不赘述。下面介绍的是如何利用第三方托管进行备份，以[git](github.com)为例：    
<!--more-->
1\. 准备工作：公司电脑和家里电脑配置git ssh密钥连接    
2\. 上传blog到git：此项建议先在blog进度最新的PC上进行，否则会有版本冲突，解决也比较麻烦。另外，一般建议blog静态文件和blog源码文件分库存放，之前看见有人说blog静态文件使用master分支，另建hexo分支存放blog源码，还是建议分库存放，因为两部分文件并不存在实际上的版本关系。在PC上建立git ssh密钥连接和建立新库respo在此略过：    
+  编辑.gitignore文件：.gitignore文件作用是声明不被git记录的文件，blog根目录下的.gitignore是hexo初始化是创建的，可以直接编辑，建议.gitignore文件包括以下内容：      
```
.DS_Store      
Thumbs.db      
db.json      
*.log      
node_modules/      
public/      
.deploy*/      
```
public内的文件可以根据source文件夹内容自动生成的，不需要备份。其他日志、压缩、数据库等文件也都是调试等使用，也不需要备份。    
+  初始化仓库：    
```
git init    
git remote add origin <server>    
```
server是仓库的在线目录地址，可以从git上直接复制过来，origin是本地分支，remote add会将本地仓库映射到托管服务器的仓库上。    
+  添加本地文件到仓库并同步到git上：    
```
git add . #添加blog目录下所有文件，注意有个'.'(.gitignore里面声明的文件不在此内)    
git commit -m "hexo source first add" #添加更新说明    
git push -u origin master  #推送更新到git上
```
至此，git库上备份已完成。    
3\. 将git的内容同步到另一台电脑：假设之前将公司电脑中的blog源码内容备份到了git上，现在家里电脑准备同步源码内容。注意，在同步前也要事先建好hexo的环境，不然同步后本地服务器运行时会出现无法运行错误。在建好的环境的主目录运行以下命令：    
```
git init  #将目录添加到版本控制系统中    
git remote add origin <server>  #同上    
git fetch --all  #将git上所有文件拉取到本地    
git reset --hard origin/master  #强制将本地内容指向刚刚同步git云端内容      
```
reset对所拉取的文件不做任何处理，此处不用pull是因为本地尚有许多文件，使用pull会有一些版本冲突，解决起来也麻烦，而本地的文件都是初始化生成的文件，较拉取的库里面的文件而言基本无用，所以直接丢弃。    
4\. 家里电脑生成完文章并部署到服务器上后，此时需要将新的blog源码文件更新到git托管库上，不然公司电脑上无法获取最新的文章。在本地文件中运行以下命令：    
```
git add . #将所有更新的本地文件添加到版本控制系统中    
```
此时可以使用git status查看本地文件的状态。然后对更改添加说明更推送到git托管库上：    
```
git commit -m '更新信息说明'   
git push    
```
至此，家里电脑更新的备份完成。在公司电脑上使用时，只需先运行:    
```
git pull    
```
命令即可，所获取的源码即为最新文件。    
参考链接：<https://segmentfault.com/a/1190000003710980>
