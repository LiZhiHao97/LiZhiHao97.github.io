---
title: '在javascript使用es6,es7'
date: 2017-02-13 20:33:43
index_img: https://rmt.dogedoge.com/fetch/fluid/storage/hexo-static/cover.jpg?w=480&fmt=webp
categories:
 - 前端
tags:
  - 前端
  - javascript
  - es6
  - es7s
---

###  const 
```javascript
const a=3; //定义常量
// 如果要用es 5的方式怎么写呢？？
Object.defineProperty(window, "a", {
  value: 37,
  writable: false,
});
console.log(window.a)
```

### 作用域
es6的作用域
```javascript
for (let i = 0; i < 10; i++) {
  // ...
}

console.log(i);
// ReferenceError: i is not defined
```
在es5中,并没有块级作用域，先用es5写一个作用域看看

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
a[5](); // 10
a[4](); // 10
a[3](); // 10
a[2](); // 10
a[1](); // 10
```
> 上面代码中，变量i是var命令声明的，在全局范围内都有效，所以全局只有一个变量i。每一次循环，变量i的值都会发生改变，而循环内被赋给数组a的函数内部的console.log(i)，里面的i指向的就是全局的i。也就是说，所有数组a的成员里面的i，指向的都是同一个i，导致运行时输出的是最后一轮的i的值，也就是 10。

#### ES6 定义属性
es5怎么写呢
```
var x = 1, y = 2;
var object = {
　　x: x,
　　y: y
};
console.log(object.x); //output "1"
```javascript
在es6中
```
//给一个属性赋一个变量值，如果变量名和属性名相同，ES6 提供了一个简洁的语法，可以省略变量名

let x = 1, y = 2;
let object = { 
　　x, 
　　y 
};
console.log(object.x); //output "1"
```



### 箭头函数
```javascript
// es5中的this
var A = function () {
    this.a = 'a';
    this.b = 'b';
    this.c = {
        a: 'a++',
        b: function () {
            console.log(this.a);
        }
    }
}

console.log(new A().c.b()) // a++ 因为es5中的this即谁调用它，this就指向谁
//es6
var A = function () {
    this.a = 'a';
    this.b = 'b';
    this.c = {
        a: 'a++',
        b:  ()=>{
            console.log(this.a);
        }
    }
}

console.log(new A().c.b()) // a,箭头函数this的指向是定义时this的指向，b函数在定义的时候this就指向函数体里面的this
```

```javascript
//带有默认参数的箭头函数
let hello =(name='world')=>{
  console.log(`hello ${name}`) //注意这边不是单引号
}
hello() //hello world
hello('skl')//hello skl
```
```
//多个参数
let cal1 =(num1,num2)=>num1*num2
let arr=[6,7]
console.log(cal1(...arr))//展开
```
```
function hello(name1,name2){
  console.log(name1,name2)
}
let arr =['skl1','skl2'];
//es5
//hello.apply(null,arr)
//es6
hello(...arr)

```
### 默认参数
```javascript
// es5
function a(x,y ) {
    x=x||1;
    y=y||2;
}
// es6
function b(x=1,y=2){
    return x+y;
}
```
### 可变参数
```javascript
{   //es5
    function f() {
        var a = Array.prototype.slice.call(arguments);
        var sum = 0;
        a.forEach(function (item) {
            sum += item;
        })
        return sum;
    }
    console.log(f(1, 2, 3, 4)) 
}
{
    //es5
    function f(...a) {
        var sum = 0;
        a.forEach(function (item) {
            sum += item;
        })
        return sum;
    }
    console.log(f(1, 2, 3, 4)) 
}
// 扩展运算符的使用
// 合并数组 es5
{
    var a=[1,2,4];
    var b=[11,111,111];
    var c=b.concat(a);
    console.log(c)
}
// 合并数组 es6运用扩展运算符
{
    var a=[1,2,4];
    var b=[11,111,111];
    var c=[...a,...b];
    console.log(c)
}
```

### 对象扩展
```javascript
var obj={
  name:'skl',
  age:18,
}
console.log(Object.keys(obj))//["name","age"]
console.log(Object.values(obj))//["skl",18]
console.log(Object.entries(obj))// key 和value变成一个数组
```
```javascript
const name = 'skl'
const age='nian';
const height='shengao'
const obj = {
  [age]:18,
  height
}
obj[name] = 'hello skl';
console.log(obj)//{nian: 18, height: "shengao", skl: "hello skl"}
```
```javascript
const obj1={name:'skl',age:18}
const obj2={type:'ha',aa:90}
console.log({...obj1,...obj2})//{name: "skl", age: 18, type: "ha", aa: 90}

