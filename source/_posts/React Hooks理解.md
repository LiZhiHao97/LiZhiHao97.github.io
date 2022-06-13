---
title: React Hooks理解
date: 2022-04-08 15:17:04
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220408172000.png
categories: 前端
tags: 
 - Javascript
 - React
 - React Hooks
---

## 前言

`React Hooks`是React16.8的强大特性，本文主要记录个人学习过程中的一些理解，由于[React官方文档](https://react.docschina.org/docs/hooks-intro.html)对其基本概念的描述已经十分直观,本文也不会啰嗦的再~~复制~~描述一遍它的概念。因此，本文可能不适用于未学习过Hooks的人群。本文的理解基于官方给的例子，后续可能会增添一些新的补充

## React Hooks做了什么事？

### 告别class

Hook 在 class 内部是不起作用的。但你可以使用它们来取代 class。

因为我本身就在`React Hooks`发布之前使用过React，能明显感觉到其带来的好处。

首先我们都知道，在`Hooks`诞生之前，函数组件被称为`无状态组件`，我们无法在函数组件中定义组件的状态，并使用`this.setState`这样的方法。

而class也是学习React的一大障碍，即使我们能够很好的理解Javascript中`this`的定义，但复杂的场景有时也会为我们理解`this`的指向带来困扰。而且我们还不能忘记绑定事件处理器。

`Hooks`解决了这一问题，消除了`this`，让我们的代码更好理解，让函数组件能够使用更多的React特性，也不需要再区分class组件与函数组件的使用场景了。

> 值得注意的是，React并没有弃用class的打算，Hook 和现有代码可以同时工作，我们可以渐进式地使用他们。React团队也将继续为class组件提供支持。

### 消除生命周期中的冗余和分离无关逻辑

首先我们来了解一下`副作用`，引用官方原话：数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用。不管你知不知道这些操作，或是“副作用”这个名字，应该都在组件中使用过它们。

`副作用`这个概念其实并不是来自React，讲到副作用，我们不得不提另一个概念：`纯函数`。简单来说，纯函数就是，通过确定的输入，产生确定的输出，像我们中学时期数学中用的函数，就是纯函数。**数据获取，设置订阅以及手动更改 React 组件中的 DOM**，这些副作用可能会产生不同的结果，改变上下文环境，有了这些，函数就不是纯函数了。

曾经，我们经常在生命周期函数中使用副作用，比如在`componentDidMount`中发起请求获取数据

以下例子为组件每次渲染后更新页面title：

``` javascript
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

在这个例子中，我们需要在两个生命周期函数中编写重复的代码，因为有些场景下，我们希望组件每次渲染后都执行某个操作，但React并没有提供这样的生命周期函数。

我们可以使用`useEffect`处理这个问题

``` javascript
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

我们可以把useEffect Hook看做componentDidMount，componentDidUpdate和componentWillUnmount这三个函数的组合。

对，你发现问题了，上述例子中我们`useEffect`的内容，只是在渲染后执行的,那么怎么实现`componentWillUnmount`呢？

有一些副作用是需要清除的。例如订阅外部数据源。这种情况下，清除工作是非常重要的，可以防止引起内存泄露。

看以下例子,假设我们有一个ChatAPI模块，它允许我们订阅好友的在线状态：

``` javascript
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if (this.state.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```

你会注意到 componentDidMount 和 componentWillUnmount 之间相互对应。使用生命周期函数迫使我们拆分这些逻辑代码，即使这两部分代码都作用于相同的副作用。

我们使用`useEffect`来解决这个问题：

``` javascript
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```
`useEffect`能够返回一个清除函数，这是可选的机制。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。

> 与componentWillUnmount不同的是，useEffect返回的清楚函数在每次重新渲染时都会执行


以前在class中使用生命周期函数时还会遇到一个问题，以以上的例子举例：

``` javascript
class FriendStatusWithCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }
  // ...
```

看，当我们如果又需要设置页面的title，也需要订阅好友的在线状态时，我们只能像以上那样写，这样除了将相关逻辑分割到不同的生命周期函数以外，`componentDidMount`中同时包含了两个不同功能的代码。

那么Hook如何解决这个问题呢？就像你可以使用多个state的Hook一样，你也可以使用多个effect。这会将不相关逻辑分离到不同的 effect中：

``` javascript
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}

```
Hook允许我们按照代码的用途分离他们，而不是像生命周期函数那样。React将按照effect声明的顺序依次调用组件中的每一个effect。

### 复用状态逻辑

如下所示，我们封装了一个好友在线状态的Hook,它将 friendID 作为参数，并返回该好友是否在线

``` javascript
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

我们可以在两个组件中使用它

``` javascript
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

``` javascript
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

首先要注意的是，这两个两个组件的`state`是完全独立的，`Hook`**只复用state的逻辑，不复用state本身**

也是这个例子，让我更好的理解了`Hook`名字的由来，正如它的中文翻译`钩子`,它能够将一些公用的逻辑抽离出组件（或者说一些特性，比如`useState`），与组件本身解耦，在需要使用它的组件里钩入它。

## Hook规则

以下摘自官方文档原话

### 只在最顶层使用 Hook

不要在循环，条件或嵌套函数中调用`Hook，`，确保总是在你的React函数的最顶层调用他们。遵守这条规则，你就能确保`Hook`在每一次渲染中都按照同样的顺序被调用。这让React能够在多次的`useState`和`useEffect`调用之间保持`Hook`状态的正确。

至于为什么要这样做，React文档中也给了相关的原因:
``` javascript
function Form() {
  // 1. Use the name state variable
  const [name, setName] = useState('Mary');

  // 2. Use an effect for persisting the form
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });

  // 3. Use the surname state variable
  const [surname, setSurname] = useState('Poppins');

  // 4. Use an effect for updating the title
  useEffect(function updateTitle() {
    document.title = name + ' ' + surname;
  });

  // ...
}
```
``` javascript
// ------------
// 首次渲染
// ------------
useState('Mary')           // 1. 使用 'Mary' 初始化变量名为 name 的 state
useEffect(persistForm)     // 2. 添加 effect 以保存 form 操作
useState('Poppins')        // 3. 使用 'Poppins' 初始化变量名为 surname 的 state
useEffect(updateTitle)     // 4. 添加 effect 以更新标题

