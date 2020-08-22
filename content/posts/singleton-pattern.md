+++
title = "单例模式的介绍和示例"
date = 2020-08-18T12:49:55+08:00
slug = "singleton-pattern-introduction-and-examples"
images = []
tags = ["design patterns", "js"]
categories = []
draft = true
+++

单例模式（Singleton Pattern）是指构建一个可以全局访问的类的实例。该模式属于一种创建型的模式，一般来说用于实例化一个类，如果该类的实例已经存在的话，则返回该实例。

## 目的

在JS中，单例模式可以提供给用户一个可共享资源的自定义命名空间来访问一个类，并将其从全局命名空间中分隔开，允许用户在系统不同部分用同一个实例来进行所基于类的操作运算等。

## 示例

```
// 定义一个类
class SingletonPatternClass {
  constructor() {
    const instance = this.constructor.instance
    if(instance) {
      console.log("单例模式实例已经存在。")
      return instance
    }
    console.log("新建单例模式实例。")
    this.constructor.instance = this
  }
  selfIntroduce() {
    console.log("这是一个单例模式实例。")
  }
}
const mySingletonPattern = new SingletonPatternClass()
// 输出："新建单例模式实例。"
mySingletonPattern.selfIntroduce()
// 输出："这是一个单例模式实例。"

const alternativeSingletonPattern = new SingletonPatternClass()``
// 输出："单例模式实例已经存在。"
---

在JS中另外一种实现单例模式的方式如下：

```
const SingletonClass = (function() {
  let instance
  function init() {
    return 
  }

})()
```

## 结尾

## 参考

- [Singleton pattern - wikipedia](https://en.wikipedia.org/wiki/Singleton_pattern)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript)
- [Javascript: best Singleton pattern - Stack Overflow](https://stackoverflow.com/questions/1635800/javascript-best-singleton-pattern)

