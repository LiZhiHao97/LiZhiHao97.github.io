---
title: python之函数使用
date: 2022-06-06 11:55
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/71654487516_.pic.jpg
categories:
  - 前端
tags:
  - React Native
  - React
---

解决方案来自[StackOverflow](https://stackoverflow.com/questions/52368342/invariant-violation-text-strings-must-be-rendered-within-a-text-component)

## 问题

当**React Native**从 0.54 升级到 0.57，代码报了以下错误

`Invariant Violation: Text strings must be rendered within a <Text> component`

乍一看好像是文本没有写在`<Text>`标签里，查了一下代码发现没有这个问题。最后网上搜索后解决

以下为伪代码：

```javascript
condition && <MyComponent />;
```

需要修改为：

```javascript
condition ? <MyComponent /> : null;
```

这是因为`condition`为`undefined`时，将不会渲染任何组件，而`null`或`空数组`可以安全的显示空白区域

> Tips: undefined && true 与 true && undefined 的结果都为 undefined
