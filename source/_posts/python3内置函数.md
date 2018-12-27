---
title: python内置函数
date: 2018-12-25 11:58:11
tags: python library
---

### ``abs(x)``

返回参数的绝对值，参数必须是整形或者浮点型，如果是复杂类型数据，则返回参数大小

### ``all(iterable)``

如果iterable所有元素都为true（或者iterable为空），则返回true，相当于：

```python
def all(iterable):
    for element in iterable:
        if not element:
           return False
    return True
```

### ``any(iterable)``

如果任何一个元素是true则返回``True``，如果iterable是空则返回``False``,相当于:

```python
def any(iterable):
    for element inf iterable:
        if element:
            return True
    return False
```

### ``ascii(object)``

返回ascii

### ``bin(x)``

将integer类型的数字转换成``"0b"``前缀的二进制字符串，结果是有效的python表达式，如果x不是integer类型，则必须定义一个返回值为integer的函数``__index__()``

```python
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
```

你可以使用以下方式决定是否需要前缀``"0b"``

```python
>>>format(14,'#b'), format(14,'b')
('0b1110','1110')
>>>f'{14:#b}', f'{14:b}'
('0b1110','1110')
```

### ``breakpoint(*args, **kws)``

debug断点调试，实际调用``sys.breakpointhook()``

### ``class bytearray([source[, encoding[, errors]]])``

### ``class bytes([source[, encoding[, errors]]])``

### ``callable(object)``

### ``chr(i)``

返回unicode 为i 的字符串

### ``@classmethod``

将方法转换成类的方法

```python
class C:
    @classmethod
    def f(cls,arg1,arg2, ...): ...
```

> 和``@staticmethod``的区别，``@classmethod``参数必须带有实体参数，实体可以调用其他函数, ``@staticmethod`` 相当于静态方法

### ``compile(source, filename, mode, flags=0, dont_inherit=False, optimize=-1)``

将源代码编译成代码或者AST对象，代码对象可以由exec（）或eval（）执行。 源代码可以是普通字符串，字节字符串或AST对象

### ``class complex([real[, imag]])``

### ``delattr(object, name)``

```python
delattr(x, 'foobar')
#@is equivalent to 
del x.foobar
```

### ``class dict(**kwarg)``

### ``class dict(mapping, **kwarg)``

### ``class dict(iterable, **kwarg)``

### ``dir([object])``

### ``divmod(a, b)``

返回值为(a整除b, a整除b取余)，如果a<b 返回(0,a)

### ``enumerate(iterable, start=0)``

枚举。
```python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]

#相当于
def enumerate(sequence, start=0):
    n = start
    for elem in sequence:
        yield n, elem
        n += 1
```

### ``eval(expression, globals=None, locals=None)``

执行expression表达式的代码

expression必选参数,globals和locals可选参数; globals为字典类型 locals为map类型

### ``exec(object[, globals[, locals]])``

支持执行python动态代码 object参数必须是string或者 code对象，

### ``filter(function, iterable)``

filter() 函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。

>Pyhton2.7 返回列表，Python3.x 返回迭代器对象

### ``class float([x])``

将integer或者字符串转换成float

```python
>>> float('+1.23')
1.23
>>> float('   -12345\n')
-12345.0
>>> float('1e-003')
0.001
>>> float('+1E6')
1000000.0
>>> float('-Infinity')
-inf
```

### ``format(value[, format_spec])``

字符串格式化的功能,format 函数可以接受不限个参数，位置可以不按顺序

```python
>>>"{} {}".format("hello", "world")    # 不设置指定位置，按默认顺序
'hello world'
 
>>> "{0} {1}".format("hello", "world")  # 设置指定位置
'hello world'
 
>>> "{1} {0} {1}".format("hello", "world")  # 设置指定位置
'world hello world'

print("网站名：{name}, 地址 {url}".format(name="菜鸟教程", url="www.runoob.com"))
 
# 通过字典设置参数
site = {"name": "菜鸟教程", "url": "www.runoob.com"}
print("网站名：{name}, 地址 {url}".format(**site))
 
# 通过列表索引设置参数
my_list = ['菜鸟教程', 'www.runoob.com']
print("网站名：{0[0]}, 地址 {0[1]}".format(my_list))  # "0" 是必须的
```
### ``class frozenset([iterable])``

返回一个冻结的集合，冻结后集合不能再添加或删除任何元素

