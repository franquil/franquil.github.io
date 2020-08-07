+++
title = "构造器模式介绍和应用"
date = 2020-08-07T08:30:19+08:00
tags = ["js", "design patterns"]
draft = false
slug = "constructor-pattern-introduction-and-examples"
summary = "最为常见、基本的创建对象模式之一：构造器模式，其功能在于通过用户所传入参数生成一个可编程对象。"
+++

构造器（或构造者）模式(Constructor Pattern)是面向对象编程中一种最为常见的模式之一，属于一种创建型模式。其主要功能与目的在于初始化和构建一个对象。通常情况下，其会根据所传入的参数来对所构建的对象属性进行配置，有时也会根据内置默认的对象属性来构建对象。

<br/>

## 构造器模式在JS中的示例

以JS为例，可以采用函数的方式来创建并返回一个对象。一个简单的构建器模式示例：


```

function constructAnObject(nameString) {
  const newObject = new Object()
  newObject.name = nameString || 'constructor'
  newObject.introduceSelf = function() {
    console.log('I am a ' + this.name + ' pattern example.')
  }
  return newObject
}

const myObject = new constructAnObject('constructor')
myObject.introduceSelf()
// 输出： "I am a constructor pattern example."

console.log(myObject.name)
// 输出："constructor"

```

<br/>

## 采用'protyotype'方式实现构建器模式

除去采用函数方式外，相似地，利用'prototype'方式也可以很容易实现构建器模式。例如：

```

function constructAnObject(nameString) {                                                            
    this.name = nameString || 'constructor'
}                                                                                                   

constructAnObject.prototype.introduceSelf = function() {
   console.log('I am a ' + this.name + ' pattern example.')
}

const myObject = new constructAnObject('constructor') 

myObject.introduceSelf()                                                                            
// 输出： "I am a constructor pattern example."                                                                                                   
console.log(myObject.name)                                                                          
// 输出："constructor"    

```

<br/>

## 采用'class'方式实现构建器模式

在ES6标准中，JS支持采用'class'的方式来构建对象。所以，上面的构建器模式可以采用如下方式构建：

```

class constructAnObject {
  constructor(nameString) {
    this.name = nameString || 'constructor'
  }
  introduceSelf() {
    console.log('I am a ' + this.name + ' pattern example.')
  }
}

const myObject = new constructAnObject('constructor')
myObject.introduceSelf()
console.log(myObject.name)

```

<br/>

## 构建器模式图示

构建器模式可以用下图来进行表示。

![构建器模式-constructor-pattern](/images/constructor-pattern.jpg "构建器模式图示")

<br/>

## 总结

构建器模式可以说是面向对象编程语言中最为常见、最为通用和简单的构建对象方式之一。本文以JS为例，对该模式进行了简要的介绍和实现方式的演示，其在其它编程语言中虽会有语法上的差异，但原理上可以说是一样的。该模式主要功能是，通过构建函数并根据用户所提供的参数来构成一个可操作的功能对象，将构建器（函数或类）内的功能进行差异化的设定，以便之后按照用户要求进行执行。

<br/>

## 参考
- <https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming>
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#constructorpatternjavascript)
- [Classes Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
