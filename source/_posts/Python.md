---
title: Python
date: 2024-08-09 20:35:00
tags: [Python]
categories: Python
---

## 安装
```plain
brew install python3
```

```plain
guxc@guxuchengdeMacBook-Pro ~ % python
Python 3.11.6 (main, Nov  2 2023, 04:39:40) [Clang 14.0.0 (clang-1400.0.29.202)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> exit()
```

## 基础使用
### hello,world
```plain
vi helllo.py
```

```plain
print('hello, world')
```

```plain
python hello.py
```

或者在mac和linux也支持直接运行

```plain
#!/usr/bin/env python3

print('hello, world')
```

```plain
$ chmod a+x hello.py
$ ./hello.py
hello, world

```

### input/print
```plain
name = input('please enter your name: ')
print('hello',name)
```

```plain
python hello.py
please enter your name: world
hello world
```

### 数据类型和变量
浮点：

很大或者很小的浮点数，必须用科学计数法表示，把10用e替代，1.23x109就是1.23e9，或者12.3e8，0.000012可以写成1.2e-5



变量

python是动态语言

```plain
a = 123 # a是整数
print(a)
a = 'ABC' # a变为字符串
print(a)
```



常量

通常大写变量名，习惯非机制

```plain
PI = 3.14159265359
```



除法

```plain
>>> 10 / 3
3.3333333333333335
```



地板除

```plain
>>> 10 // 3
3
```

### 字符串和编码
Python 3的字符串使用Unicode，直接支持多语言。

```plain
>>> print('包含中文的str')
包含中文的str
```

当str和bytes互相转换时，需要指定编码。

以Unicode表示的str通过encode()方法可以编码为指定的bytes

```plain
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
```

要把bytes变为str，就需要用decode()方法：

```plain
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

要计算str包含多少个字符，可以用len()函数：

```plain
>>> len('ABC')
3
>>> len('中文')
2
```

当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。

通常在文件开头写上这两行

```plain
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```



格式化字符串

```plain
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

| <font style="color:rgb(209, 213, 219);">占位符 | <font style="color:rgb(209, 213, 219);">替换内容 |
| :--- | :--- |
| <font style="color:rgb(209, 213, 219);">%d | <font style="color:rgb(209, 213, 219);">整数 |
| <font style="color:rgb(209, 213, 219);">%f | <font style="color:rgb(209, 213, 219);">浮点数 |
| <font style="color:rgb(209, 213, 219);">%s | <font style="color:rgb(209, 213, 219);">字符串 |
| <font style="color:rgb(209, 213, 219);">%x | <font style="color:rgb(209, 213, 219);">十六进制整数 |


```plain
print('%2d-%02d' % (3, 1))
print('%.2f' % 3.1415926)
```





### list和tuple
list是一种有序的集合，可以随时添加和删除其中的元素。

```plain
classmates = ['Michael', 'Bob', 'Tracy']
//取第一个
>>> classmates[0]
'Michael'

//取倒数第一个
>>> classmates[-1]
'Tracy'

//追加末尾（['Michael', 'Bob', 'Tracy', 'Adam']）
>>> classmates.append('Adam')

//插到索引为1的位置（['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']）
>>> classmates.insert(1, 'Jack')

//删除list末尾的元素（['Michael', 'Jack', 'Bob', 'Tracy']）
>>> classmates.pop()

//删除指定位置（索引为1）的元素（['Michael', 'Bob', 'Tracy']）
>>> classmates.pop(1)

//替换元素（['Michael', 'Sarah', 'Tracy']）
>>> classmates[1] = 'Sarah'

//list里面的元素的数据类型也可以不同
>>> L = ['Apple', 123, True]


//list元素也可以是另一个list
>>> s = ['python', 'java', ['asp', 'php'], 'scheme']
>>> len(s)
4

>>> s[2][1]
'php'


//空的list，len 0
>>> L = []
>>> len(L)
0
```



