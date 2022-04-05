---
title: Javascript常见手写题
date: 2022-03-20 19:17:24
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/image-20220320172852864.png
categories:
 - 前端
tags:
  - Javascript
---

## 防抖（debounce）

``` javascript
function debounce(fn, timeout){
  let timer = null
  return function(...args){
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => {
    	fn.apply(this, args)
    }, timeout)
  }
}
```

## 节流（throttle）

``` javascript
function throttle(fn, timeout) {
  let timer = null
  return function (...args) {
    if(timer) return
    timer = setTimeout(() => {
      fn.apply(this, args)
      timer = null
    }, timeout)
  }
}
```

## 函数柯里化（Currying）

``` javascript
function dynamicAdd() {
    return [...arguments].reduce((a, b) => a + b);
}

function curry(fn) {
    const args = [];

    const add = function() {
        args.push(...arguments);
        return add;
    }

    add.toString = function() {
        return fn.apply(null, args)
    }
    return add;
}

const add = curry(dynamicAdd);

console.log(+add(1)) // 1
console.log(+add(1)(2)) // 2
console.log(+add(1)(2)(3)) // 4
console.log(+add(1, 2, 3)(4, 5)) // 25
```

## 数据类型判断

``` javascript
function typeOf(x) {
    if (typeof x !== 'object'){
        return typeof x
    } else {
        return Object.prototype.toString.call(x).slice(8, -1).toLowerCase()
    }
}

console.log(typeOf(1)) // number
console.log(typeOf('1')) // string
console.log(typeOf(null)) // null
console.log(typeOf(undefined)) // undefined
console.log(typeOf(true)) // boolean
console.log(typeOf(Symbol('1'))) // symbol
console.log(typeOf(BigInt(1))) // bigint
console.log(typeOf(new Array([]))) // array
console.log(typeOf(new Date())) // date
console.log(typeOf({})) // object
```

## 数组去重

ES5实现：
``` javascript
function unique(arr) {
  var res = arr.filter(function(item, index) {
    return arr.indexOf(item) === index
  })
  return res
}
```

ES6实现：
``` javascript
const unique = arr => Array.from([...new Set(arr)])
```

## 数组扁平化

ES5实现：
``` javascript
function flatten(arr) {
  var res = []

  for (var i = 0; i < arr.length; i++) {
    if (Array.isArray(arr[i])) {
      res = res.concat(flatten(arr[i]))
    } else {
      res.push(arr[i])
    }
  }

  return res
}
```

ES6实现：
``` javascript
function flatten(arr) {
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr)
  }
  return arr
}
```

## Promise

### Promise.all

``` javascript
const all = promiseList => {
  const result = []
  let count = 0
  return new Promise((resolve, reject) => {
      const addData = (index, value) => {
          result[index] = value
          count++
          if (count === promiseList.length) resolve(result)
      }
      promiseList.forEach((promise, index) => {
          if (promise instanceof Promise) {
              promise.then(res => {
                  addData(index, res)
              }, err => reject(err))
          } else {
              addData(index, promise)
          }
      })
  })
}
```

### Promise.race

``` javascript
const race = promiseList => {
  return new Promise((resolve, reject) => {
      promiseList.forEach(promise => {
          if (promise instanceof Promise) {
              promise.then(res => {
                  resolve(res)
              }, err => {
                  reject(err)
              })
          } else {
              resolve(promise)
          }
      })
  })
}
```

### Promise.allSettled

``` javascript
const allSettled = promiseList => {
  return new Promise((resolve, reject) => {
      const res = []
      let count = 0
      const addData = (status, value, i) => {
          res[i] = {
              status,
              value
          }
          count++
          if (count === promiseList.length) {
              resolve(res)
          }
      }
      promiseList.forEach((promise, i) => {
          if (promise instanceof Promise) {
              promise.then(res => {
                  addData('fulfilled', res, i)
              }, err => {
                  addData('rejected', err, i)
              })
          } else {
              addData('fulfilled', promise, i)
          }
      })
  })
}
```

### Promise.any

``` javascript
const any = promiseList => {
  return new Promise((resolve, reject) => {
      let count = 0
      promiseList.forEach((promise) => {
        if (promise instanceof Promise) {
            promise.then(val => {
              resolve(val)
            }, err => {
                count++
                if (count === promiseList.length) {
                    reject(new AggregateError('All promises were rejected'))
                }
            })
        } else {
            resolve(promise)
        }
      })
  })
}
```