```python
>>>a = frozenset(range(10))     # 生成一个新的不可变集合
>>> a
frozenset([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> b = frozenset('runoob') 
>>> b
frozenset(['b', 'r', 'u', 'o', 'n'])   # 创建不可变集合
>>>
```

### ``getattr(object, name[, default])``

函数用于返回一个对象属性值

```python
>>>class A(object):
...     bar = 1
... 
>>> a = A()
>>> getattr(a, 'bar')        # 获取属性 bar 值
1
>>> getattr(a, 'bar2')       # 属性 bar2 不存在，触发异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'A' object has no attribute 'bar2'
>>> getattr(a, 'bar2', 3)    # 属性 bar2 不存在，但设置了默认值
3
>>>
```

### ``globals()``

会以字典类型返回当前位置的全部全局变量

```python
>>>a='runoob'
>>> print(globals()) # globals 函数返回一个全局变量的字典，包括所有导入的变量。
{'__builtins__': <module '__builtin__' (built-in)>, '__name__': '__main__', '__doc__': None, 'a': 'runoob', '__package__': None}
```

### ``hasattr(object, name)``

用于判断对象是否包含对应的属性

```python
class Coordinate:
    x = 10
    y = -5
    z = 0
 
point1 = Coordinate() 
print(hasattr(point1, 'x'))
print(hasattr(point1, 'y'))
print(hasattr(point1, 'z'))
print(hasattr(point1, 'no'))  # 没有该属性
```

### ``hash(object)``

```python
>>>hash('test')            # 字符串
2314058222102390712
>>> hash(1)                 # 数字
1
>>> hash(str([1,2,3]))      # 集合
1335416675971793195
>>> hash(str(sorted({'1':1}))) # 字典
7666464346782421378
>>>
```

### ``help([object])``

用于查看函数或模块用途的详细说明

```python
>>>help('sys')             # 查看 sys 模块的帮助
……显示帮助信息……
 
>>>help('str')             # 查看 str 数据类型的帮助
……显示帮助信息……
 
>>>a = [1,2,3]
>>>help(a)                 # 查看列表 list 帮助信息
……显示帮助信息……
 
>>>help(a.append)          # 显示list的append方法的帮助
……显示帮助信息……
```

### ``hex(x)``

用于将10进制整数转换成16进制，以字符串形式表示

```python
>>>hex(255)
'0xff'
>>> hex(-42)
'-0x2a'
>>> hex(1L)
'0x1L'
>>> hex(12)
'0xc'
>>> type(hex(12))
<class 'str'>      # 字符串
```

### ``id([object])``

函数用于获取对象的内存地址。

```python
>>>a = 'runoob'
>>> id(a)
4531887632
>>> b = 1
>>> id(b)
140588731085608
```

### ``input([prompt])``

```python
>>>a = input("input:")
input:123                  # 输入整数
>>> type(a)
<class 'str'>              # 字符串
>>> a = input("input:")    
input:runoob              # 正确，字符串表达式
>>> type(a)
<class 'str'>             # 字符串
```

### ``class int(x, base=10)``

将一个字符串或数字转换为整型

```python
>>>int()               # 不传入参数时，得到结果0
0
>>> int(3)
3
>>> int(3.6)
3
>>> int('12',16)        # 如果是带参数base的话，12要以字符串的形式进行输入，12 为 16进制
18
>>> int('0xa',16)  
10  
>>> int('10',8)  
8
```

### ``isinstance(object, classinfo)``

判断一个对象是否是一个已知的类型，类似 type()

>isinstance() 与 type() 区别：
></br>type() 不会认为子类是一种父类类型，不考虑继承关系。
></br>isinstance() 会认为子类是一种父类类型，考虑继承关系。
></br>如果要判断两个类型是否相同推荐使用 isinstance()。

```python
>>>a = 2
>>> isinstance (a,int)
True
>>> isinstance (a,str)
False
>>> isinstance (a,(str,int,list))    # 是元组中的一个返回 True
True
```

**type() 与 isinstance()区别：**

```python
class A:
    pass
 
class B(A):
    pass
 
isinstance(A(), A)    # returns True
type(A()) == A        # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```

### ``issubclass(class, classinfo)``

判断参数 class 是否是类型参数 classinfo 的子类

```python
class A:
    pass
class B(A):
    pass
    
print(issubclass(B,A))    # 返回 True
```

### ``iter(object[, sentinel])``

用来生成迭代器

