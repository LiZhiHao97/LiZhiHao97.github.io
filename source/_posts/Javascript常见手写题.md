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

```javascript
function debounce(fn, timeout) {
  let timer = null;
  return function (...args) {
    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, timeout);
  };
}
```

## 节流（throttle）

```javascript
function throttle(fn, timeout) {
  let timer = null;
  return function (...args) {
    if (timer) return;
    timer = setTimeout(() => {
      fn.apply(this, args);
      timer = null;
    }, timeout);
  };
}
```

## 函数柯里化（Currying）

```javascript
function dynamicAdd() {
  return [...arguments].reduce((a, b) => a + b);
}

function curry(fn) {
  const args = [];

  const add = function () {
    args.push(...arguments);
    return add;
  };

  add.toString = function () {
    return fn.apply(null, args);
  };
  return add;
}

const add = curry(dynamicAdd);

console.log(+add(1)); // 1
console.log(+add(1)(2)); // 3
console.log(+add(1)(2)(3)); // 6
console.log(+add(1, 2, 3)(4, 5)); // 15
```

## 数据类型判断

```javascript
function typeOf(x) {
  if (typeof x !== "object") {
    return typeof x;
  } else {
    return Object.prototype.toString.call(x).slice(8, -1).toLowerCase();
  }
}

console.log(typeOf(1)); // number
console.log(typeOf("1")); // string
console.log(typeOf(null)); // null
console.log(typeOf(undefined)); // undefined
console.log(typeOf(true)); // boolean
console.log(typeOf(Symbol("1"))); // symbol
console.log(typeOf(BigInt(1))); // bigint
console.log(typeOf(new Array([]))); // array
console.log(typeOf(new Date())); // date
console.log(typeOf({})); // object
```

## 数组去重

ES5 实现：

```javascript
function unique(arr) {
  var res = arr.filter(function (item, index) {
    return arr.indexOf(item) === index;
  });
  return res;
}
```

ES6 实现：

```javascript
const unique = (arr) => Array.from([...new Set(arr)]);
```

## 数组扁平化

ES5 实现：

```javascript
function flatten(arr) {
  var res = [];

  for (var i = 0; i < arr.length; i++) {
    if (Array.isArray(arr[i])) {
      res = res.concat(flatten(arr[i]));
    } else {
      res.push(arr[i]);
    }
  }

  return res;
}
```

ES6 实现：

```javascript
function flatten(arr) {
  while (arr.some((item) => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}
```

## 实现深拷贝

乞丐版：

```javascript
function deepClone(target) {
  return JSON.parse(JSON.stringify(target));
}
```

存在的问题：

- 对象中有字段值为 undefined，转换后则会直接字段消失
- 对象如果有字段值为 RegExp、Date 对象，转换后则字段值会变成{}
- 对象如果有字段值为 NaN、+-Infinity，转换后则字段值变成 null
- 对象如果有环引用，转换直接报错
- 不会拷贝键为 Symbol 类型的对象

进阶版：

```javascript
const isObject = (target) => typeof target === "object" && target !== null;

function deepClone(target, map = new WeakMap()) {
  if (target === undefined) return undefined;
  if (map.get(target)) return target;

  const Constructor = target.constructor;
  if (/^(RegExp|Date)$/i.test(Constructor.name)) {
    return new Constructor(target);
  }

  if (isObject(target)) {
    const temp = Array.isArray(target) ? [] : {};
    map.set(target, true);

    for (let key in target) {
      if (target.hasOwnProperty(key)) {
        temp[key] = deepClone(target[key], map);
      }
    }

    return temp;
  } else {
    return target;
  }
}
```

ps: 这个版本还是有很多的不足之处，但已经能解决大多问题了

## Ajax

```javascript
const ajax = {
  get(url, fn) {
    const xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject();
    xhr.open("GET", url, true);
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4 && xhr.status === 200) {
        fn(xhr.responeText);
      }
    };
    xhr.send();
  },
  post(url, data, fn) {
    const xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject();
    xhr.open("POST", url, true);
    xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4 && xhr.status === 200) {
        fn(xhr.responeText);
      }
    };
    xhr.send(data);
  },
};
```

## 发布订阅模式与观察者模式

