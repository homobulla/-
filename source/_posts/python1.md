---
title: python基础一
date: 2018-09-04 10:08:45
tags: [python]
categories: python

---

>重视语言特性，而不是语言 --王银
- 变量定义
- 算术运算
- for 循环语句，while 循环语句
- 函数定义，函数调用
- 递归
- 静态类型系统
- 类型推导
- lambda 函数
- 面向对象
- 垃圾回收
- 指针算术
- goto 语句

### number and string

#### number 计算 大致类似于 js

#### 一些不同之处

有 floast 和 int 类型
除了 int 和 float，Python 还支持其它数字类型，例如 Decimal 和 Fraction。Python 还内建支持 复数 ，使用后缀 j 或 J 表示虚数部分（例如，3+5j）。

```py
8 / 8
# 1.0  / 永远返回一个float
8 // 5 # 1
8 // 5 # 1.0 // 永远返回小数点之前的整数，至于是float 还是 int ，取决于除数和被除数的类型
8 % 5 # 3 % 求摸操作符 ,返回类型同上
# 整数和浮点数的混合计算中，整数会被转换为浮点数
```

#### string

单双引号，字符转义`\`,如果你前面带有 \ 的字符被当作特殊字符，你可以使用 原始字符串，方法是在第一个引号前面加上一个 r:

```py
>>> print('C:\some\name')  # here \n means newline!
C:\some
ame
>>> print(r'C:\some\name')  # note the r before the quote
C:\some\name
```

python 提供了类似 html 的`<pre>`标签的功能，可以保留换行符和空格，一是使用三引号： `"""..."""` 或者 `'''...'''`,其中可以用 \ 来去除换行。

```py
>>> print ('''\
demo
	this is line one        happy
	this is line two        sad''')
demo
	this is line one        happy
	this is line two        sad
>>> print ('''
demo
	this is line one        happy
	this is line two        sad''')

demo
	this is line one        happy
	this is line two        sad
>>>
```

用`+`来链接字符和表达式，用`*`来重复字符串,字符同样可以被检索，（支持正负）以及切片，

```py
>>> homobulla[:1] + homobulla[1:]
'homobulla'
>>> homobulla[:4] + homobulla[4:]
'homobulla'
```

<b>字符串不可变</b>(js 中 string 同样不可变，但编译器不报错),内置函数 len() 返回字符串长度:

```py
>>> word[0] = 'J'
  ...
TypeError: 'str' object does not support item assignment
>>> word[2:] = 'py'
  ...
TypeError: 'str' object does not support item assignment
>>> s = 'supercalifragilisticexpialidocious'
>>> len(s)
34
```

#### list

复合数据类型之一：列表，基本就是 js 的数组，切片，赋值，浅拷贝，嵌套等等。

`range(int1,int2,int3)`函数生成一个可迭代列表，`list()`也是一个迭代器

```py
>>> list(range(0,101,10))

[0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```
### python 中的控制流语句

#### for 循环

当在循环中进行列表的修改时，可以迭代其副本，即使用切割标识符：

```py
>>> for w in words[:]:  # Loop over a slice copy of the entire list.
...     if len(w) > 6:
...         words.insert(0, w)
...
>>> words
['defenestrate', 'cat', 'window', 'defenestrate']
```
#### if 语句

if...elif...elif...else(非必需)

#### break 和 continue 语句, 以及循环中的 else 子句

`break`跳出当前循环，循环中可以套`else`语句用来循环那些条件为`flase`的情况，当有`break`的时候就不会执行了。

```py
>>> for n in range(2, 10):
...     for x in range(2, n):
...         if n % x == 0:
...             print(n, 'equals', x, '*', n//x)
...             break
...     else:
            # 这里的else不是在上面的if里，而是在外层的for循环中，
...         # loop fell through without finding a factor
...         print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```

#### pass 语句

`pass`语句类似于占位语句，即语法上需要有语句但真正程序什么也不做。


### 函数

> 无论什么语言，函数都是重中之重. ——homobulla

#### 函数的定义

关键字`def`,在其后必须跟有函数名和包括形式参数的圆括号。函数体语句从下一行开始，必须是缩进的。
函数体的第一行语句可以是可选的字符串文本，这个字符串是函数的文档字符串，或者称为 docstring。

```py
def fib(n):
    """Print a Fibonacci series up to n."""
    a,b = 0,1
    while(a<n):
        print(a,end=' ' )
        a,b = b,a+b
    print()
```

函数内变量、参数、作用域的问题：
<b>局部符号表:</b>js 当中局部变量吧。
函数调用会为函数局部变量生成一个新的符号表。变量引用首先在局部符号表中查找，然后是包含函数的局部符号表，再然后是全局符号表，最后是内置符号表，和 js 的原型链类似。
<b>全局变量不能在函数中直接赋值（除非用 global 语句命名），尽管他们可以被引用。</b>
参数的问题：函数的实参在函数调用时引入局部符号表。所以实参是<b>传值调用</b>(此处的值是一个对象引用，而不是该对象的值)
`return`语句从函数中返回一个值，不带表达式的 return 返回 None。
过程结束后也会返回 None。
参数不能多次赋值，尤其是当参数为一个对象时，赋值操作会被累加。
函数的调用更加的多样和弱检测，