>sentinel -- 如果传递了第二个参数，则参数 object 必须是一个可调用的对象（如，函数），此时，iter 创建了一个迭代器对象，每次调用这个迭代器对象的__next__()方法时，都会调用 object。

```python
>>>lst = [1, 2, 3]
>>> for i in iter(lst):
...     print(i)
... 
1
2
3
```

### ``len( s )``

返回对象（字符、列表、元组等）长度或项目个数

```python
>>>str = "runoob"
>>> len(str)             # 字符串长度
6
>>> l = [1,2,3,4,5]
>>> len(l)               # 列表元素个数
5
```

### ``list( seq )``

将元组或字符串转换为列表

元组与列表是非常类似的，区别在于元组的元素值不能修改，元组是放在括号中，列表是放于方括号中

```python
aTuple = (123, 'Google', 'Runoob', 'Taobao')
list1 = list(aTuple)
print ("列表元素 : ", list1)

str="Hello World"
list2=list(str)
print ("列表元素 : ", list2)
```

### ``locals()``

以字典类型返回当前位置的全部局部变量

对于函数, 方法, lambda 函式, 类, 以及实现了 __call__ 方法的类实例, 它都返回 True

```python
>>>def runoob(arg):    # 两个局部变量：arg、z
...     z = 1
...     print (locals())
... 
>>> runoob(4)
{'z': 1, 'arg': 4}      # 返回一个名字/值对的字典
>>>
```

### ``map(function, iterable, ...)``

根据提供的函数对指定序列做映射

第一个参数 function 以参数序列中的每一个元素调用 function 函数，返回包含每次 function 函数返回值的新列表

Python 2.x 返回列表。

Python 3.x 返回迭代器。

```python
>>>def square(x) :            # 计算平方数
...     return x ** 2
... 
>>> map(square, [1,2,3,4,5])   # 计算列表各个元素的平方
[1, 4, 9, 16, 25]
>>> map(lambda x: x ** 2, [1, 2, 3, 4, 5])  # 使用 lambda 匿名函数
[1, 4, 9, 16, 25]
 
# 提供了两个列表，对相同位置的列表数据进行相加
>>> map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
[3, 7, 11, 15, 19]
```

### ``max( x, y, z, .... )``

返回给定参数的最大值，参数可以为序列。

```python
print ("max(80, 100, 1000) : ", max(80, 100, 1000))
print ("max(-20, 100, 400) : ", max(-20, 100, 400))
print ("max(-80, -20, -10) : ", max(-80, -20, -10))
print ("max(0, 100, -400) : ", max(0, 100, -400))
```

### ``memoryview(obj)``

返回给定参数的内存查看对象(Momory view)

**Python2.x 应用：**

```python
>>>v = memoryview('abcefg')
>>> v[1]
'b'
>>> v[-1]
'g'
>>> v[1:4]
<memory at 0x77ab28>
>>> v[1:4].tobytes()
'bce'
```

**Python3.x 应用：**

```python
>>>v = memoryview(bytearray("abcefg", 'utf-8'))
>>> print(v[1])
98
>>> print(v[-1])
103
>>> print(v[1:4])
<memory at 0x10f543a08>
>>> print(v[1:4].tobytes())
b'bce'
>>>
```

### ``min( x, y, z, .... )``

返回给定参数的最小值，参数可以为序列

### ``next(iterator[, default])``

返回迭代器的下一个项目。

### ``oct(x)``

函数将一个整数转换成8进制字符串。

```python
>>>oct(10)
'012'
>>> oct(20)
'024'
>>> oct(15)
'017'
>>>
```

### ``open()``

open() 函数常用形式是接收两个参数：文件名(file)和模式(mode)。

``open(file, mode='r')``

完整的语法格式为：

``open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)``

参数说明:
* file: 必需，文件路径（相对或者绝对路径）。
* mode: 可选，文件打开模式
* buffering: 设置缓冲
* encoding: 一般使用utf8
* errors: 报错级别
* newline: 区分换行符
* closefd: 传入的file参数类型
* opener:

### ``ord(c)``

函数是 chr() 函数（对于 8 位的 ASCII 字符串）的配对函数，它以一个字符串（Unicode 字符）作为参数，返回对应的 ASCII 数值，或者 Unicode 数值。

返回值是对应的十进制整数。

```python
>>>ord('a')
97
>>> ord('€')
8364
>>>
```

### ``pow(x, y[, z])``

返回 xy（x的y次方） 的值

函数是计算x的y次方，如果z在存在，则再对结果进行取模，其结果等效于pow(x,y) %z

