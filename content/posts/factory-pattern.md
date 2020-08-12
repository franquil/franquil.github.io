+++
title = "工厂模式的介绍和示例"
date = 2020-08-07T08:48:38+08:00
tags = ["design patterns", "js"]
draft = true
slug = "factory-pattern-introduction-and-examples"
summary = "工厂模式是一种在面向对象编程中用于创造对象的模式，本文简要介绍了该模式，并简要地展示了该模式的实现方式。"
+++

工厂模式(Factory Pattern)属于一种创造型模式，其根本目的在于创建一个对象。然而，不同于[构建器模式]({{< ref "/posts/constructor-pattern.md" >}})，工厂本身不会直接创建对象，而是会根据用户所传入的参数，通过工厂方法（函数）来调用构建器，从而实现创建对象的功能。根据具体实现方式的不同，该模式有时也被细分为工厂方法模式（Factory Method Pattern）和抽象工厂模式（Abstract Factory Pattern）。


假设当前用户需要创建一个图书对象，虽然图书对象可能包含不同的种类（如：词典、诗集、小说等等），但它们都可能分享相同的图书对象方法（如：阅读、销售、上架等）或属性，为了避免分别为不同种类图书分别创建构建器等重复工作，可以采用一个“工厂”用来管理这些不同的图书对象构建器，这样用户只需指定类别，工厂就会根据用户的需求创建一个所指定的图书对象。

## 目的

工厂模式的目的在于创建一个灵活及可重复使用的方式，将对象构建的方式从对象本身分隔开，以方便实现、修改、测试和管理等。

## 何时使用工厂模式

- 当对象实例的创建过程过于复杂，需要将其与对象本身分离开来降低复杂度时；
- 当不同的对象实例需要根据不同参数来进行创建时；
- 当所需创建的不同的对象实例需要共享属性或方法时；
- 当需要采用同一个方式来新建不同但相似的对象时；

等等。

## 采用JS进行示例

有ES5的支持，可以采用'class'方式来进行构建。

```
// 构建一个可共享的基本图书类
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

// 构建一个类用于实例化具体'book'类的工厂
class BookFactory {
  constructor() {
    // 记录工厂中所涵盖的'book'类
    this.registeredBooks = {
      "default": Book,
      "dictionary": Dictionary,
      "poetry": Poetry
    }
  }

  // 用于创建'book'类的工厂函数
  createBook(optionsObject) {
    return new this.registeredBooks[optionsObject.type](optionsObject)
  }
}

// 设定'dictionary'类所需参数
const dictOptions = {
  title: '设计模式',
  author: '大国',
  publisher: '某出版社',
  price: 69.0,
  type: 'dictionary'
}

// 实例化一个'dictionary'类
const myBookFactory = new BookFactory()
const myDictionary = myBookFactory.createBook(dictOptions)
myDictionary.show()
// 输出：这本书《设计模式》是由大国撰写的,出版商为某出版社，定价：69元。
myDictionary.searchWord('工厂')
// 输出：您正在使用词典《设计模式》查找工厂...

// 设定'poetry'类所需参数
const poemOptions = {
  title: '诗经',
  author: '未知',
  publisher: '某出版社',
  price: 39.0,
  type: 'poetry'
}

// 实例化'poetry'类
const myPoetry = myBookFactory.createBook(poemOptions)
myPoetry.show()
// 输出：这本书《诗经》是由未知撰写的,出版商为某出版社，定价：39元。
myPoetry.readPoem('关雎')
// 您正在阅读诗集《诗经》中的诗：关雎。

```

## 工厂模式图示

以上面所提到的图书类为例，工厂模式简要地可以用下图来表示。

![工厂模式](/images/factory-pattern.jpg "工厂模式(Factory Pattern)")


## 抽象工厂模式（Abstract Factory Pattern）

除去上面展示的工厂模式外，若当前有多个相似的工厂需要构建，可以采用抽象工厂模式。该模式可以视为构建工厂的工厂，用于将不同工厂集成在一起。

假设除上面提到的'BookFactory'外，现新加入了一个以音频书为出发点的'AudioBookFactory'，那么，就可以通过以下方式创建一个构建工厂('BookFactory'或'AudioBookFactory')的工厂。

```
class BookFactory {
  // 沿用之前所创建的类...此处省略
}

class AudioBookFactory {
  // 采用'BookFactory'同样的方式创建本类...此处省略
}

class AbstractFactory {
  constructor() {
    // 注册可用的工厂
    this.registeredFactories = {
      'BookFactory': BookFactory,
      'AudioBookFactory': AudioBookFactory
    }
  }

  // 返回指定的工厂
  getFactory(factoryType) {
    return new this.registeredFactories[factoryType]()      
  }
}

const myAbstractFactory = new AbstractFactory()
const myNewFactory = myAbstractFactory.getFactory('BookFactory')
// 此处的'myNewFactory'为'BookFactory'的实例

```

## 结尾

工厂模式可以方便用户采用同一种方式创建类似的对象，然而如果对象差异过大，或不同对象创建过程过于繁琐，该方式可能会带来大量多余的工作，也可能会给测试带来负担，或其他不利的元素。在应用过程中，应慎重考虑该模式的利弊，以避免不必要的工作。

---

## 参考

- [Factory method pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)
- [JavasScript Design Patterns: The Factory pattern](https://medium.com/javascript-in-plain-english/javascript-design-patterns-the-factory-pattern-6b399656d710)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#factorypatternjavascript)
- [Head First Design Patterns](https://www.goodreads.com/book/show/58128.Head_First_Design_Patterns)
- [Classes Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- [Design Patterns: Factory vs Factory method vs Abstract Factory](https://stackoverflow.com/questions/13029261/design-patterns-factory-vs-factory-method-vs-abstract-factory)
