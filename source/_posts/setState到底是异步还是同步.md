---
title: this.setState到底是异步还是同步？
date: 2022-04-09 15:55:04
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220409143542.png
categories: 前端
tags: 
 - Javascript
 - React
---

## 前言

先说结论，`this.state`是**同步**的，它是一个**伪装的异步**，不属于`宏任务`和`微任务`，并且会在微任务执行前执行。

好的，我们先忘记结论，看个例子

``` javascript
componentDidMount() {
    // this.state.val 初始为0
    
    this.setState({ val: this.state.val + 1})
    console.log(this.state.val) // 0

    this.setState({ val: this.state.val + 1})
    console.log(this.state.val) // 0

    setTimeout(() => {
        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 2

        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 3
    }, 0)
}
```

这里有两个令人疑惑的点，为什么前面两次调用`setState`后打印出的结果还是初始值，而且明明调用了**4**次，最后结果是**3**呢？

接下来我们来看看`setState`的特性

## 同步更新or异步更新

>前面我们说了，setState的异步是伪装的，但为了方便起见，我们以下内容中直接称其为异步

**state默认是异步更新的**，所以下面代码中，会打印出两个0，同步代码无法立即获取到新更新的值。

 

解决这个问题的方法也很简单，因为`setState`有第二个参数，是一个回调函数，我们可以在回调函数中打印更新后的state的值

``` javascript
componentDidMount() {
    this.setState(
        { val: this.state.val + 1 },
        () => { console.log(this.state.val) } // 1
    )
}
```

那么什么情况会同步执行呢?

**当setState不在React上下文中触发时，会同步更新**

- setTimeout, setInterval, promise.then
- 自定义的DOM事件
- Ajax回调
> 注：在最新的React 18中，上述场景能够异步更新,但需将ReactDOM.render替换为ReactDOM.createRoot

所以下面的例子可以成功打印出更新后的结果**1**和**2**

``` javascript
componentDidMount() {
    // this.state.val 初始为0
    setTimeout(() => {
        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 1

        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 2
    }, 0)
}

```

再看一个自定义事件的例子

``` javascript
componentDidMount() {
    // this.state.val 初始为0
    document.getElementById('item').addEventListener('click', () => {
        this.setState({ val: this.state.val + 1 })
        console.log(this.state.val) // 1
    })
}
```

## 合并or不合并

**state默认是合并**

``` javascript
componentDidMount() {
    this.setState( { val: this.state.val + 1 } )

    this.setState(
        { val: this.state.val + 2 },
        () => { console.log(this.state.val) } // 2
    )
}
```

以上结果不应该是**3**吗，为什么会是**2**？因为默认会对`setState`进行合并，避免重复渲染，并以最新一次为准，所以不是**3**，也不会是**1**。

``` javascript
componentDidMount() {
    // this.state.val 初始为0
    setTimeout(() => {
        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 1

        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 2
    }, 0)
}
```

那上面这个例子为什么没合并呢，答案很容易猜到，都已经同步执行了，在执行下一个`setState`之前，已经更新了，当然没办法合并啦。

以下情况也不会合并

``` javascript
componentDidMount() {
    this.setState(
        (prevState, props) => {
            return { val: prevState.val + 1 },
        },
        () => { console.log(this.state.val) } // 1
    )

    this.setState(
        (prevState, props) => {
            return { val: prevState.val + 1 },
        },
        () => { console.log(this.state.val) } // 2
    )
}
```

因为函数不执行，我们无法确定结果，不能像对象那样使用`Object.assign`来合并。

再回到最初那个例子

``` javascript
componentDidMount() {
    // this.state.val 初始为0
    
    this.setState({ val: this.state.val + 1})
    console.log(this.state.val) // 0

    this.setState({ val: this.state.val + 1})
    console.log(this.state.val) // 0

    setTimeout(() => {
        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 2

        this.setState({ val: this.state.val + 1})
        console.log(this.state.val) // 3
    }, 0)
}
```

现在这个问题对我们来说已经非常简单了，至于为什么，也就不多说了。

## 为什么是伪异步？

我们再来看一个例子

``` javascript
componentDidMount() {
    console.log('start')

    Promise.resolve().then(() => console.log('promise))
    
    this.setState(
        { val: this.state.val + 1 },
        () => { console.log('setState') }
    )

    console.log('end')
}
```

上述打印结果为`start`、`end`、`setState`、`promise`

前两个结果大家肯定没有异议，因为promise.then是`微任务`，但是promise.then不是在setState前面吗，为什么反而是`setState`先打印呢？

## 结论

- setState本质上是同步，只不过让React做成了异步的样子,它既不是微任务也不是宏任务，在微任务开始之前，state就已经计算完了
- 因为要考虑性能问题，多次state修改，只进行一次DOM渲染