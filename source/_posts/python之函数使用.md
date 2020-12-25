---
title: python之函数使用
date: 2018-10-11 15:25:23
index_img: https://i.loli.net/2020/12/25/yshc4DfBtJiId5N.png
categories:
 - Python
tags:
  - Python
---

## 一、Python自定义函数的简单使用

与其他许多语言相似，函数是组织好的，可重复使用的，用来实现单一，或相关联功能的代码段。

python中函数定义的规则，基本由以下几个步骤组成：

* 函数代码块以 def 关键词开头，后接函数标识符名称和圆括号()
* 任何传入参数和自变量必须放在圆括号中间。圆括号之间可以用于定义参数
* 函数的第一行语句可以选择性地使用文档字符串（用于存放函数说明）
* return [表达式] 结束函数，选择性地返回一个值给调用方。不带表达式的 return 相当于返回 None。

语法示例：

``` python
def fn( parameters ):
   '''函数_文档字符串'''
   function_suite
   return [expression]
```

实例：

``` python
def sum(num1,num2):
    "两数之和"
    return num1+num2

# 调用函数
print(sum(1,2))
```

输出结果：

``` python
3
```

## 二、函数的传值与传地址

函数的传值与传地址从字面意思来看很好理解，传值就是传入一个参数的值，传址就是传入一个参数的地址，也就是内存的地址（相当于指针）。他们的区别是如果函数里面对传入的参数重新赋值，函数外的全局变量是否相应改变，用传值传入的参数是不会改变的，用传址传入就会发生改变。

我们来看一下下面的例子：

``` python
def afferentValue(num):
    num = 2
def afferentAddress(list):
    list.append(2)

num = 1
list = [1]

# 执行函数
afferentValue(num)
afferentAddress(list)

print(num)
print(list)
```

输出结果：

``` python
1
[1, 2]
```

从以上的实例看出，当将变量传入函数时，并在函数内部修改后，在函数作用域外打印传入函数的变量，Number类型的变量并没有修改成功，List类型的变量修改成功，这就是，传值与传地址的区别

> python的传值和传址是根据传入参数的类型来选择的，传值的参数类型：数字，字符串，元组，传址的参数类型：列表，字典

## 三、函数的返回值

与许多语言不同的是，Python的函数返回值可以有多个，当返回值为多个时，返回的结果为1个元组

示例：

``` python
def returnTest(num1, num2):
    return num1, num2

print(returnTest(1, 2))
```

输出结果：

``` python
(1, 2)
```

## 四、函数的参数

### 1、位置参数

大部分情况下我们使用的都是位置参数，例子如下：

``` python
def userInfo(name, age, sex):
    print('姓名:%s, 年龄: %d, 性别: sex: %s' % (name, age, sex))

userInfo('李XX', 18, '男')
```
输出结果：

``` python
姓名:李XX, 年龄: 18, 性别: sex: 男
```

我们为什么要使用位置参数，原因显而易见，有时我们在调用函数时，必须按照顺序输入参数，否则输出的信息与预料中会有出入。

### 2、关键字参数

前面说了，在大多数情况下我们用的是位置参数，但是如果参数很多的时候，我们在调用时可能很难记住参数的顺序，我们引入关键字参数即使用参数名提供的参数，在于明确每个参数的作用，使得函数调用的时候，参数的含义变得更加清晰。

实例：

``` python
def userInfo(name, age, sex):
    print('姓名:%s, 年龄: %d, 性别: sex: %s' % (name, age, sex))

userInfo(name='李XX', sex='男', age=18)
```

输出结果：

``` python
姓名:李XX, 年龄: 18, 性别: sex: 男
```

可以看出，我们使用关键字参数后，传参的顺序打乱，但是输出的结果与使用位置参数时一样，这就是关键字参数的奇妙之处

### 3、默认值参数

有时候，我们自定义的函数中，如果调用的时候没有设置参数，需要给个默认值，这时候就需要用到默认值参数了。

示例:

``` python
def userInfo(name, age, sex='男'):
    print('姓名:%s, 年龄: %d, 性别: sex: %s' % (name, age, sex))

userInfo('李XX', 18)
```

输出结果：

``` python
姓名:李XX, 年龄: 18, 性别: sex: 男
```

从输出结果可以看出，当你设置了默认参数的时候，在调用函数的时候，不传该参数，就会使用默认值。但是有一点需要注意的是：**只有在形参表末尾的那些参数可以有默认参数值**，也就是说你不能在声明函数形参的时候，先声明有默认值的形参而后声明没有默认值的形参。这是因为赋给形参的值是根据实参传递的顺序而赋值的。例如，def func(a, b=1) 是有效的，但是 def func(a=1, b) 是 无效 的。

