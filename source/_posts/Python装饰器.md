---
title: Python装饰器(一)
date: 2016-04-15 15:01:54
tags: python
categories: 技术
---
#### 装饰器（decorator）
***
装饰器（decorator）其实就是一个函数，一个用来“装饰”函数的函数，返回一个“装饰”之后的函数对象。装饰器经常被用于有切面需求的场景，较为经典的有插入日志、性能测试、缓存、权限校验等。简单的将，函数装饰器就是可以让我们为已有的函数在从种程度上增加额外的功能，即使原函数是不允许修改的。
> Function decorators let us “mark” functions in the source code to enhance their behavior in some way。

装饰器是一个可调用对象，使用其它函数（被装饰的函数）作为参数，进行一些操作，返回被装饰的函数对象或者返回另外一个函数对象来替代被装饰的函数对象，或者甚至返回其它的可调用对象。 
<!--more--> 
下面是一个简单的装饰器例子：  
```python
def decorate(func):
	def inner():
		print('running inner()')
	return inner

@decorate
def target():
	print('running target()')  
```
 
在上面的例子中，函数decorate就是一个简单的装饰器，在上面的例子中也没有做太多的事情，就是打印了一行消息，如果运行target函数，将会有以下输出：  

```
>>>target()
running inner()
```

从上面的输出来看，装饰器已经用另外的函数对象（inner）替换了被装饰的函数（target），被装饰的函数（target）的功能已经和装饰前的功能无关了。  
在上面的例子中，我们使用`@decorate`来调用装饰器，严格来说，装饰器只是python的语法糖（syntactic sugar），我们可以像使用python中其它的可调用对象一样使用装饰器，传入被装饰的函数对象作为参数即可：
```
target = decrotate(target)
```
上面的调用方式和前面的例子产生的效果是一样的，只不过前面的写法更为简便。有时候装饰器确实更为简便，特别是在进行元编程（metaprogramming）的时候。
一般而言，我们会将装饰器和所装饰的函数定义在不同的模块，需要使用装饰器时导入装饰器所在的模块即可。装饰器在所在的模块（module）被导入（import）的时候就开始执行了，即导入装饰器所在模块时就马上执行了，而被装饰的函数在被显式调用时才会执行，这个就是我们所说的导入时（importtime）和运行时（runtime）的区别。  
就像我们上面例子里所看到的，很多的装饰器确实会改变所装饰的函数，通过在函数里面定义另外的函数，然后返回所定义的函数对象来替代被装饰的函数对象。理解函数里面的函数（inner function）首先要理解python里面的闭包（closure）概念。  
#### 闭包（closure）
***
让我们先来看看python里面的变量范围（variable scope）法则，假定有如下函数定义：
```python
def f1(a):
    print(a)
    print(b)
```
如果我们调用函数f1的话，将会有以下输出：
```python
>>> f1(3)
3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in f1
NameError: global name 'b' is not defined
```
这个错误的产生毫无悬念，因为变量b确实没有被定义，如果我们给变量b赋值然后再调用函数f1，它就会正常工作了：
```
>>> b = 6
>>> f1(3)
3
6
```
但如果我们再定义一个函数f2:
```python
def f2(a):
	print(a)
	print(b)
	b = 9
```
并且在调用f2之前给变量b赋值，结果会是怎样呢？
```python
>>> b = 6
>>> f2(3)
3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in f2
UnboundLocalError: local variable 'b' referenced before assignment
```
注意到上面的结果中先输出了3，证明print(a)语句被执行了，但是第二条语句print(b)却没有执行，大大出乎我的意料，开始我以为会输出6（至少java里面会这样），因为全局变量b在print(b)语句之前已经定义并被赋值了。  
这不是一个bug，而是python语言设计时的选择：Python不要求你进行变量声明，但是会假定如果你在一个函数体里面对一个变量进行了赋值（assigned），那么那个变量默认就是局部（local）变量。这可以避免像JavaScript一类语言（也不要求对变量进行声明）所产生的问题：如果你在使用变量前忘记对变量进行声明（如通过var关键字），你可能会直接使用一个你根本不知道的同名变量的值。  
回到前面的例子，如果我们要在函数体内使用全局变量b应该如何操作呢？可以使用global关键字：
```python
def f3(a):
    global b
    print(a)
    print(b)
    b = 9
```
此时调用f3会有如下结果：
```python
>>> b = 6
>>> f3(3)
3
6
>>> b
9
```
可以看到，上面的变量b已经是全局变量了，对其在函数内赋值也改变了其原来的值了。了解了python变量范围法则，下面就可以介绍闭包了（closure）：  
> a closure is a function with an extended scope that encompasses nonglobal variables referenced in the body of the function but not defined there。

简单的说，闭包就是一个函数，此函数具有扩展的变量访问范围，在函数内部可以访问到未在函数里面定义的非全局变量。举一个简单的例子：
```python
def make_averager():
	series = []
	
	def averager(new_value):
		series.append(new_value)
		total = sum(series)
		return total/len(series)
	return averager
	
>>> avg = make_averager()
>>> avg(10)
10.0
>>> avg(11)
10.5
>>> avg(12)
11.0
```
注意到变量series是函数make_averager内的局部变量，因为series 的初始化是在make_averager函数体内。但是调用avg(10)时，make_averager也返回了，并且其局部变量series的范围扩展了，在内部函数averager内部也可以访问了。  
在函数averager内部，series是一个“自由变量”（free variable），即变量不再绑定（bound）到它所定义的局部范围了，访问范围扩展了：  
![Closure](http://7xte88.com2.z0.glb.clouddn.com/closure.png)
总结而言，闭包就是一个函数中的函数，它“维持”（retains）了外层函数定义的“自由变量”（free variables）的内部绑定，从而当闭包（函数）被调用时，它可以自由访问这些变量即使这些变量的定义范围已经不再可达了。就像图中的series变量，如果按照变量定义而言，series是定义在函数make_averager里面的，是一个local变量，它只能在make_averager函数内被访问，在内部函数averager内是不能被访问的，然而通过闭包，在函数averager内部就可以使用series了。
