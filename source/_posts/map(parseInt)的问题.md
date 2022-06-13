---
title: arr.map(parseInt)的问题
date: 2022-04-08 21:41:04
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220409093608.png
categories: 前端
tags: 
 - Javascript
---

这个问题还挺坑的，我在写算法时，还真这么写过，当时不知道有问题，可能有些题AC不了就是这个导致的

首先我们来看看`parseInt`这个方法

- string(参数1)：必需。要被解析的字符串。
- raidx(参数2)：可选，默认为10。表示要解析的数字的基数。该值介于 2 ~ 36 之间。

从定义中我们可以看出，`parseInt`会将`string`按照`raidx进制`解析，我们来看几个例子

```javascript
parseInt('11', 1) // NaN 因为超出了2-36的范围
parseInt('asd', 1) // NaN 因为不是数字
parseInt('3', 2) // NaN 因为2进制没有3
parseInt('11', 2) // 3
parseInt('11', 10) // 10
```

需要注意的是，如果`radix`不在2-36范围内，输出结果为`NaN`，但也有特例：

```javascript
parseInt('11', 0) // 当radix为0时，按10进制处理
```

了解了以上内容，我们有请今天的主角

``` javascript
[1,2,3].map(parseInt)
```

我们知道，当map自定义回调函数时，写法如下

``` javascript
[1,2,3].map(function(value, index) {
    // ...
})
```

所以前面的代码等价于

``` javascript
[1,2,3].map(function(value, index) {
    return parseInt(value, index)
})
```

返回的结果为`[1, NaN, NaN]`

至于原因，显而易见，知道你平时这样用为什么这么多`NaN`了吧