### 4、可变参数

可变参数有两种，第一种是*args，第二种是*kwargs。

先来看第一种，用老师布置的作业举例：

``` python
def multi(*numbers):
    result = 1
    for num in numbers:
        result = num * result
    return result

print(multi(1, 2, 3, 4))
```

输出结果：

``` python
24
```

在上面定义的函数中，***numbers**是一个可变参数，而且它的本质其实就是一个元组，使用了可变参数后，我们可以在函数调用时传入若干个参数。

``` python
def userInfo(name, age, sex, **hobby):
    print('姓名:%s, 年龄: %d, 性别: sex: %s' % (name, age, sex))
    print('爱好：{}'.format(hobby))
    
userInfo('李XX', 18, '男', hobby1 = '编程', hobby2 = '编程', hobby3 = '编程')
```

输出结果：

``` python
姓名:李XX, 年龄: 18, 性别: sex: 男
爱好：{'hobby1': '编程', 'hobby2': '编程', 'hobby3': '编程'}
```

可以看出，这种可变参数，在调用函数时传入实参需用关键字参数，因为它的本质是一个字典。

## 匿名函数

lambda表达式，通常是在需要一个函数，但是又不想费神去命名一个函数的场合下使用，被称为匿名函数.lambda所表示的匿名函数内容应该比较简单，如果函数内容复杂的话，应该重新定义一个函数。

实例：

``` python
add = lambda x, y : x+y

print(add(1, 2))
```

输出结果：

``` python
3
```

从实例看，我们可以把冒号左边的表达式，冒号右边的表达式理解为函数返回值。

Python提供了很多函数式编程的特性，如：map、reduce、filter、sorted等这些函数都支持函数作为参数。lambda函数就可以应用在函数式编程中。

>函数式编程中的函数这个术语不是指计算机中的函数（实际上是Subroutine），而是指**数学中的函数**，即**自变量的映射**。也就是说一个**函数的值仅决定于函数参数的值，不依赖其他状态。**比如sqrt(x)函数计算x的平方根，只要x不变，不论什么时候调用，调用几次，值都是不变的。

比如将列表中的元素按照绝对值大小进行升序排列：

``` python
list = [3, 5, -4, -1, 0, -2, -6]

list = sorted(list, key=lambda x: abs(x))

print(list)
```

输出结果：

``` python
[0, -1, -2, 3, -4, 5, -6]
```
## 拓展

上面我们提到了map、reduce、filter、sorted这些函数，那它们到底是用来干什么的，让我们来介绍一下

### 1、map

map函数接受两个参数，第一个参数是函数的名称，第二个参数一个可迭代对象。map函数就是将具体数值根据算法进行计算，，并将结果保存为一个迭代器。

实例：

``` python
def add(x):
    x += 1
    return x

list = [1, 2, 3]

newList = map(add, list)

for i in newList:
    print(i)
```

输出结果：

``` python
2
3
4
```

### 2、reduce

　reduce同map函数一样，也是接受两个参数，但不同的是，reduce函数将当前数值的计算结果与下一个数值的计算结果进行累积计算。reduce()函数第一次运行时会将可迭代对象的第一项作为第一个参数，第二项作为第二个参数传入函数。第二次运行则会将函数第一次运行所返回的结果作为第一个参数，可迭代对象的第三项作为第二个参数传入函数……
　
实例：

``` python
from functools import reduce

def accumulate(x, y):
    return x + y

list = [1, 2, 3, 4]

result = reduce(accumulate, list)

print(result)
```

输出结果：

``` python
10
```


### 3、filter

同上两个函数一样，接受两个参数，第一个参数为函数名，第二个参数为序列。但filter根据序列中各个元素作用与函数时，函数返回结果(True/False)来决定该元素是否保留。

实例：

``` python
def fetchEven(num):
    if num % 2 == 0:
        return True
    else:
        return False

list = [1, 2, 3, 4, 5]

newList = filter(fetchEven, list)

for i in newList:
    print(i)
```

输出结果：

``` python
2
4
```

### 4、sorted

sorted顾名思义，就是用来排序的，sorted可以接受一个key函数(可选参数)，实现自定义排序。

实例：

``` python
list = [3, 5, -4, -1, 0, -2, -6]

newList = sorted(list, key=abs)

print(newList)
```

输出结果：

``` python
[0, -1, -2, 3, -4, 5, -6]
```

以上几个函数的优点是，在执行过程中没有副作用。例如当我们使用map处理list时，并不会改变传入的list，而时返回了一个新的迭代器对象。