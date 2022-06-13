---
title: Javascript中继承的方式
date: 2022-04-14 18:37:24
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220414153048.png
categories:
 - 前端
tags:
  - Javascript
  - 面向对象
---

## 前言

本篇的学习建立在对原型链的理解之上，如果对原型链还不够了解，可以先自行学习相关知识

## 1.原型链继承

``` javascript
function Animal() {
    this.name = 'animal'
}

function Dog() {
    this.eat = 'eat shit'
}

Animal.prototype.getName = function() {
    return this.name
}

Dog.prototype = new Animal()

const dog = new Dog()

console.log(dog.getName()) // animal
console.log(dog.eat) // eat shit
```
原型链继承的本质就是将需要继承的构造函数（Dog）的原型`prototype`指向被继承的构造函数（Animal）的`实例`，这样就获得Animal上的所有属性与方法了。

**缺点**：
- 原型中包含的引用类型属性将被所有实例共享；
- 子类在实例化的时候不能给父类构造函数传参；

``` javascript
function Animal() {
    this.name = 'animal'
    this.type = [0, 1]
}

function Dog() {
    this.eat = 'eat shit'
}

Dog.prototype = new Animal()

const dog1 = new Dog()
const dog2 = new Dog()

dog1.type.push(2)

console.log(dog1.type) // [0, 1, 2]
console.log(dog2.type) // [0, 1, 2]
```

## 2.借用构造函数继承

``` javascript
function Animal() {
    this.name = 'animal'
    this.type = [0, 1]
}

function Dog() {
    Animal.call(this)
    this.eat = 'eat shit'
}

Dog.prototype = new Animal()

const dog1 = new Dog()
const dog2 = new Dog()

dog1.type.push(2)

console.log(dog1.type) // [0, 1, 2]
console.log(dog2.type) // [0, 1, 2]

```

核心代码是`Animal.call(this)`，在创建子类`Dog`的实例时调用父类`Animal`的构造函数，这样就会把父类的每个属性都给子类复制一份

**缺点：**
- 只能继承父类的`实例`属性和方法，不能继承原型属性/方法(因为它只执行了父类构造函数里的内容)
- 无法实现复用，每个子类都有父类实例函数的副本，影响性能

## 3.组合继承

组合上述两种方法就是组合继承。用原型链实现对原型属性和方法的继承，用借用构造函数技术来实现实例属性的继承。

``` javascript
function Animal(name) {
    this.name = name
    this.type = [0, 1]
}

function Dog(name) {
    Animal.call(this, name)
    this.eat = 'eat shit'
}

Animal.prototype.getName = function() {
    return this.name
}

Dog.prototype = new Animal()
Dog.prototype.constructor = Dog

const dog1 = new Dog('王一琳')
const dog2 = new Dog('刘贝利')

dog1.type.push(2)

console.log(dog1.type) // [0, 1, 2]
console.log(dog2.type) // [0, 1, 2]
console.log(dog1.eat) // eat shit
console.log(dog2.eat) // eat shit
console.log(dog1.getName()) // 王一琳
console.log(dog2.getName()) // 刘贝利

```

![](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220414162016.png)

**缺点：**可以清楚的看到，其实我们的代码里调用了两次`Animal()`，所以其`实例`中会存在`原型对象`中的所有属性，造成冗余。

## 4.原型式继承

``` javascript
function clone(obj){
  function F(){}
  F.prototype = obj;
  return new F();
}

// 等价于

Object.create(obj)

```

利用一个`空构造函数`作为媒介，完成对一个对象的浅拷贝

``` javascript
const dog = {
    name: '王一琳',
    friends: ['王二琳', '王三琳']
}

const newDog1 = clone(dog)
newDog1.name = '鸡公'
newDog1.friends.push('王四琳')

const newDog2 = clone(dog)
newDog2.name = '鸡夫'
newDog1.friends.push('王五琳')

console.log(newDog1.name) // 鸡公
console.log(newDog2.name) // 鸡夫

console.log(newDog1.friends) // [ '王二琳', '王三琳', '王四琳', '王五琳' ]
console.log(newDog2.friends) // [ '王二琳', '王三琳', '王四琳', '王五琳' ]
```

**缺点：**

- 原型链继承多个实例的引用类型属性指向相同，存在篡改的可能。
- 无法传递参数

## 5.寄生式继承

核心：在原型式继承的基础上，增强对象（新增属性方法），返回返回这个对象

``` javascript

function createNew(obj){
    const clone = Object.create(obj)
    clone.sayHi = function() {
        console.log('你好')
    }
    return clone
}

const dog = {
    name: '王一琳',
    friends: ['王二琳', '王三琳']
}

const newDog = createNew(dog)

console.log(newDog.name) //王一琳
console.log(newDog.friends) // [ '王二琳', '王三琳' ]
newDog.sayHi() // 你好
```

**缺点（同原型式继承）:**

- 原型链继承多个实例的引用类型属性指向相同，存在篡改的可能。
- 无法传递参数

## 6.寄生组合继承

结合借用构造函数传递参数和寄生模式实现继承

``` javascript
function inheritPrototype(Son, Father) {
    const prototype = Object.create(Father.prototype)
    prototype.constructor = Son
    Son.prototype = prototype
}

function Animal(name) {
    this.name = name
    this.type = [0, 1]
}

Animal.prototype.sayHi = function() {
    console.log('你好')
}

function Dog(name, eat) {
    this.name = name
    this.eat = eat
}

inheritPrototype(Dog, Animal)

const dog1 = new Dog('鸡公', 'eat shit')
const dog2 = new Dog('王一琳', 'eat more shit')
```

![](https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220414171142.png)

**这是最成熟的方法，也是现在库实现的方法**

与`组合`继承相比，`寄生组合继承`只调用了一次父类`Animal`,因此避免了在`Animal.protoype`上创建多余的属性。于此同时，原型链还能保持不变；因此，还能够正常使用instanceof 和isPrototypeOf()

## 7.class继承

``` javascript
class Animal {
    constructor(name) {
        this.name = name
    } 
    getName() {
        return this.name
    }
}
class Dog extends Animal {
    constructor(name, age) {
        super(name)
        this.age = age
    }
}
```