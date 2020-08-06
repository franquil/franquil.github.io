+++
title = "构造器模式介绍和应用"
date = 2020-08-04T17:40:19+08:00
images = []
tags = ["js", "design patterns"]
categories = []
draft = true
slug = "constructor-pattern-introduction-and-examples"
+++

构造器（或构造者）模式(Constructor Pattern)是面向对象编程中一种最为常见的模式之一，属于一种创建型模式。其主要功能与目的在于初始化和构建一个对象。通常情况下，其会根据所传入的参数来对所构建的对象属性进行配置，有时也会根据需求内置默认的对象属性。

## 构造器模式在JS中的示例

以JS为例，可以采用函数的方式来创建并返回一个对象。

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

在ES6标准中，JS支持采用`class`的方式来构建对象。所以，上面的构建器模式可以采用如下方式构建：

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



## 参考
- [Constructor_(object-oriented_programming)](https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#constructorpatternjavascript)
- [Classes Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