> 注意：pow() 通过内置的方法直接调用，内置方法会把参数作为整型，而 math 模块则会把参数转换为 float。

```python
import math   # 导入 math 模块

print ("math.pow(100, 2) : ", math.pow(100, 2))
# 使用内置，查看输出结果区别
print ("pow(100, 2) : ", pow(100, 2))
print ("math.pow(100, -2) : ", math.pow(100, -2))
print ("math.pow(2, 4) : ", math.pow(2, 4))
print ("math.pow(3, 0) : ", math.pow(3, 0))
```

    math.pow(100, 2) :  10000.0
    pow(100, 2) :  10000
    math.pow(100, -2) :  0.0001
    math.pow(2, 4) :  16.0
    math.pow(3, 0) :  1.0

### ``print(*objects, sep=' ', end='\n', file=sys.stdout)``

> print 在 Python3.x 是一个函数，但在 Python2.x 版本不是一个函数，只是一个关键字。

参数

* objects -- 复数，表示可以一次输出多个对象。输出多个对象时，需要用 , 分隔。
* sep -- 用来间隔多个对象，默认值是一个空格。
* end -- 用来设定以什么结尾。默认值是换行符 \n，我们可以换成其他字符串。
* file -- 要写入的文件对象。

```python
>>>print(1)  
1  
>>> print("Hello World")  
Hello World  
 
>>> a = 1
>>> b = 'runoob'
>>> print(a,b)
1 runoob
 
>>> print("aaa""bbb")
aaabbb
>>> print("aaa","bbb")
aaa bbb
>>> 
 
>>> print("www","runoob","com",sep=".")  # 设置间隔符
www.runoob.com
```

### ``class property([fget[, fset[, fdel[, doc]]]])``

在新式类中返回属性值

参数

* fget -- 获取属性值的函数
* fset -- 设置属性值的函数
* fdel -- 删除属性值函数
* doc -- 属性描述信息

```python
class C(object):
    def __init__(self):
        self._x = None
 
    def getx(self):
        return self._x
 
    def setx(self, value):
        self._x = value
 
    def delx(self):
        del self._x
 
    x = property(getx, setx, delx, "I'm the 'x' property.")
```

### ``range()``

``range(stop)``

``range(start, stop[, step])``

参数说明:

* start: 计数从 start 开始。默认是从 0 开始。例如range（5）等价于range（0， 5）;
* stop: 计数到 stop 结束，但不包括 stop。例如：range（0， 5） 是[0, 1, 2, 3, 4]没有5
* step：步长，默认为1。例如：range（0， 5） 等价于 range(0, 5, 1)

```python
>>>range(5)
range(0, 5)
>>> for i in range(5):
...     print(i)
... 
0
1
2
3
4
>>> list(range(5))
[0, 1, 2, 3, 4]
>>> list(range(0))
[]
>>>
```
```python
>>>list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
>>> list(range(0, 10, 2))
[0, 2, 4, 6, 8]
>>> list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> list(range(1, 0))
[]
>>>
>>>
```

### ``repr(object)``

将对象转化为供解释器读取的形式.返回一个对象的 string 格式。

```python
>>>s = 'RUNOOB'
>>> repr(s)
"'RUNOOB'"
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'};
>>> repr(dict)
"{'google': 'google.com', 'runoob': 'runoob.com'}"
>>>
```

### ``reversed(seq)``

返回一个反转的迭代器。

```python
# 字符串
seqString = 'Runoob'
print(list(reversed(seqString)))
 
# 元组
seqTuple = ('R', 'u', 'n', 'o', 'o', 'b')
print(list(reversed(seqTuple)))
 
# range
seqRange = range(5, 9)
print(list(reversed(seqRange)))
 
# 列表
seqList = [1, 2, 4, 3, 5]
print(list(reversed(seqList)))

...

['b', 'o', 'o', 'n', 'u', 'R']
['b', 'o', 'o', 'n', 'u', 'R']
[8, 7, 6, 5]
[5, 3, 4, 2, 1]
```

### ``round( x [, n]  )``

返回浮点数x的四舍五入值

n -- 表示从小数点位数，其中 x 需要四舍五入，默认值为 0。

```python
print ("round(70.23456) : ", round(70.23456))
print ("round(56.659,1) : ", round(56.659,1))
print ("round(80.264, 2) : ", round(80.264, 2))
print ("round(100.000056, 3) : ", round(100.000056, 3))
print ("round(-100.000056, 3) : ", round(-100.000056, 3))

...
round(70.23456) :  70
round(56.659,1) :  56.7
round(80.264, 2) :  80.26
round(100.000056, 3) :  100.0
round(-100.000056, 3) :  -100.0
```

