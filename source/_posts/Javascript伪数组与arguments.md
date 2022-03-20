---
title: Javascript伪数组与arguments
date: 2022-03-19 21:59:01
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220319223452332.png
categories: 前端
tags:
 - Javascript
---

## 什么是伪数组？

所谓伪数组，也称为类数组对象，指的是`可以通过索引属性访问元素`并且`拥有 length 属性`的`对象`。

以下为一个简单的伪数组的例子：

``` javascript
const arrFake = {
  0: 'name',
  1: 'age',
  2: 'job',
  length: 3
}
```

它所对应的数组如下所示：

``` javascript
const arr = ['name', 'age', 'job'];
```

它们的**相同点**：

``` javascript
console.log(arr[0]) // name
console.log(arrFake[0]) // name

arr[0] = 'new name'
arrFake[0] = 'new name'

console.log(arr.length); // 3
console.log(arrFake.length); // 3
```

它们的**不同点**在于，伪数组不能调用数组原型链上的方法，且伪数组调用`Array.isArray`方法输出`false`：

``` javascript
arrFake.push('hobby')  // Uncaught TypeError: arrFake.push is not a function
arr.push('hobby'); // ['name', 'age', 'job', 'hobby']

Array.isArray(arrFake) // false
Array.isArray(arr) // true
```

我们可以用**Array.from**将伪数组转化为真正的数组

``` javascript
const newArr = Array.from(arrFake) // ['name', 'age', 'job']
newArr.push('hobby') // ['name', 'age', 'job', 'hobby']
```
> 也可以使用
> 1.Array.prototype.slice等返回新数组的方法
> 2.扩展运算符[...arguments]

js中的伪数组对象：

- `document.getElementsByTagName`获取的元素标签集合
- 函数中的`arguments`

## arguments

定义如下函数：

``` javascript
function a(x, y) {
    console.log(arguments)
}

a(1, 2, 3, 4)
```
> 注意！箭头函数没有 arguments

在控制台显示打印结果：
![image-20220319225005184](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220319225005184.png)

显然，`arguments`就是一个伪数组，`callee`属性如上图所示，为函数本身，我们可以使用`arguments.callee()`的方式调用函数。

再思考一下以下代码：

``` javascript
function a(x, y) {
    console.log(a.length) // 2
    console.log(arguments.length) // 4
}

a(1, 2, 3, 4)
```

其中`function`本身的`length`代表行参的长度，而`arguments`的`length`代表实参的长度。
> 注意！function本身的length不包括有默认值的形参和剩余参数...args



## arguments的应用

实现函数的重载
```
function doAdd() {
    if (arguments.length == 1) {
        console.log(arguments[0] + 5);
    } else if (arguments.length == 2) {
        console.log(arguments[0] + arguments[1]);
    }
}

doAdd(1) // 6
doAdd(1, 2) // 3
```