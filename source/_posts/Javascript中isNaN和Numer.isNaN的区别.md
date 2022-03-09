---
title: Javascript中isNaN和Numer.isNaN的区别
date: 2022-03-09 10:32:01
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220309104530660.png
categories: 前端
tags:
 - 前端
 - Javascript
---

### isNaN()
当我们将参数传入**isNaN**时，它的内部实现调用了**Number()**方法将参数转换为Number类型，如下所示。

``` javascript
Number('')  // 0
Number(null) // 0
Number(true) // 1
Number(false) // 0
Number(undefined) // NaN
Number('aa') // NaN
Number(NaN) // NaN
```
若能成功转换为Number类型，**isNaN**返回false，否则返回true。
``` javascript
isNaN('')  // false
isNaN(null) // false
isNaN(true) // false
isNaN(false) // false
isNaN(undefined) // true
isNaN('aa') // true
isNaN(NaN) // true
```
> Javascript中0 / 0返回的是NaN，而非0数值除以0返回的是Infinity

### Number.isNaN()

**判断传入的参数是否严格等于NaN（也就是===）**

``` javascript
Number.isNaN('')  // false
Number.isNaN(null) // false
Number.isNaN(true) // false
Number.isNaN(false) // false
Number.isNaN(undefined) // false
Number.isNaN('aa') // false
Number.isNaN(NaN) // true
```
使用场景：判断某个运算的结果是否为NaN
``` javascript
function check(value) {
	return Number.isNaN();
}
check(1 / 'test') // false
```