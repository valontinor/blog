---
title: PEP8(Python代码风格指南)（一）
date: 2016-07-07 15:36:39
tags: python
categories: 技术
---
介绍（Introduction）
***
这篇文档翻译自PEP8，说明了Python主要发行版中标准库代码所遵守的规范，旨在提供一个python的编码规范指南。很多项目都有自己的一套风格，本文旨在为其提供一个参考。
<!--more-->

代码布局（Code Lay-Out)
***
1.&nbsp;&nbsp;&nbsp;&nbsp;缩进（Indentation）
* 每个缩进级别采用4个空格。
* 续行应将换行的元素垂直对齐，可以使用python的圆括号、方括号、花括号中的隐式连接符，也可以使用悬挂式缩进。当采用悬挂式缩进方式时，应该符合下列考虑条件：第一行不应该有参数，而且应该能够清楚无误地将后面采用缩进的行作为后续行从其它行中区分出来。
```python
# Aligned with opening delimiter.
# 使用分隔符来对齐
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# More indentation included to distinguish this from the rest.
# 多使用一些缩进来与其他行进行区分
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# Hanging indents should add a level.
# 悬挂式缩进应该增加一个级别。
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```
* 对于续行而言，4个空格的规定是可选的
```python
# Hanging indents *may* be indented to other than 4 spaces.
# 悬挂式缩进的空格个数*可以*不为4。
foo = long_function_name(
  var_one, var_two,
  var_three, var_four)
```
* 当if语句由于条件判断部分太长而需要跨多行时，值得注意的是，这一两个字母的关键字（比如if）加上一个空格再加上一个左括号，就使得需要在这一续行的条件语句的前面添加4个空格的缩进，这是很自然的事情。这可能会与if语句所嵌套的那套分支代码产生视觉上的冲突，因为很自然的，分支语句也是缩进4个空格的。对于如何（或者是否需要）将条件判断语句与if语句中的分支语句明确地区分出来，本PEP文档没有明确的立场。对于这种情况，下列方式都是可接受的，但又不限于此：
```python
# No extra indentation.
# 没有多余缩进
if (this_is_one_thing and
    that_is_another_thing):
    do_something()

# Add a comment, which will provide some distinction in editors
# supporting syntax highlighting.
# 添加一条注释来在编辑器中提供一些不同之处
# 支持与语法高亮
if (this_is_one_thing and
    that_is_another_thing):
    # Since both conditions are true, we can frobnicate.
    # 当两种条件都为true时，我们可以执行操作
    do_something()

# Add some extra indentation on the conditional continuation line.
# 在条件判断语句所在行添加一些额外的缩进
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```
* 在多行代码结构中，右花括号/中括号/小括号可以排在列出的元素的最后一行的第一个非空格字符的下面，也可以排在该多行代码结构的起始行的第一个字符下面：
```
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
    

my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```
2.&nbsp;&nbsp;&nbsp;&nbsp;Tab还是空格（Space）
* 空格是首选的缩进方法
* Tab键只能用在那些已经使用tab符来进行缩进的代码中用来保持一致性
* python 3不允许将tab和空格键混合使用用于缩进
* python 2中混合使用tab和空格键来进行缩进的代码必须转换成仅仅使用空格的方式
* 当使用-t选项来调用python 2的命令行解释器时，它将对那些非法混合使用tab和空格键的代码给出警告(warnings)；而当使用-tt选项时，这些警告（warning）将会变成错误（error）。强烈推荐使用这些选项来运行解释器。

3.&nbsp;&nbsp;&nbsp;&nbsp;行最大长度（Maximum Line Length）
* 将所有行的最大字符数控制在79以内。
* 对于连续的且结构上限制更少的大段字符（如文档字符串（docstring）或注释），应将其控制在72字符以内。
* 限制编辑器的窗口宽度尽可能让更多的文件同时打开在多个窗口；当使用代码评审工具（code review tools）时，让两个不同版本的代码显示在相邻的窗口效果会更好。
* 很多工具的默认自动换行会破坏代码的可视化结构，使得代码更加难以理解。选择这个数值（79）是为了避免在窗口宽度设置为80的编辑器中出现代码换行的情况，即使在这些编辑器中可能会在最后一列放置一个记号。而一些基于web的编辑器甚至不提供动态换行功能。
* 一些团队可能会希望更长字符的代码行。当代码仅仅是由一个团队维护时，可以在团队成员达成一致时让行长度增加到80～100个字符（实际上最大的行长是99），但注释和文档字符串仍建议为72最佳。
* python标准库是比较保守的，它要求将每行限制为79个字符，文档字符（docstring）和注释则限制为72个字符。
* 一种推荐的换行方式是利用python的圆括号、方括号和花括号中的隐式方式续行，长行可以通过在括号内换行来分成多行。这种续行的方式应优于使用右斜杠（\）的方式。
* 有的时候使用右斜杠（\）换行是可以接受的方式，当不能使用隐式的续行方式时，右斜杠也是一种可以接受的方法：
```python
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
```
* 确保对续行进行适当的缩进