### ``class set([iterable])``

数创建一个无序不重复元素集，可进行关系测试，删除重复数据，还可以计算交集、差集、并集等

```python
>>>x = set('runoob')
>>> y = set('google')
>>> x, y
(set(['b', 'r', 'u', 'o', 'n']), set(['e', 'o', 'g', 'l']))   # 重复的被删除
>>> x & y         # 交集
set(['o'])
>>> x | y         # 并集
set(['b', 'e', 'g', 'l', 'o', 'n', 'r', 'u'])
>>> x - y         # 差集
set(['r', 'b', 'u', 'n'])
>>>
```

### ``setattr(object, name, value)``

对应函数 getattr()，用于设置属性值，该属性必须存在

```python
>>>class A(object):
...     bar = 1
... 
>>> a = A()
>>> getattr(a, 'bar')          # 获取属性 bar 值
1
>>> setattr(a, 'bar', 5)       # 设置属性 bar 值
>>> a.bar
5
```

### ``class slice(stop)``

### ``class slice(start, stop[, step])``

实现切片对象，主要用在切片操作函数里的参数传递

参数说明:

* start -- 起始位置
* stop -- 结束位置
* step -- 间距

```python
>>>myslice = slice(5)    # 设置截取5个元素的切片
>>> myslice
slice(None, 5, None)
>>> arr = range(10)
>>> arr
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> arr[myslice]         # 截取 5 个元素
[0, 1, 2, 3, 4]
>>>
```

### ``sorted(iterable, key=None, reverse=False)``

对所有可迭代的对象进行排序操作

>sort 与 sorted 区别：
><br>sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
><br>list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。

参数说明：

* iterable -- 可迭代对象。
* key -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
* reverse -- 排序规则，reverse = True 降序 ， reverse = False 升序（默认）

```python
>>>sorted([5, 2, 3, 1, 4])
[1, 2, 3, 4, 5]                      # 默认为升序
```

```python
# 你也可以使用 list 的 list.sort() 方法。这个方法会修改原始的 list（返回值为None）。通常这个方法不如sorted()方便-如果你不需要原始的 list，list.sort()方法效率会稍微高一些。
>>>a=[5,2,3,1,4]
>>> a.sort()
>>> a
[1,2,3,4,5]
```

```python
#另一个区别在于list.sort() 方法只为 list 定义。而 sorted() 函数可以接收任何的 iterable。
>>>sorted({1: 'D', 2: 'B', 3: 'B', 4: 'E', 5: 'A'})
[1, 2, 3, 4, 5]
```

```python
# 利用key进行倒序排序
>>>example_list = [5, 0, 6, 1, 2, 7, 3, 4]
>>> result_list = sorted(example_list, key=lambda x: x*-1)
>>> print(result_list)
[7, 6, 5, 4, 3, 2, 1, 0]
>>>
```

```python
#要进行反向排序，也通过传入第三个参数 reverse=True
>>>example_list = [5, 0, 6, 1, 2, 7, 3, 4]
>>> sorted(example_list, reverse=True)
[7, 6, 5, 4, 3, 2, 1, 0]
```

### ``staticmethod(function)``

返回函数的静态方法。

该方法不强制要求传递参数，如下声明一个静态方法：

```python
class C(object):
    @staticmethod
    def f(arg1, arg2, ...):
        ...
```

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class C(object):
    @staticmethod
    def f():
        print('runoob');
 
C.f();          # 静态方法无需实例化
cobj = C()
cobj.f()     
```

### ``class str(object='')``

将对象转化为适于人阅读的形式。

```python
>>>s = 'RUNOOB'
>>> str(s)
'RUNOOB'
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'};
>>> str(dict)
"{'google': 'google.com', 'runoob': 'runoob.com'}"
>>>
```

### ``sum(iterable[, start])``

对系列进行求和计算。

参数说明:

* iterable -- 可迭代对象，如：列表、元组、集合。
* start -- 指定相加的参数，如果没有设置这个值，默认为0。

```python
>>>sum([0,1,2])  
3  
>>> sum((2, 3, 4), 1)        # 元组计算总和后再加 1
10
>>> sum([0,1,2,3,4], 2)      # 列表计算总和后再加 2
12
```

### ``super(type[, object-or-type])``

是用于调用父类(超类)的一个方法。

是用来解决多重继承问题的，直接用类名调用父类方法在使用单继承的时候没问题，但是如果使用多继承，会涉及到查找顺序（MRO）、重复调用（钻石继承）等种种问题。

参数:

* type -- 类。
* object-or-type -- 类，一般是 self

Python3.x 和 Python2.x 的一个区别是: Python 3 可以使用直接使用 super().xxx 代替 super(Class, self).xxx :

```python
# python3 实例
class A:
    pass
