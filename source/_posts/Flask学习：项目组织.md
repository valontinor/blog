---
title: Flask学习：项目组织
date: 2016-04-19 23:19:11
tags: python flask
categories: 技术
---
Flask 是一个使用 Python 编写的轻量级 Web 应用框架。其 WSGI 工具箱采用 Werkzeug ，模板引擎则使用 Jinja2。Flask 也被称为 “microframework” ，因为它使用简单的核心，用 extension 增加其他功能。Flask没有默认使用的数据库、窗体验证工具。然而，Flask保留了扩增的弹性，可以用Flask-extension加入这些功能：ORM、窗体验证工具、文件上传、各种开放式身份验证技术。  
Flask把项目组织的职责交由用户自己，使项目具有很大的灵活性，但是这意味着用户不得不思考怎么组织你的代码。 可以把这个应用放到一个文件中，或者把它分割多个包。然而这两种结构并不适合大多数项目。 这里有一些固定的组织模式，可以遵循它们以便于开发和部署。
<!--more-->
#### 约定
***
版本库（Repository）：应用的根目录，在开始建立时都会初始化为版本控制的目录，以便及时将程序及时推送到本地或远程的版本库。
包（Package）：包含了应用代码的一个包，版本库的一个子目录。
模块（Module）：一个模块是一个简单的、可以被其它Python文件引入的Python文件。一个包由多个模块组成。
#### 项目组织
***
* 单一模块
许多Flask例子里，我们会看到里面把所有的代码放到一个单一文件中，通常是app.py或myapp.py，如官方的示例代码。对于一些微项目或功能单一的项目，这是一个方便的组织方式，比较需要处理的路由（route）少并且代码量也不大。单一模块的应用组织方式通常如下：
```
app.py
config.py
requiremnts.txt
static/
templates/
```
在上述组织中，应用逻辑部分通常会存放在app.py中。
* 包
当项目开始变得复杂时，需要处理的路由越来越多，需要为模型（model）和表单（form）定义单独的类，代码量也越来越大，这个时候如果还采取单一模块的方式就会让人变得焦头烂额，同时让code review变得越来越难。为了解决这个问题，可以把应用中不同的组件分开到单独的、高内聚的一组模块——即包中。
	基于包的组织方式通常如下：
```
config.py
requirments.txt
run.py
instance/
    /config.py
youapp/
    /__init__.py
    /views.py
    /models.py
    /forms.py
    /static/
    /templates/
```
	这种组织模式允许我们有序的整理应用不同组件，将有关模型的定义全部放在models.py，有关路由的定义全部放在views.py里面，有关表单的定义全部放在forms.py里面。
	下面列出了大多数Flask应用都有的基本组件，对于实际的应用可能还会需要一些别的文件，但这适用于大多数Flask应用：  

| 组件 | 作用 | 
| --- | ---- |
|  run.py | 这个文件中用于启动一个开发服务器。它从你的包获得应用的副本并运行它。这不会在生产环境中用到，不过依然在许多Flask开发的过程中看到 |
|requirments.txt| 这个文件列出了应用所依赖的所有Python包，可以用pip freeze命令生成，一般可能需要把它分成生产依赖和开发依赖。|
| config.py | 这个文件包含了应用所需要的大多数配置参数 |
| instance/config.py | 这个文件包含不应该出现在版本控制的配置变量。其中有类似调用密钥和数据库URI连接密码。同样也包括了你的应用中特有的不能放到阳光下的东西。比如，你可能在config.py中设定DEBUG = False，但在你自己的开发机上的instance/config.py设置DEBUG = True。因为这个文件可以在config.py之后被载入，它将覆盖掉DEBUG = False，并设置DEBUG = True。 |
| yourapp/ | 这个包里包括了所开发的应用。 |
| yourapp/__init__.py | 这个文件初始化应用并把所有其他需要的组件组合在一起。 |
| yourapp/views.py | 这个文件定义了所有的路由，如果需要也可以作为一个包(yourapp/views/)，由一些包含了紧密相联的路由的模块组成。 |
| yourapp/models.py | 这个文件定义了应用的模型，如果需要，也可以像views.py一样把它分割成许多模块。 |
| yourapp/static/ | 这个目录包括了公共的资源文件，CSS、JS、img及其它你想通过应用展示出去的静态文件，默认可以通过yourapp.com/static/获取这些文件。 |
| yourapp/templates/ | 这个目录放置着应用的Jinja2模板。 |

* Blueprints
有朝一日你可能会发觉应用里有许多相关的路由了，此时会将views.py分割成一个包并把相关的路由组织成模块，Flask提供了蓝图（blueprints）来更好的组织项目。  
蓝图是按照一定程度上的自组织的方式，作为应用的一部分的组件。 它们表现得就像你的应用下的子应用一样。可以使用不同的蓝图来对应管理面板（admin panel），前端（front-end）和用户面板（user dashboard）。 这使得你按照组件组织视图，静态文件和模板，并在组件间共享模型，表单和你的应用的其他部分。