tuple和list非常类似，但是tuple一旦初始化就不能修改

当你定义一个tuple时，在定义的时候，tuple的元素就必须被确定下来

```plain
>>> t = (1, 2)
>>> t
(1, 2)

//只有1个元素的tuple定义时必须加一个逗号,
>>> t = (1,)
>>> t
(1,)


//tuple里的list可以变
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```



### 条件判断/match
```plain
//只要x是非零数值、非空字符串、非空list等，就判断为True，否则为False。
if x:
    print('True')
```

```plain
score = 'B'

match score:
    case 'A':
        print('score is A.')
    case 'B':
        print('score is B.')
    case 'C':
        print('score is C.')
    case _: # _表示匹配到其他任何情况
        print('score is ???.')



age = 15

match age:
//第一个case x if x < 10表示当age < 10成立时匹配
    case x if x < 10:
        print(f'< 10 years old: {x}')
    case 10:
        print('10 years old.')
//第三个case 11|12|...|18能匹配多个值，用|分隔。
    case 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18:
        print('11~18 years old.')
    case 19:
        print('19 years old.')
    case _:
        print('not sure.')
```

### 循环
```plain
//list输出
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)

//数字相加
sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum = sum + x
print(sum)

//range()函数，可以生成一个整数序列
>>> list(range(5))
[0, 1, 2, 3, 4]


//0加到100
sum = 0
for x in range(101):
    sum = sum + x
print(sum)

//也支持while
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```



### dict和set
dict在其他语言中也称为map

list是可变的，就不能作为key

```plain
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Adam'] = 67

>>> 'Thomas' in d
False

//如果key不存在，可以返回None，或者自己指定的value，返回None的时候Python的交互环境不显示结果。
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1

//要删除一个key
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```



set不存储value，key不能重复

不能放入可变对象，比如list

```plain
>>> s = {1, 2, 3}
>>> s
{1, 2, 3}

//提供一个list作为输入集合
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}


//重复元素在set中自动被过滤
>>> s = {1, 1, 2, 2, 3, 3}
>>> s
{1, 2, 3}

//添加元素
>>> s.add(4)

//删除元素
>>> s.remove(4)

//数学交集
>>> s1 = {1, 2, 3}
>>> s2 = {2, 3, 4}
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}

```



不可变对象

str是不变对象，而list是可变对象。

```plain
>>> a = 'abc'
>>> a.replace('a', 'A')
'Abc'
>>> a
'abc'

//这里replace方法创建了一个新字符串'Abc'并返回
//如果我们用变量b指向该新字符串，就容易理解了，变量a仍指向原有的字符串'abc'，但变量b却指向新字符串'Abc'了：
>>> a = 'abc'
>>> b = a.replace('a', 'A')
>>> b
'Abc'
>>> a
'abc'

```



### 函数
```plain
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x

print(my_abs(-99))
```



如果你已经把my_abs()的函数定义保存为abstest.py文件了

用from abstest import my_abs来导入my_abs()函数，注意abstest是文件名（不含.py扩展名）

```plain
>>> from abstest import my_abs                          │
│>>> my_abs(-9)                                          │
│9          
```



空函数

pass可以用来作为占位符，让代码能运行起来

```plain
def nop():
    pass
```

pass还可以用在其他语句里，比如：

```plain
if age >= 18:
    pass
```



返回多个值

Python的函数返回多值其实就是返回一个tuple

```plain
import math

//angle默认0，可以不传
def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```



默认参数

```plain
def enroll(name, gender, age=6, city='Beijing'):
    print('name:', name)
    print('gender:', gender)
    print('age:', age)
    print('city:', city)
```

```plain
enroll('Bob', 'M', 7)
enroll('Adam', 'M', city='Tianjin')
```

默认参数必须指向不变对象

如果指向可变对象比如list

```plain
def add_end(L=[]):
    L.append('END')
    return L
```