// -------------
// 二次渲染
// -------------
useState('Mary')           // 1. 读取变量名为 name 的 state（参数被忽略）
useEffect(persistForm)     // 2. 替换保存 form 的 effect
useState('Poppins')        // 3. 读取变量名为 surname 的 state（参数被忽略）
useEffect(updateTitle)     // 4. 替换更新标题的 effect

// ...
```
比如我们在组件中定义了四个`Hook`，如果在顶层调用的话，效果如上所示。

我们注意到，二次渲染的时候，`useState`读取的是改变后的状态（如果未改变，就是之前的状态），这是怎么实现的呢？

**只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联。**

如果我们将一个`Hook`的调用放到一个条件语句中

``` javascript
  // 在条件语句中使用 Hook 违反第一条规则
  if (name !== '') {
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  }
```

```
useState('Mary')           // 1. 读取变量名为 name 的 state（参数被忽略）
// useEffect(persistForm)  // 此 Hook 被忽略！
useState('Poppins')        // 2 （之前为 3）。读取变量名为 surname 的 state 失败, 它和上一次的useEffect(persistForm) 对应了
useEffect(updateTitle)     // 3 （之前为 4）。替换更新标题的 effect 失败
```

**这就是为什么Hook需要在我们组件的最顶层调用。如果我们想要有条件地执行一个effect，可以将判断放到Hook的内部：**

``` javascript
  useEffect(function persistForm() {
    // 👍 将条件判断放置在 effect 中
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```

### 只在 React 函数中调用 Hook

不要在普通的 JavaScript 函数中调用 Hook。你可以：
- 在React的函数组件中调用`Hook`
- 在`自定义Hook`中调用其他`Hook`