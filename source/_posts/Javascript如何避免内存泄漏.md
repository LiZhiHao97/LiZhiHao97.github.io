---
title: Javascript如何避免内存泄漏
date: 2022-03-10 20:49:51
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310155116766.png
categories: 前端
tags: 
 - 前端
 - Javascript
 - 内存泄漏
---

## 一、如何监控内存状况

这里借用一下[林三心](https://juejin.cn/user/1292681407377624)大佬的图文。

### 浏览器任务管理器

打开方式：在浏览器顶部右键，打开任务管理器：

![image-20220310164329157](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310164329157.png)

![image-20220310164734409](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310164734409.png)

打开后，可以看到内存和JavaScript内存：

- 内存：页面里的原始内存，也就是**DOM节点**的总占用内存
- JavaScript内存(括号里)：是该页面中所有**可达对象**的总占用内存

那什么是**可达对象**呢？简单说就是：就是从初始的根对象（window或者global）的指针开始，向下搜索子节点，子节点被搜索到了，说明该子节点的引用对象可达，搜不到，说明该子节点对象不可达。举个例子：

``` javascript
// 可达，可以通过window.name访问
var name = 'Zh'

function fn () {
    // 不可达，访问不了
    var name = 'Zh'
}
```
回到我们的任务管理，此时我们在页面中编写一段代码：
``` javascript
<button id="btn">点击</button>
<script>
    document.getElementById('btn').onclick = function () {
        list = new Array(1000000)
    }
</script>
```
点击前：
![image-20220310165143724](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310165143724.png)
点击后，发现内存瞬间上升：
![image-20220310165024376](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310165024376.png)

### Performance

使用Chrome浏览器的**无痕模式**，是为了避免很多其他因素，影响咱们查看内存：

![image-20220310165336577](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310165336577.png)

按F12打开调试窗口，选择**Performance**

![image-20220310165416413](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310165416413.png)

咱们就以掘金首页为例吧！**点击录制 -> 刷新掘金 -> 点击stop**，可以看到以下指标随着时间的`上下波动`：

- `JS Heap`：JS堆

- `Documents`: 文档

- `Nodes`: DOM节点

- `Listeners`: 监听器

- `GPU Memory`: GPU内存

  ![juejinperf.gif](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/1c8bc35ae2c64dc692fe94a925c0ed5a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 堆快照
**堆快照**，顾名思义，就是将当前某一个页面的堆内存拍下照片存起来，同一个页面，执行某个操作前，录制堆快照是一个样，有可能执行完后，录制的堆快照又是另外一个样。

![image-20220310165725251](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220310165725251.png)

还是以**掘金首页**为例，可以看到当前页面内存为**13.3M**，咱们可以选择**Statistics**，查看**数组，对象，字符串**等所占内存。

![掘金堆快照.gif](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/f3159441afe843c4b36993f9db54d19b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 二、内存泄漏的场景

下面列举了可能会造成**内存泄漏**的情况：
1. 闭包使用不当引起内存泄漏
2. 全局变量
3. 分离的DOM节
4. 控制台的打印
5. 未清除的定时器
接下来我们一一来介绍这些情况。

### 1.闭包使用不当

``` javascript
function fn1() {
    let arr = new Array(100000)

    return arr
}
let a = []
document.getElementById('btn').onclick = function () {
    a.push(fn1())
}
```
上述代码中**f1**被调用后，从**可达性**的角度来说，**arr**应该被回收，但实际上并不是这样的。**f1**将**arr**return之后，**arr**被**push**进了数组**a**，而数组**a**是一个全局变量，并不会被回收，这就导致了**arr**不会被回收。

### 全局变量

全局变量一般不会被垃圾回收机制回收。当然，这并不意味着完全禁止我们定义全局变量，只是有时候会因为疏忽而导致某些变量流失到全局，例如未声明变量，却直接对某变量进行赋值，就会导致该变量在全局创建，如下所示：

``` javascript
function fn1() {
    // 此处变量arr未被声明
    arr = new Array(100000)
}
fn1()
```
上述代码会自动在全局创建一个变量**arr**，并将数组赋值给**arr**，由于是全局变量，所以**arr**的内存一直不会释放。

因此，我们平时需多加注意，不要在变量未声明前赋值，或者也可以开启严格模式，这样就会在不知情犯错时，收到报错警告，例如：

``` javascript
function fn1() {
    'use strict';
    name = new Array(100000)
}

fn1()
```

### 3.分离的dom节点
让我们用代码来解释一下什么为**分离的dom节点**:
``` javascript
<button id="btn">点击</button>

let btn = document.getElementById('btn')
document.body.removeChild(btn)
```

如上所示，虽然最后把**button**给删除了，但是因为全局变量**btn**对此**DOM对象**引用着，导致此**DOM对象**一直没有被回收，这个**DOM对象**就称为**分离DOM**

这个问题很好解决，删除button后，顺便把btn设置成null就行了：
``` javascript
<button id="btn">点击</button>

let btn = document.getElementById('btn')
document.body.removeChild(btn)
btn = null
```

### 4.控制台的打印
控制台的打印也会造成内存泄漏吗？？？是的呀，如果浏览器不一直保存着我们打印对象的信息，我们为何能在每次打开控制的Console时看到具体的数据呢？先来看一段测试代码：

``` javascript
<button>按钮</button>
<script>
	document.querySelector('button').addEventListener('click',function({
		let arr = new Array(100000)
        console.log(obj);
    })
</script>
```
当我们点击按钮时，这个**arr**会被控制台打印下来，浏览器一直保存着这个**arr**的信息，并不会被垃圾回收机制回收。

虽然**console.log**便于调试，但是我们在**生产环境**，我们尽可能不要在控制台打印数据，所以我们经常会在代码中看到类似如下的操作：
``` javascript
// 如果在开发环境下，打印变量obj
if(isDev) {
    console.log(obj)
}
```
这样就避免了生产环境下无用的变量打印占用一定的内存空间，同样的除了**console.log**之外，**console.error**、**console.info**、**console.dir**等等都不要在生产环境下使用

### 5.未清除的定时器

下面这段代码中，执行完**fn1**函数，按理说**arr**数组会被回收，但是他却回收不了。为什么呢？因为定时器里的**a**引用着**arr**，并且定时器不清除的话，**a**就不会被回收，**a**不回收就会一直引用着**arr**，那么**arr**肯定也回收不了了。

``` javascript
function fn() {
      let arr = new Array(1000000).fill('Sunshine_Lin')
      setInterval(() => {
          let a = arr
     }, 1000)
}
document.getElementById('btn').onclick = function () {
    fn()
}
```
处理的方法也很简单，只要我们清除定时器就行了：
``` javascript
function fn() {
  let arr = new Array(1000000).fill('Sunshine_Lin')
  let i = 0
  let timer = setInterval(() => {
    if (i > 5)  clearInterval(timer)
    let a = arr
    i++
  }, 1000)
}
document.getElementById('btn').onclick = function () {
  fn()
}
```

## 三、总结
在项目过程中，如果遇到了某些性能问题可能跟内存泄漏有关时，就可以参照以上列举的5种情况去排查。

虽然JavaScript的垃圾回收是自动的，但我们有时也是需要考虑要不要手动清除某些变量的内存占用的，例如你明确某个变量在一定条件下再也不需要，但是还会被外部变量引用导致内存无法得到释放时，你可以用null对该变量重新赋值就可以在后续垃圾回收阶段释放该变量的内存了。

## 参考资料

[赠你13张图，助你20分钟打败了「V8垃圾回收机制」！！！](https://juejin.cn/post/6995706341041897486)
[哪是大神？只是用他人七夕约会时间，整理「JS避免内存泄漏」罢了](https://juejin.cn/post/6996828267068014600)
[一文带你了解如何排查内存泄漏导致的页面卡顿现象](https://juejin.cn/post/6947841638118998029)