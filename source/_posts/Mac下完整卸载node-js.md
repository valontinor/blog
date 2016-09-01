---
title: Mac下完整卸载node.js
date: 2016-08-03 15:51:35
tags: mac nodejs
categories: 技术
---
mac下之前有安装过node，后来我又用brew工具重建了node导致了各种问题，用brew uninstall命令卸载node时又出现"No such keg:/usr/local/Cellar/node"的错误，为了保持一个干净的node环境，我决定卸载掉系统所有的node.js和npm，重新安装。具体操作如下：<!--more-->
1. 进入到/usr/local/lib目录下，删除所有node和node_modules
2. 进入到/usr/local/include目录下，删除所有node和node_modules文件夹
3. 如果你之前也通过brew工具安装node，终端下运行'brew uninstall node'命令
4. 检查当前用户的主目录，看相应的local、lib或者include目录下面是否有node或node_modules，如果有，删除它们

一般经过以上的操作就可以清除掉系统现有的node.js了，但还有一些额外的工作可能需要：
1. sudo rm /usr/local/bin/npm
2. sudo rm /usr/local/share/man/man1/node.1
3. sudo rm /usr/local/lib/dtrace/node.d
4. sudo rm -rf ~/.npm
5. sudo rm -rf ~/.node-gyp
6. sudo rm /opt/local/bin/node
7. sudo rm /opt/local/include/node
8. sudo rm -rf /opt/local/lib/node_modules
9. sudo rm -rf /usr/local/include/node/

上面的某些工作可能不需要，如opt目录下面可能并没有local子目录等，这些这是辅助性操作，以便更好的删除node.js.    
删除掉node.js后，便可以通过brew重新安装node.js了，重新安装时可能会提示一些link无法建立、一些link已经存在等，并给出操作建议，按照给出的提示建议操作即可.