| 设计模式 | 观察者模式                                       | 发布订阅模式                                               |
| -------- | ------------------------------------------------ | ---------------------------------------------------------- |
| 主体     | Object 观察者、Subject 目标对象                  | Publisher 发布者、Event Channel 事件中心、Subscribe 订阅者 |
| 主体关系 | Subject 中通过 observerList 记录 ObServer        | Publisher 和 Subscribe 不想不知道对方，通过中介联系        |
| 优点     | 角色明确，Subject 和 Object 要遵循约定的成员方法 | 松散耦合，灵活度高，通常应用在异步编程中                   |
| 缺点     | 紧耦合                                           | 当事件类型变多时，会增加维护成本                           |
| 使用案例 | 双向数据绑定                                     | 事件总线 EventBus                                          |

### 发布订阅模式

```javascript
class PubSub {
  constructor() {
    this.events = {};
  }

  publish(type, ...args) {
    if (this.events[type]) {
      this.events[type].forEach((cb) => cb(args));
    }
  }

  subscribe(type, cb) {
    if (!this.events[type]) {
      this.events[type] = [];
    }
    this.events[type].push(cb);
  }

  ubsubscribe(type, cb) {
    if (this.events[type]) {
      const index = this.events[type].findIndex((e) => e === cb);
      this.events.splice(index, 1);
    }

    if (this.events[type].length === 1) {
      delete this.events[type];
    }
  }

  unsubscribeAll() {}
}

const pubsub = new PubSub();

pubsub.subscribe("war", (info) => {
  console.log(`发布了${info}`);
});

pubsub.subscribe("routine", (info) => {
  console.log(`发布了${info}`);
});

pubsub.publish("war", "猎杀老鼠");
pubsub.publish("routine", "干杂活");
```

### 观察者模式

```javascript
class Observer {
  constructor(name) {
    this.name = name;
  }

  update({ tasktype, taskinfo }) {
    // 可以给观察者自定义一些处理观察到的数据的逻辑
    if (tasktype === "war") {
      console.log(`${this.name}收到猎杀任务-${taskinfo}通知`);
    }
    if (tasktype === "routine") {
      console.log(`${this.name}收到日常任务-${taskinfo}通知`);
    }
  }
}

class Subject {
  constructor() {
    this.observerList = [];
  }

  addObserver(observer) {
    this.observerList.push(observer);
  }

  notify(task) {
    this.observerList.forEach((observer) => observer.update(task));
  }
}

const subject = new Subject();

const observer1 = new Observer("弟子一");
const observer2 = new Observer("弟子二");

subject.addObserver(observer1);
subject.addObserver(observer2);

subject.notify({ tasktype: "war", taskinfo: "猎杀老鼠" });
```

## 将 url 中的 queryString 转为 map

```javascript
const urlSearchParams = new URLSearchParams(window.location.search);
const params = Object.fromEntries(urlSearchParams.entries());
```

## 获取页面中数量前三的标签

```jsx
const fn = () => {
  const tags = document.getElementsByTagName("*");
  const map = {};

  for (const tag of tags) {
    const tagName = tag.tagName;
    if (map[tagName]) map[tagName] += 1;
    else map[tagName] = 1;
  }

  const ret = Object.entries(map);

  ret.sort((a, b) => b[1] - a[1]);

  return ret.slice(0, 3);
};
```

## Nodejs 爬取网页源码并对内容进行替换后保存文件

```javascript
const http = require("http");
const fs = require("fs");

const fn = () => {
  http.get("http://www.baidu.com", (res) => {
    let code = "";
    res.on("data", (data) => {
      code += data;
    });
    res.on("end", () => {
      code = code.replaceAll("百度", "百度123");
      fs.writeFile("code.html", code, (err) => {
        if (err) console.error(err);
        console.log("write done");
      });
    });
  });
};

fn();
```

## 实现 new 关键字

```javascript
function objectFactory() {
  const obj = new Object();
  const Constructor = Array.prototype.shift.call(arguments);
  obj.__proto__ = Constructor.prototype;
  const ret = Constructor.apply(obj, arguments);

  return typeof ret === "object" ? ret || obj : obj;
}
```

## 实现 instanceof 关键字

```javascript
function instanceOf(left, right) {
  let proto = left.__proto__;

  while (proto !== null) {
    if (proto === right.prototype) return true;
    proto = proto.__proto__;
  }

  return false;
}
```