多次执行

```plain
>>> add_end()
['END']
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

可以修改为

```plain
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

```plain
>>> add_end()
['END']
>>> add_end()
['END']
```



可变参数

在参数前面加了一个*号，在函数内部，参数numbers接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数：

```plain
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

```plain
>>> calc(1, 2)
5
>>> calc()
0
```

有一个list或者tuple调用可变参数时

```plain
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

*nums表示把nums这个list的所有元素作为可变参数传进去。这种写法相当有用，而且很常见。



关键字参数

同时支持必选参数和可选参数

kw是一个dict

```plain
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

```plain
>>> person('Michael', 30)
name: Michael age: 30 other: {}

>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}

>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
```

> *args是可变参数，args接收的是一个tuple；
>
> **kw是关键字参数，kw接收的是一个dict。
>



### 迭代
默认dict迭代key

```plain
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
...
a
c
b

```

如果要迭代value

```plain
for value in d.values()
```

如果要同时迭代key，value

```plain
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> for k, v in d.items():
...     print(k, '=', v)
...
y = B
x = A
z = C
```



如果要生成[1x1, 2x2, 3x3, ..., 10x10]

```plain
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

```plain
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

两层循环实现全排列

```plain
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

列出目录下所有文件和目录名

```plain
>>> import os # 导入os模块，模块的概念后面讲到
>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
['.emacs.d', '.ssh', '.Trash', 'Adlm', 'Applications', 'Desktop', 'Documents', 'Downloads', 'Library', 'Movies', 'Music', 'Pictures', 'Public', 'VirtualBox VMs', 'Workspace', 'XCode']
```



### 生成器
在循环的过程中不断推算出后续的元素，不必创建完整的list

```plain
L = [x * x for x in range(10)]
g = (x * x for x in range(10))
```

创建L和g的区别仅在于最外层的[]和()，L是一个list，而g是一个generator。

```plain
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)

```



generator函数

```plain
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator函数，调用一个generator函数将返回一个generator：

```plain
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

普通函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。



### 高阶函数
```plain
def add(x, y, f):
    return f(x) + f(y）

print(add(-5, 6, abs))

```



### 模块
一个abc.py的文件就是一个名字叫abc的模块

一个顶层包名，比如mycompany，按照如下目录存放

```plain
mycompany
├─ __init__.py
├─ abc.py
└─ xyz.py
```

__init__.py的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。__init__.py可以是空文件，也可以有Python代码



可以有多级目录，组成多级层次的包结构

```plain
mycompany
 ├─ web
 │  ├─ __init__.py
 │  ├─ utils.py
 │  └─ www.py
 ├─ __init__.py
 ├─ abc.py
 └─ utils.py
```

文件www.py的模块名就是mycompany.web.www，两个文件utils.py的模块名分别是mycompany.utils和mycompany.web.utils。



使用内建sys模块

```plain
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```

> 第1行和第2行是标准注释，第1行注释可以让这个hello.py文件直接在Unix/Linux/Mac上运行，第2行注释表示.py文件本身使用标准UTF-8编码；
>
> 
>
> 第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；
>
> 
>
> 第6行使用__author__变量把作者写进去，这样当你公开源代码后别人就可以瞻仰你的大名；
>
> 
>
> sys模块有一个argv变量，用list存储了命令行的所有参数
>
> 运行python3 hello.py获得的sys.argv就是['hello.py']；
>
> 运行python3 hello.py Michael获得的sys.argv就是['hello.py', 'Michael']。
>
> 
>
> if __name__=='__main__':
>
>     test()
>
> 这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。
>
> 
>
> 
>



私有/非公开函数变量

通过_前缀来实现的，比如前面的__author__，或者_xxx，样的函数或变量就是非公开的（private），不应该被直接引用

