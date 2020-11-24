---
title: react生命周期详解
date: 2017-06-27 20:14:02
tags:
  - 前端
  - react 
  - 生命周期
  - 框架
  - javascript
---

### 组件的生命周期分成三个状态
- Mounting：已插入真实 DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实 DOM


## Mounting(装载)
- getInitialState(): 在组件挂载之前调用一次。返回值将会作为 this.state 的初始值。
- componentWillMount()：服务器端和客户端都只调用一次，在初始化渲染执行之前立刻调用。不会触发再次渲染。
- componentDidMount()：在初始化渲染执行之后立刻调用一次，仅客户端有效（服务器端不会调用）,可以设置state，会触发再次渲染，组件内部可以通过 
## Updating (更新)
- componentWillReceiveProps(nextProps) 在组件接收到新的 props 的时候调用。在初始化渲染的时候，该方法不会调用。通常可以调用this.setState方法来比较this.props和nextProps的执行状态，完成对state的修改。在该函数中调用 this.setState() 将不会引起第二次渲染。
- shouldComponentUpdate(nextProps,nextState): 在接收到新的 props 或者 state，将要渲染之前调用。该方法用来拦截新的props或state，然后判断是否更新组件

该方法在初始化渲染的时候不会调用，在使用 forceUpdate 方法的时候也不会。如果确定新的 props 和 state 不会导致组件更新，则此处应该 返回 false。

- componentWillUpdate(nextProps, nextState)：在接收到新的 props 或者 state 之前立刻调用，即更新之前调用

在初始化渲染的时候该方法不会被调用。使用该方法做一些更新之前的准备工作。
> 注意：你不能在该方法中使用 this.setState()。如果需要更新 state 来响应某个 prop 的改变，请使用 componentWillReceiveProps。

- componentDidUpdate(prevProps, prevState): 在组件的更新已经同步到 DOM 中之后立刻被调用。

该方法不会在初始化渲染的时候调用。使用该方法可以在组件更新之后操作 DOM 元素。

## Unmounting(移除)
- componentWillUnmount：在组件从 DOM 中移除的时候立刻被调用。

在该方法中执行任何必要的清理,收尾工作，比如无效的定时器，或者清除在 componentDidMount 中创建的 DOM 元素。

![](/image/react生命周期详解/react生命周期笔记-1.png)




参考文章:

- [React-Native之React速学教程-(中)](http://www.devio.org/2016/08/10/React-Native%E4%B9%8BReact%E9%80%9F%E5%AD%A6%E6%95%99%E7%A8%8B-(%E4%B8%AD)/)
- [react生命周期](https://www.jianshu.com/p/c36a0601b00c)

