+++
title = "单例模式的介绍和示例"
date = 2020-09-09T08:18:55+08:00
slug = "singleton-pattern-introduction-and-examples"
tags = ["design patterns", "js"]
categories = []
draft = true
+++

单例模式（Singleton Pattern）用于构建可以全局访问的一个类的实例。该模式属于一种创建型的模式，一般来说用于限制类的实例为单一的一个实例，如果该类的实例已经存在的话，则返回该实例。该实例一般在整个系统中都可以调用。

然而，有批评认为该模式属于一种“反模式”，因为其在有些情况下不会带来太多收益，且会引进不必要的对类的实例化限制，并会构建不必要的全局变量等。

## 目的

在JS中，单例模式可以提供给用户一个可共享资源的自定义命名空间来访问一个类，并将其从全局命名空间中分隔开，允许用户在系统不同部分用同一个实例来进行基于类的操作运算和数据共享。

## 示例

```
// 定义一个类
class SingletonPatternClass {
  constructor() {
    const instance = SingletonPatternClass.instance
    if(!!instance) {
      console.log("单例模式实例已经存在。")
      return instance
    }
    console.log("新建单例模式实例。")
    SingletonClass.instance = this
    return this
  }
  selfIntroduce() {
    console.log("这是一个单例模式实例。")
  }
}
const mySingletonPattern = new SingletonPatternClass()
// 输出："新建单例模式实例。"
mySingletonPattern.selfIntroduce()
// 输出："这是一个单例模式实例。"

const alternativeSingletonPattern = new SingletonPatternClass()
// 输出："单例模式实例已经存在。"

```

在JS中用函数方式实现单例模式的一种方式如下：

```
function JSSingletonClass() {
  if (arguments.callee.instance) {
    return arguments.callee.instance
  }
  arguments.callee.instance = this
  this.selfIntroduce = function() {
    console.log('这也是一个单例模式实例。')
  }
}

const myJSSingletonClass = new JSSingletonClass()
myJSSingletonClass.selfIntroduce()
// 输出： 这也是一个单例模式实例。

const alternativeSingleton = new JSSingletonClass()
console.log(myJSSingletonClass === alternativeSingleton)
// 输出： true

```

## 结尾

单例模式在全局环境下，限制用户只可访问一个实例化的类。它方便了用户在不同子环境中，访问同一个功能模块的需求。然而，也带来了潜在的负面效果，例如：在共享的实例过大时，会占用过多内存；在需要多个类的实例时，会带来困难；由于潜在的隐藏依赖，会带来测试问题；无法轻易添加依赖；及之前提到的全局变量等等问题。如果在开发过程中过度使用的话，可能会带来很大的潜在维护成本，所以需要酌情考虑使用该模式。


## 参考及延伸阅读

- [Singleton pattern - wikipedia](https://en.wikipedia.org/wiki/Singleton_pattern)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript)
- [Javascript: best Singleton pattern - Stack Overflow](https://stackoverflow.com/questions/1635800/javascript-best-singleton-pattern)
- [function - Simplest/Cleanest way to implement singleton in JavaScript - Stack Overflow](https://stackoverflow.com/questions/1479319/simplest-cleanest-way-to-implement-singleton-in-javascript)
- [JavaScript Design Patterns: Singleton](https://robdodson.me/javascript-design-patterns-singleton/)
- [Use your singletons wisely](https://www.ibm.com/developerworks/webservices/library/co-single/index.html)
