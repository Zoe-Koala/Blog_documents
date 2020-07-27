---
title: python
date: 2020-07-17 21:00:17
tags: python
categories: python
---
python的学习
<!-- more -->
# python 基础
## python函数
默认参数可以简化函数的调用。设置默认参数时，有几点要注意：
1. 必选参数在前，默认参数在后，否则Python的解释器会报错
2. 如何设置默认参数:
当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。


**定义默认参数要牢记一点：默认参数必须指向不变对象！**

可变参数就是传入的参数个数是可变的，可以是1个、2个到任意个，还可以是0个。
要定义出这个函数，我们必须确定输入的参数。由于参数个数不确定，我们首先想到可以把a，b，c……作为一个list或tuple传进来，这样，函数可以定义如下：
```
def calc(numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
但是调用的时候，需要先组装出一个list或tuple
```
>>> calc([1, 2, 3])
14
>>> calc((1, 3, 5, 7))
84
```
如果利用可变参数，调用函数的方式可以简化成这样：
```
>>> calc(1, 2, 3)
14
>>> calc(1, 3, 5, 7)
84
```
所以，我们把函数的参数改为可变参数：
```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
定义可变参数和定义一个list或tuple参数相比，仅仅在参数前面加了一个`*`号。在函数内部，参数numbers接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数


**参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数**
`*args`是*可变参数*，args接收的是一个tuple；
`**kw`是*关键字参数*，kw接收的是一个dict。
定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数

## python 迭代
默认情况下，dict迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`
如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断：
```
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：
```
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

## python生成器
创建一个生成器generator
1. 把一个列表生成式的`[]`改成`()`，就创建了一个`generator`
如果要一个一个打印generator的每个值，可以通过next()函数获得generator的下一个返回值
generator可迭代
2. 一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个`generator`
`函数`是`顺序`执行，遇到`return语句`或者`最后一行函数语句`就返回。而变成`generator`的函数，在`每次调用next()的时候执行`，遇到`yield语句返回`，*再次执行时从上次返回的yield语句处继续执行。*

## python迭代器
可以直接作用于`for循环`的对象统称为`可迭代对象`：`Iterable`
`生成器`不但可以作用于`for循环`，还可以被next()函数不断调用并返回下一个值，*直到最后抛出StopIteration错误表示无法继续返回下一个值了。*

可以被`next()函数调用并不断返回下一个值的对象`称为`迭代器`：`Iterator`

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：
```
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

**小结**
凡是可作用于`for`循环的对象都是`Iterable`类型；
凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个`惰性计算`的序列；
集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过`iter()`函数获得一个Iteratobangwr对象。

## 函数式编程
### 高阶函数 Higher-order function
函数本身也可以赋值给变量，即：变量可以指向函数
函数名其实就是指向函数的变量！

一个函数可以接收另一个函数作为参数，这种函数就称之为**高阶函数**

#### map/reduce
`map()`函数接收两个参数，一个是函数，一个是`Iterable`，map将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收`两个参数`，`reduce`把结果继续和序列的下一个元素做`累积计算`

#### filter
Python内建的`filter()`函数用于过滤序列。
`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素

`filter()`函数返回的是一个`Iterator`，也就是一个`惰性序列`，所以要强迫filter()完成计算结果，需要用`list()`函数获得所有结果并返回list

#### sorted
Python内置的`sorted()`函数就可以对list进行排序

sorted()函数也是一个高阶函数，它还可以接收一个`key`函数来实现自定义的排序
`sorted([36, 5, -12, 9, -21], key=abs)`
要进行反向排序，不必改动key函数，可以传入第三个参数`reverse=True`

### 返回函数
**函数作为返回值**
高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回

**闭包**
**返回函数不要引用任何循环变量，或者后续会发生变化的变量**