4.&nbsp;&nbsp;&nbsp;&nbsp;二元操作符（binary operator）及逻辑运算符换行的问题
数年来所推崇的二元操作符的换行方式是在二元操作符后面换行，但这牺牲了代码的可读性：运算符分散在不同的列，运算被分成当前行操作数与之前行之间的运算，眼睛不得不做一些额外的工作来确认当前行的操作数到底是加法操作还是减法操作。
为了解决可读性性的问题，数学家们建议打破之前的惯例，在二元操作符前面进行换行。在python编码规范里面，在二元操作符前面或者后面换行都是可以的，只要团队的风格保持一致就可以了。
```python
# Yes: easy to match operators with operands
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

逻辑运算符附件的换行处最好是在运算符之后而不是之前，如：

```python
class Rectangle(Blob):

    def __init__(self, width, height,
                 color='black', emphasis=None, highlight=0):
        if (width == 0 and height == 0 and
                color == 'red' and emphasis == 'strong' or
                highlight > 100):
            raise ValueError("sorry, you lose")
        if width == 0 and height == 0 and (color == 'red' or
                                           emphasis is None):
            raise ValueError("I don't think so -- values are %s, %s" %
                             (width, height))
        Blob.__init__(self, width, height,
                      color, emphasis, highlight)
```
5.&nbsp;&nbsp;&nbsp;&nbsp;空行（Blank Line）
* 使用两个空行分隔最高层的函数和类
* 使用一个空行分隔类中定义的方法
* 可以多使用一些空行（有限制的使用）来对一组相关的函数进行分隔。另外，在一些相关的单行代码（如一组dummy的实现）之间的空行也可以省略
* 函数里面可以用空行来区分一些逻辑相关的代码块，但要有节制的使用
* python把ctrl-L换页符也作为空白符号，许多工具把它当作页分隔符来进行处理，所以可以使用它们来将文件进行分页，将相关联的代码块放到一页里。注意：有一些编辑器和基于web的代码查看器可能不把ctrl-L当作分页，会在相应的地方显示一个图像字符

6.&nbsp;&nbsp;&nbsp;&nbsp;源文件代码（Source File Encoding）
* python的核心发行版中的代码应该一直使用UTF-8（python2中使用ASCII）编码方式
* 使用ASCII（python2）或者UTF-8（python3）编码的文件不需要在开头部分添加编码申明
* 在标准库中，只有用作测试目的，或者注释或文档字符串（docstring）中需要提及作者名字而不得不使用非ASCII字符时才能使用非默认的编码；否则在字符串文字（string literals）中包括非ASCII字符时，推荐使用\x、\u、\U或\N等转移字符。
* 对于python3及其以后的版本，标准库应遵循以下原则：python标准库中的所有标识符都必须只采用ASCII编码的标识符，在可行的条件下也应当使用英文词（很多情况下，使用的缩写和技术术语词都不是英文）。另外，字符串文字和注释应该只包含ASCII编码。可以有两种例外情况：
	* （a）测试情况下为了测试非ASCII编码的特性
	* （b）作者名字：作者名字不是由拉丁字母组成的也必须提供一个拉丁音译名。
	
7.&nbsp;&nbsp;&nbsp;&nbsp;Imports语句
* import语句应该分行写而不是都写在一行，如：
```python
#分开写
import os
import sys

#不要写在一行
import sys, os

#同一包中的模块可以写在一行
from subprocess import Pepen, PIPE
```

* import语句应该放在代码文件的开头，位于模块注释和文档字符串（docstring）之后，模块全局变量（globals）和常量（constants）声明之前。
	应该按照下面的顺序分组来写import语句：
	（1）import标准库    
	（2）import相关第三方库    
	（3）import具体的本地应用/库    
	不同分组的import之间用空行隔开
* 推荐使用绝对路径的import，因为这样通常更易读，另外在import系统没有被正确配置时（例如一个包里面的文件夹以sys.path结尾时）也更易维护（或者当出现错误时至少可以给出良好的错误提示信息）：
```python
import mkpkg.sibling
from mypkg import sibling
from mypkg.sibling import example
```
	然而，除了绝对路径的import，显式的相对路径import也是一种可接受的替代方式，特别是当处理复杂的包布局（package layouts）时，采用绝对路径的import会显得啰嗦：
```python
from . import sibling
from .sibling import example
```
	隐式的相对路径import应该禁止使用，并且在python3中已经被去掉了。
* 当从一个包括类的模块中import一个类时，通常可以这样写：
```python
from myclass import MyClass
from foo.bar.yourclass import YourClass
```
	但如果和本地命名的拼写产生了冲突，应当直接import模块：
```python
import myclass
import foo.bar.yourclass
```
	然后在程序中使用“myclass.MyClass”和“foo.bar.yourclass.YourClass”方式来引用类。
* 避免使用通配符式的import（from <module> import *），因为这样会造成在当前命名空间出现的命名含义不清晰，给读者和许多自动化工具造成困扰。有一种可以正当使用通配符的情形：将一个内部接口重新发布成公共API的一部分（比如，使用备用的加速模块中的定义去覆盖纯python实现的接口，被覆盖的定义恰好不能提前知晓）。
当使用这种方式重新发布命名时，后面关于公共和内部接口的部分仍然适用。