+++
title = "工厂模式的介绍和示例"
date = 2020-08-07T08:48:38+08:00
tags = ["design patterns"]
draft = true
slug = "factory-pattern-introduction-and-examples"
summary = "工厂模式是一种在面向对象编程中用于创造对象的模式，本文简要介绍了该模式，并用示例了该模式的实现方式。"
+++

工厂模式(Factory Pattern)属于一种创造型模式，其根本目的在于创建一个对象。然而，不同于[构建器模式]({{< ref "/posts/constructor-pattern.md" >}})，工厂本身不会直接创建对象，而是会根据用户所传入的参数，通过工厂方法（函数）来调用构建器，从而实现创建对象的功能。该方式也被称为工厂方法模式（Factory Method Pattern）。


假设当前用户需要创建一个图书对象，虽然图书对象可能包含不同的种类（如：词典、诗集、小说等等），但它们都可能分享相同的图书对象方法（如：阅读、销售、上架等）或属性，为了避免分别为不同种类图书分别创建构建器等重复工作，可以采用一个“工厂”用来管理这些不同的图书对象构建器，这样用户只需指定类别，工厂就会根据用户的需求创建一个所指定的图书对象。

## 目的

工厂模式的目的在于创建一个灵活及可重复使用的方式，将对象构建的方式从对象本身分隔开，以便方面实现、修改、测试和管理等。这也符合SOLID原理中开放/闭合原则（Open/Close Principle）。

## 何时使用工厂模式

- 当对象实例的创建过程过于复杂，需要将其与对象本身分离开来降低复杂度时；
- 当不同的对象实例需要根据不同参数来进行创建时；
- 当所需创建的不同小的对象实例需要共享属性或方法时；

等等。

## 工厂模式图示

工厂模式可以用下图来进行


## 采用JS进行示例

有ES5的支持，可以采用'class'方式来进行构建。

```
// 一个可共享基本的图书类
class Book {
  constructor(optionsObject) {
    this.title = optionsObject.title
    this.author = optionsObject.author
    this.publisher = optionsObject.publisher
    this.price = optionsObject.price
  }

  show() {
    console.log(`这本书《${this.title}》是由${this.author}撰写的,出版商为${this.publisher}，定价：${this.price}元。`)
  }
}

// 基于'Book'类，创建一个'Dictionary'类
class Dictionary extends Book {
  constructor(optionsObject) {
    super(optionsObject)
    this.type = "词典"
  }

  searchWord(wordString) {
    console.log(`您正在使用${this.type}《${this.title}》查找${wordString}...`)
  }
}

// 基于'Book'类，创建一个'Poetry'类
class Poetry extends Book {
  constructor(optionsObject) {
    super(optionsObject)
    this.type = "诗集"
  }

  readPoem(titleString) {
    console.log(`您正在阅读诗集《${this.title}》中的诗：${titleString}。`)
  }
}

// 构建一个基于'Book'类的工厂函数来实例化具体的子类
function bookFactory(optionsObject) {
  this.bookClass = Book
  switch(optionsObject.type) {
    case 'dictionary':
      this.bookClass = Dictionary
      break;
    case 'poetry':
      this.bookClass = Poetry
      break;
  }
  return new this.bookClass(optionsObject)
}

// 实例化一个'dictionary'类
const dictOptions = {
  title: '设计模式',
  author: '大国',
  publisher: '某出版社',
  price: 69.0,
  type: 'dictionary'
}

const myDictionary = new bookFactory(dictOptions)
myDictionary.show()
// 输出：这本书《设计模式》是由大国撰写的,出版商为某出版社，定价：69元。
myDictionary.searchWord('工厂')
// 输出：您正在使用词典《设计模式》查找工厂...

// 实例化一个'poetry'类
const poemOptions = {
  title: '诗经',
  author: '未知',
  publisher: '某出版社',
  price: 39.0,
  type: 'poetry'
}

const myPoetry = new bookFactory(poemOptions)
myPoetry.show()
// 输出：这本书《诗经》是由未知撰写的,出版商为某出版社，定价：39元。
myPoetry.readPoem('关雎')
// 您正在阅读诗集《诗经》中的诗：关雎。

```



## 参考

- [Factory method pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)
- [Factory pattern](https://www.oodesign.com/factory-pattern.html)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#factorypatternjavascript)
- [Head First Design Patterns](https://www.goodreads.com/book/show/58128.Head_First_Design_Patterns)
- [Classes Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