## 实现对象被 for of 遍历

- `for in`用于遍历可枚举数据类型
  - 对象
  - 数组
  - 字符串
- `for of`用于遍历可迭代数据类型
  - 数组
  - 字符串
  - Map
  - Set

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
  [Symbol.iterator]: function* () {
    for (const key in obj) {
      yield obj[key];
    }
  },
};

for (const val of obj) {
  console.log(val);
}
```

## Promise

### 实现

#### Promise

看林三心大佬的这篇[文章](https://juejin.cn/post/6994594642280857630)

#### Promise.all

```javascript
const all = (promiseList) => {
  const result = [];
  let count = 0;
  return new Promise((resolve, reject) => {
    const addData = (index, value) => {
      result[index] = value;
      count++;
      if (count === promiseList.length) resolve(result);
    };
    promiseList.forEach((promise, index) => {
      if (promise instanceof Promise) {
        promise.then(
          (res) => {
            addData(index, res);
          },
          (err) => reject(err)
        );
      } else {
        addData(index, promise);
      }
    });
  });
};
```

#### Promise.race

```javascript
const race = (promiseList) => {
  return new Promise((resolve, reject) => {
    promiseList.forEach((promise) => {
      if (promise instanceof Promise) {
        promise.then(
          (res) => {
            resolve(res);
          },
          (err) => {
            reject(err);
          }
        );
      } else {
        resolve(promise);
      }
    });
  });
};
```

#### Promise.allSettled

```javascript
const allSettled = (promiseList) => {
  return new Promise((resolve, reject) => {
    const res = [];
    let count = 0;
    const addData = (status, value, i) => {
      res[i] = {
        status,
        value,
      };
      count++;
      if (count === promiseList.length) {
        resolve(res);
      }
    };
    promiseList.forEach((promise, i) => {
      if (promise instanceof Promise) {
        promise.then(
          (res) => {
            addData("fulfilled", res, i);
          },
          (err) => {
            addData("rejected", err, i);
          }
        );
      } else {
        addData("fulfilled", promise, i);
      }
    });
  });
};
```

#### Promise.any

```javascript
const any = (promiseList) => {
  return new Promise((resolve, reject) => {
    let count = 0;
    promiseList.forEach((promise) => {
      if (promise instanceof Promise) {
        promise.then(
          (val) => {
            resolve(val);
          },
          (err) => {
            count++;
            if (count === promiseList.length) {
              reject(new AggregateError("All promises were rejected"));
            }
          }
        );
      } else {
        resolve(promise);
      }
    });
  });
};
```

### 应用

#### 接口请求超时

```javascript
// 模拟请求,1000ms后成功
const request = () => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("请求成功啦");
    }, 1000);
  });
};

const sleep = (delay) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject("请求超时啦");
    }, delay);
  });
};

const limitRequest = (fn, delay) => {
  return Promise.race([fn(), sleep(delay)]);
};

// test

limitRequest(request, 500)
  .then((res) => console.log(res))
  .catch((err) => console.log(err)); // 请求超时啦
limitRequest(request, 2000)
  .then((res) => console.log(res))
  .catch((err) => console.log(err)); // 请求成功啦
```

#### Promise 控制并发量

```javascript
class Scheduler {
  constructor(max) {
    this.max = max;
    this.tasks = [];
    this.pool = [];
  }

  // task()需要返回promise
  addTask(task) {
    this.tasks.push(task);
  }

  async start() {
    for (const task of this.tasks) {
      const cur = task();
      this.pool.push(cur);
      cur.then((res) => {
        this.pool.splice(this.pool.indexOf(cur), 1);
      });
      if (this.pool.length === this.max) {
        await Promise.race(this.pool);
      }
    }
  }
}

const taskFn = (delay, info) => {
  const task = async () => {
    return new Promise((resolve) => {
      setTimeout(() => {
        console.log(info);
        resolve();
      }, delay);
    });
  };
  return task;
};

const scheduler = new Scheduler(2);

scheduler.addTask(taskFn(1000, "1"));
scheduler.addTask(taskFn(500, "2"));
scheduler.addTask(taskFn(300, "3"));
scheduler.addTask(taskFn(400, "4"));

scheduler.start();
```