```
```javascript
//结构赋值
const arr =['skl','sk2'];
let [arg1,arg2]=arr;
console.log(arg1,arg2)//skl sk2

const obj={nian: 18, height: "shengao", skl: "hello skl"}
const {nian,height}=obj;
console.log(nian,height)//18 shengao
```
### 代理做数据保护
#### es5中的数据保护
```javascript
{
  // 数据保护（相当于私有变量）es5的写法,定义一个构造函数
  var Person = function () {
    var data = {
      name: 'es5',
      sex: 'male',
      age: 20
    }
    // getter 和setter
    this.get = function (key) {
      return data[key]
    }
    this.set = function (key, value) {
      if (key !== 'sex') {
        data[key] = value;
      }
    }
  }
  // 声明实例
  var person = new Person();
  // 读取
  console.table({
    name: person.get('name'),
    sex: person.get('sex'),
    aeg: person.get('ageqe3w')
  })
  // 修改
  person.set('name', '啦啦啦');// 这时候console的结果就会变了
  person.set('sex', 'male')// console出来发现没有用因为数据被保护了
}
{
  // 另一种写法
  var Person = {
    name: 'skl',
    age: 10,
  }
  // sex只可读不可写
  Object.defineProperties(Person, 'sex', {
    writeable: false,
    value: 'male',
  })
  console.table({
    name: Person.name,
    age: Person.age,
    sex: Person.sex,
  })
  // 若强行给只读属性复制会报错
}
```
#### es6中通过代理的方式做数据保护
```javascript
{
  // es的代理 
  let Person = {
    name: '11',
    age: 11,
    sex: 'male'
  }
  //person是暴露给用户的，吧上面的Person保护起来
  let person = new Proxy(Person, {
    get(target, key) {
      return target[key]
    },
    set(target, key, value) {
      if (key !== 'sex') {
        target[key] = value;
      }
    }
  })
}
```
### 字符串拼接
在es5中，凭借字符串要不断的+,尤其是在html模板的时候，变得特别不方便
```javascript
const a=3;
console.log(`skl${a}`)//skl3
`Hello ${'World'}`
// "Hello World"
```

### for... of...
```javascript
var arr = ["a", "b", "c"];
var iterator = arr.entries();
console.log(iterator) //Array Iterator {}

for (let e of iterator) {  //返回有下标的数组
    console.log(e) 
    //[0, "a"]
    //[1, "b"]
    //[2, "c"]
}
```
```javascript
var arr = ["a", "b", "c"];
var iterator = arr;

for (let e of iterator) {
    console.log(e);
    //a b c
}
```
```javascript
var arr = ["a", "b", "c"];
var iterator = arr.entries();

for (let [index,e] of iterator) {
    console.log(index,e);
    // 0 "a"
    // 1 "b"
    // 2 "c"
}
```
```javascript
var arr = ["a", "b", "c"];
var iterator = arr.entries();

for (let [index,e] of iterator) {
    console.log(e);
}
// a 
// b
// c
```


### promise

```javascript
let state = 1;
function step1(resolve, reject) {
  console.log('1.开始-做饭');
  if (state == 1) {
    resolve('做饭--完成');
  } else {
    reject('做饭--出错');
  }
}

function step2(resolve, reject) {
  console.log('2.开始-吃饭');
  if (state == 1) {
    resolve('吃饭--完成');
  } else {
    reject('吃饭--出错');
  }
}

function step3(resolve, reject) {
  console.log('3.开始-收拾');
  if (state == 1) {
    resolve('收拾--完成');
  } else {
    reject('收拾--出错');
  }
}

new Promise(step1)
  .then(function (val) {
    console.log(val)
    return new Promise(step2)
  })
  .then(function (val) {
    console.log(val)
    return new Promise(step3)
  }).then(function (val) {
    console.log(val)
  })
```

### async
```javascript
async function testAsync(){
  return 'hello async'
}
const result =testAsync();
console.log(result); // 返回的是一个promise
```
```javascript

function getSomething(){
  return 'something'
}
async function testAsync(){
  return 'hello async'
}

async function test(){
  const v1 = await getSomething();
  const v2 = await testAsync();
  console.log(v1,v2)

}
test()

```
 模拟异步请求
```javascript
function takeLongTime(){
  return new Promise(resolve=>{
    setTimeout(()=>{
      resolve("long_time_value")
    },1000)
  })
}

async function test(){
  const v = await takeLongTime()
  console.log(v)
}
test()
```

