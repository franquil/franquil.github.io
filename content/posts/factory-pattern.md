+++
title = "工厂模式的介绍和示例"
date = 2020-08-07T08:48:38+08:00
tags = ["design patterns"]
draft = true
slug = "factory-pattern-introduction-and-examples"
summary = "工厂模式是一种在面向对象编程中用于创造对象的模式，本文简要介绍了该模式，并用示例了该模式的实现方式。"
+++

工厂模式(Factory Pattern)属于一种创造型模式，其根本目的在于创建一个对象。然而，不同于[构建器模式]({{< ref "/posts/constructor-pattern.md" >}})，工厂本身不会直接创建对象，而是会根据用户所传入的参数来调用构建器，从而实现创建对象的功能。


假设当前用户需要创建一个图书对象，虽然图书对象可能包含不同的种类（如：画册、词典、诗集、小说等等），但它们都可能分享相同的图书对象方法（如：上架、运输、销售等），为了避免分别为不同种类图书分别创建构建器等重复工作，可以采用一个“工厂”用来管理这些不同的图书对象构建器，这样用户只需指定类别，工厂就会根据用户的需求创建一个所指定的图书对象。

## 工厂模式图示

工厂模式可以用下图来进行


## 采用JS进行示例




<br/>

## 参考

- [Factory method pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)
- [Factory pattern](https://www.oodesign.com/factory-pattern.html)
- [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#factorypatternjavascript)
- [Head First Design Patterns](https://www.goodreads.com/book/show/58128.Head_First_Design_Patterns)