> 之所以我们说，private函数和变量“不应该”被直接引用，而不是“不能”被直接引用，是因为Python并没有一种方法可以完全限制访问private函数或变量，但是，从编程习惯上不应该引用private函数或变量。
>



### 第三方模块
在Python中，安装第三方模块，是通过包管理工具pip完成的。

如果你正在使用Mac或Linux，安装pip本身这个步骤就可以跳过了。

如果你正在使用Windows，请参考安装Python一节的内容，确保安装时勾选了pip和Add python.exe to Path。



一般来说，第三方库都会在Python官方的pypi.python.org网站注册，要安装一个第三方库，必须先知道该库的名称，可以在官网或者pypi上搜索，比如Pillow的名称叫Pillow，因此，安装Pillow的命令就是：

```plain
pip install Pillow
```



推荐直接使用Anaconda，这是一个基于Python的数据处理和科学计算平台，它已经内置了许多非常有用的第三方库，我们装上Anaconda，就相当于把数十个第三方模块自动安装好了，非常简单易用。

可以从Anaconda官网下载GUI安装包，安装包有500~600M，所以需要耐心等待下载。下载后直接安装，Anaconda会把系统Path中的python指向自己自带的Python，并且，Anaconda安装的第三方模块会安装在Anaconda自己的路径下，不影响系统已安装的Python目录。



安装好Anaconda后，重新打开命令行窗口，输入python，可以看到Anaconda的信息：

```plain
│C:\> python                                             │
│Python 3.6.3 |Anaconda, Inc.| ... on win32              │
│Type "help", ... for more information.                  │
│>>> import numpy                                        │
│>>> _
```

可以尝试直接import numpy等已安装的第三方模块。



### from module_name import item_name
这里的 `module_name` 是你想要导入的模块的名字，`item_name` 是你希望从该模块中导入的对象（可以是函数、类、变量等）。

```plain
from math import sqrt
result = sqrt(16)  # 直接使用sqrt函数，无需加math.前缀
print(result)  # 输出 4.0
```

+ 解释：这会从 `math` 模块中导入 `sqrt` 函数，直接在代码中使用 `sqrt()`。



```plain
from math import sqrt, pi
print(sqrt(25))  # 输出 5.0
print(pi)        # 输出 3.141592653589793
```

+ 解释：从 `math` 模块中导入了多个对象，可以同时导入多个函数或变量。



```plain
from math import sqrt as square_root
print(square_root(25))  # 输出 5.0
```

+ 解释：通过 `as` 关键字，给导入的函数或类起一个别名，方便在当前脚本中使用。





### 错误
python内置了一套try...except...finally...的错误处理机制

```plain
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e)
finally:
    print('finally...')
print('END')
```

执行完except后，如果有finally语句块，则执行finally语句块（可以没有finally语句）

计算10 / 0

```plain
try...
except: division by zero
finally...
END
```

计算10/2

```plain
try...
result: 5
finally...
END
```



记录错误，日志收集

```plain
import logging

def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)

main()
print('END')
```

程序打印完错误信息后会继续执行



抛出错误/定义错误

定义一个错误的class，选择好继承关系，然后，用raise语句抛出一个错误的实例：

```plain
class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise FooError('invalid value: %s' % s)
    return 10 / n

foo('0')
```

如果可以选择Python已有的内置的错误类型（比如ValueError，TypeError），尽量使用Python内置的错误类型。



### 调试
方法一：print()把可能有问题的变量打印出来看看

```plain
def foo(s):
    n = int(s)
    print('>>> n = %d' % n)
    return 10 / n

def main():
    foo('0')

main()
```

方法二：断言，print()的地方用assert替代，如果断言失败，assert语句本身就会抛出AssertionError

```plain
def foo(s):
    n = int(s)
    assert n != 0, 'n is zero!'
    return 10 / n

def main():
    foo('0')
```

启动Python解释器时可以用-O参数来关闭assert

