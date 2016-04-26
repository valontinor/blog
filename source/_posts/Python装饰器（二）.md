---
title: Python装饰器（二）
date: 2016-04-26 11:06:55
tags: python
categories: 技术
---
#### 简单的装饰器
***
装饰器是一个可调用对象，使用其它函数（被装饰的函数）作为参数，进行一些操作，返回被装饰的函数对象或者返回另外一个函数对象来替代被装饰的函数对象，或者甚至返回其它的可调用对象。  
上文介绍了Python中的闭包（closure），它是装饰器的基础。下面先来看一个简单的装饰器例子：
<!--more-->
```python
import time

def clock(func):
    def clocked(*args):
        t0 = time.perf_counter()
        result = func(*args)
        elapsed = time.perf_counter() - t0
        name = func.__name__
        arg_str = ', '.join(repr(arg) for arg in args)
        print('[%0.8fs] %s(%s)) -> %r' % (elapsed, name, arg_str, result))
        return result
    return clocked


@clock
def factorial(n):
    return 1 if n < 2 else n*factorial(n-1)
```
前面讲过，@是python的语法糖，上面的操作和下面的是等价的：
```
def factorial(n):
    return 1 if n < 2 else n*factorial(n-1)
   
factorial = clock(factorial)
```
它们都是用factorial函数作为装饰器clock的func参数，然后创建并直接返回clocked函数。实际上，如果你在命令行里面检查一下装饰后的factorial函数的__name__属性，你会发现它已经发生了变化：
```python
>>> factorial.__name__
'clocked'
```
factorial的__name__属性已经不是之前的函数名了，而是指向了（reference）clocked函数。另外，factorial的__doc__属性也发生了变化。如果想让装饰后的函数还保持以前的__name__和__doc__等属性，python内置了一个装饰器：functools.wraps，使用它来装饰装饰器的内部函数，就可以保持原有属性不变：
```python
import time
import functools

def clock(func):
    @functools.wraps(func)
    def clocked(*args):
        t0 = time.perf_counter()
        result = func(*args)
        elapsed = time.perf_counter() - t0
        name = func.__name__
        arg_str = ', '.join(repr(arg) for arg in args)
        print('[%0.8fs] %s(%s)) -> %r' % (elapsed, name, arg_str, result))
        return result
    return clocked
```
#### 带参数的装饰器
***
python在执行装饰器语法糖时，默认将被装饰函数的函数名作为第一个参数传给装饰器，所以，怎么样让装饰器接受额外的参数呢？答案就是，构建一个装饰器工厂函数（decorator factory），接受这些额外参数，执行相关的操作，然后返回一个装饰器。
还是拿之前的clock装饰器来示例吧，如果我们想为装饰器clock添加一个特性：用户可自定义输出格式，则可以如下构建装饰器clock：
```python
import time

DEFAULT_FMT = '[{elapsed:0.8f}s] {name}({args}) -> {result}'

def clock(fmt=DEFAULT_FMT):
	def decorate(func):
		def clocked(*_args):
			t0 = time.time()
			_result = func(*_args)
			elapsed = time.time() - t0
			name = func.__name__
			args = ', '.join(repr(arg) for arg in _args)
			result = repr(_result)
			print(fmt.format(**locals()))
			return _result
		return clocked
	return decorate


if __name__ == '__main__':

	@clock()
	def snooze(seconds):
		time.sleep(seconds)
	
	for i in range(3):
		snooze(.123)
```
上面的例子中，clock就是一个参数话的装饰器工厂，其返回的是一个装饰器；decorate才是真正的装饰器，clocked是装饰器的闭包，包装（wrap）了被装饰的函数func。注意到在调用装饰器工厂函数时，因其返回的是一个装饰器，所以即使使用默认参数（如例子中的fmt）也要使用函数的调用方式，带上()，否则装饰器无法生效。
  
文中的例子来自于[O'Reilly](http://www.oreilly.com/)的[Fluent Python](https://www.amazon.cn/Fluent-Python-Ramalho-Luciano/dp/1491946008/ref=sr_1_1?ie=UTF8&qid=1461654214&sr=8-1&keywords=fluent+python)。