class B(A):
    def add(self, x):
        super().add(x)
```

```python
class A(object):   # Python2.x 记得继承 object
    pass
class B(A):
    def add(self, x):
        super(B, self).add(x)
```

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class FooParent(object):
    def __init__(self):
        self.parent = 'I\'m the parent.'
        print ('Parent')
    
    def bar(self,message):
        print ("%s from Parent" % message)
 
class FooChild(FooParent):
    def __init__(self):
        # super(FooChild,self) 首先找到 FooChild 的父类（就是类 FooParent），然后把类B的对象 FooChild 转换为类 FooParent 的对象
        super(FooChild,self).__init__()    
        print ('Child')
        
    def bar(self,message):
        super(FooChild, self).bar(message)
        print ('Child bar fuction')
        print (self.parent)
 
if __name__ == '__main__':
    fooChild = FooChild()
    fooChild.bar('HelloWorld')


...
Parent
Child
HelloWorld from Parent
Child bar fuction
I'm the parent.
```

### ``tuple( seq )``

将列表转换为元组。

```python
>>>list1= ['Google', 'Taobao', 'Runoob', 'Baidu']
>>> tuple1=tuple(list1)
>>> tuple1
('Google', 'Taobao', 'Runoob', 'Baidu')
```

### ``type(object)``

### ``type(name, bases, dict)``

如果你只有第一个参数则返回对象的类型，三个参数返回新的类型对象。

> isinstance() 与 type() 区别：
> * type() 不会认为子类是一种父类类型，不考虑继承关系。
> * isinstance() 会认为子类是一种父类类型，考虑继承关系。
> 
> 如果要判断两个类型是否相同推荐使用 isinstance()。

参数

* name -- 类的名称。
* bases -- 基类的元组。
* dict -- 字典，类内定义的命名空间变量。

```python
# 一个参数实例
>>> type(1)
<type 'int'>
>>> type('runoob')
<type 'str'>
>>> type([2])
<type 'list'>
>>> type({0:'zero'})
<type 'dict'>
>>> x = 1          
>>> type( x ) == int    # 判断类型是否相等
True
 
# 三个参数
>>> class X(object):
...     a = 1
...
>>> X = type('X', (object,), dict(a=1))  # 产生一个新的类型 X
>>> X
<class '__main__.X'>
```

```python
# type() 与 isinstance()区别：
class A:
    pass
 
class B(A):
    pass
 
isinstance(A(), A)    # returns True
type(A()) == A        # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```

### ``vars([object])``

返回对象object的属性和属性值的字典对象。

```python
>>>print(vars())
{'__builtins__': <module '__builtin__' (built-in)>, '__name__': '__main__', '__doc__': None, '__package__': None}
>>> class Runoob:
...     a = 1
... 
>>> print(vars(Runoob))
{'a': 1, '__module__': '__main__', '__doc__': None}
>>> runoob = Runoob()
>>> print(vars(runoob))
{}
```

### ``zip([iterable, ...])``

用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。

我们可以使用 list() 转换来输出列表。

如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

```python
>>>a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)     # 返回一个对象
>>> zipped
<zip object at 0x103abc288>
>>> list(zipped)  # list() 转换为列表
[(1, 4), (2, 5), (3, 6)]
>>> list(zip(a,c))              # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
 
>>> a1, a2 = zip(*zip(a,b))          # 与 zip 相反，zip(*) 可理解为解压，返回二维矩阵式
>>> list(a1)
[1, 2, 3]
>>> list(a2)
[4, 5, 6]
>>>
```
### ``__import__(name[, globals[, locals[, fromlist[, level]]]])``

用于动态加载类和函数 。

```python
#a.py 文件代码：
import os  
print ('在 a.py 文件中 %s' % id(os))
```

```python
#test.py 文件代码：
#!/usr/bin/env python    
#encoding: utf-8  
import sys  
__import__('a')        # 导入 a.py 模块
```