```plain
$ python -O err.py
Traceback (most recent call last):
  ...
ZeroDivisionError: division by zero
```

方法三：logging

```plain
import logging
logging.basicConfig(level=logging.INFO)
s = '0'
n = int(s)
logging.info('n = %d' % n)
print(10 / n)
```

```plain
$ python err.py
INFO:root:n = 0
Traceback (most recent call last):
  File "err.py", line 8, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```



### 单元测试
```plain
class Dict(dict):
    def __init__(self, **kw):
        super().__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value
```



```plain
import unittest

from mydict import Dict

class TestDict(unittest.TestCase):
    def test_init(self):
        d = Dict(a=1, b='test')
        self.assertEqual(d.a, 1)
        self.assertEqual(d.b, 'test')
        self.assertTrue(isinstance(d, dict))

    def test_key(self):
        d = Dict()
        d['key'] = 'value'
        self.assertEqual(d.key, 'value')

    def test_attr(self):
        d = Dict()
        d.key = 'value'
        self.assertTrue('key' in d)
        self.assertEqual(d['key'], 'value')

    def test_keyerror(self):
        d = Dict()
        with self.assertRaises(KeyError):
            value = d['empty']

    def test_attrerror(self):
        d = Dict()
        with self.assertRaises(AttributeError):
            value = d.empty
            

```

```plain
$ python -m unittest mydict_test

```



### setUp与tearDown
setUp()和tearDown()方法，这两个方法会分别在每调用一个测试方法的前后分别被执行。

setUp()和tearDown()方法有什么用呢？设想你的测试需要启动一个数据库，这时，就可以在setUp()方法中连接数据库，在tearDown()方法中关闭数据库，这样，不必在每个测试方法中重复相同的代码：

### 面向对象
```plain
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))
```



如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，在Python中，实例的变量名如果以__开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问

```plain
class Student(object):
    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))
```

```plain
>>> bart = Student('Bart Simpson', 59)
>>> bart.__name
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute '__name'
```



### 继承
```plain
class Animal(object):
    def run(self):
        print('Animal is running...')

class Dog(Animal):
    pass

class Cat(Animal):
    pass

dog = Dog()
dog.run()

cat = Cat()
cat.run()
```



### __slots__
限制实例的属性怎么办？比如，只允许对Student实例添加name和age属性。

```plain
class Student(object):
    __slots__ = ('name', 'age') 
```

```plain
>>> s = Student() # 创建新的实例
>>> s.name = 'Michael' # 绑定属性'name'
>>> s.age = 25 # 绑定属性'age'
>>> s.score = 99 # 绑定属性'score'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'score'
```

__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用



### @property
用于将类的方法变成属性

```plain
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @property
    def area(self):
        return 3.14 * (self._radius ** 2)

# 创建 Circle 对象
c = Circle(5)

# 通过属性访问 radius 和 area
print(c.radius)  # 5
print(c.area)    # 78.5

```



### if __name__ == '__main__':
用于判断当前模块是否作为主程序执行。它的作用是让某些代码仅在直接运行 Python 脚本时执行，而在该脚本作为模块导入到其他脚本时不会执行。

每个 Python 模块都有一个内置的特殊变量 `__name__`。它表示当前模块的名字。

+ 如果 Python 文件被直接运行，`__name__` 的值将是 `'__main__'`。
+ 如果 Python 文件被导入为模块，则 `__name__` 的值将是模块的名称（即脚本文件的名称，不包括扩展名 `.py`

`**if __name__ == '__main__':**`** 是什么？**

**这个条件语句用于检查当前脚本是否是作为主程序运行。如果是，则执行 **`**if**`** 块中的代码；如果脚本是作为模块被导入到其他程序中，则不会执行 **`**if**`** 块中的代码。**

****

****

## 参考
[https://liaoxuefeng.com/books/python/introduction/index.html](https://liaoxuefeng.com/books/python/introduction/index.html)

