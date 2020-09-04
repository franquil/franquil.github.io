---
title: "JS内存管理与泄漏"
date: 2020-08-22T16:24:04+08:00
draft: true
slug: "js-memory-management-and-leaks"
tags: 
  - js
  - memory
---

虽然在JavaScript中，当对象创建后会自动被分配内存，且在所创建的对象不再被使用后，这些对象所占据的内存应该会被释放。但，这并不意味着不存在内存泄漏的情况出现。作为开发人员，了解JS中内存管理方式，并避免严重的内存泄漏情况，可以说是很重要的一项技能。

本篇文章主要介绍了在JS中内存管理的方式，常见的可能出现内存泄漏的情况，以及如何调试及避免内存泄漏。

## JS的内存生命周期

一般编程语言都会内存的生命周期都会包含三个阶段，在JS中也不例外，它们分别为：
1. 内存的分配；
2. 内存的使用；
3. 内存的释放。

### 内存的分配

在JS中，当对象创建时，会被自动分配内存。

```
// 当变量声明时，系统自动分配给对象内存空间
const JSMemoryObject = { foo: 'bar' };

// 分配内存给一个函数
function createAnDateObject() {
  return new Date()
}

// 当调用函数时，返回值也会给分配内存
const memoryInstance = createAnDateObject()
```

### 内存的使用

使用内存意味着读取或写入已分配的内存资源。例如：

```
// 声明字符串和函数并分配内存
const memoryValue = "内存读取"
function returnMemoryObject(str) {
  // 将所传入的内存值写入一个对象并返回
  return {
    name: 'memory',
    value: str || ''
  }
}

// 从内存中读取所声明的字符串和函数，并运行后返回所得值
const myMemoryObject = returnMemoryObject(memoryValue)

```

### 内存的释放

在JS中，当已分配的内存不再使用后，会自动触发所谓的“垃圾回收”机制。在这种内存垃圾，需要利用垃圾回收器进行自动清理。而垃圾回收器的目的是监视内存的分配，当其达到“释放”的判断标准后，再将其回收。然而，很多内存泄漏问题也就是出在这个阶段。由于这个阶段在JS中是自动实现的，而有些时候，一些内存地址是否还需要使用，或是已经无需使用的标准可能无法完全确认，这就导致了已声明的内存无法得到回收的情况出现，也就是通常所谓的内存泄漏。

## 内存中的垃圾回收

在JS中内存垃圾的判断与回收，都是按照指定规则触发和自动完成的。而垃圾回收机制的局限性，可以被认为是导致内存泄漏的“元凶”。

“标记并清除算法”(Mark-and-Sweep Algorithm)被认为在当代浏览器中广泛使用的一种内存垃圾回收机制。该机制会间隔运行垃圾回收器，首先，它会在当前全局根变量中找到并标记所有被参考的对象，和这些对象所参考的对象，并遍历所有对全局根变量有着些许“联系”（被参考）的对象。被参考说明一个对象仍在使用，或仍有着存在价值，这样垃圾回收器就不会在内存中清除这些有用的数据。

在找到并标记(mark)这些“有用”的对象后，下一步就是清除(sweep)掉所有那些没有被标记的对象，并释放这些对象所占据的内存。


{{< figure src="/images/202008/js-garbage-collection-mark-and-sweep-demo.gif" title="JS垃圾回收标记并清除算法实例" alt="JS垃圾回收标记并清除算法示例" caption="来源：blog.appsignal.com">}}

更多有关JS中的垃圾回收机制，可以[查看参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Garbage_collection)。

## 在浏览器中JS内存泄漏示例及避免

不同的JS运行环境或不同的浏览器，可能会有不同的垃圾回收机制和相对应的调试方式。下面，本文主要以Chrome浏览器为例，来演示JS在浏览器中常见内存泄漏的情况。

常见的内存泄漏有如下几种情况引起的：

### 不恰当的全局变量

全局变量是导致内存泄漏常见的一种情况。如下例所示：

```
<html>
<head>
  <title>JS内存泄漏示例</title>
  <meta content="text/html;charset=utf-8" http-equiv="Content-Type">
</head>
<body>
  <div id="global-variables-memory-leaking-example">
    <button id="start-global-variables-memory-usage">开始给全局变量分配内存</button>
    <button id="stop-global-variables-memory-usage">清理全局变量内存</button>
  </div>

<script type="text/javascript" charset="utf-8">
  // 全局变量，这里以数组为例
  let GlobalVariableMemory = [];

  document.getElementById('start-global-variables-memory-usage').onclick = function() {
    // 给全局变量数组添加数据
    for ( let i = 0; i <= 10000; i++) {
      GlobalVariableMemory.push(new Date())
    }
  };

  document.getElementById('stop-global-variables-memory-usage').onclick = function() {
    // 清理全局变量数据内存中的数据
    GlobalVariableMemory = null;
  };
</script>
</body>
</html>
```

当我们点击“开始给全局变量分配内存”按钮后，会触发给全局变量载入数据的函数。而这些载入全局变量的数据会载入当前页面的内存中，尤其是在由单页面构成的功能中，即使这些数据已经使用后，且无需再次使用时，其也会占据一定的内存。而由于其是全局变量，JS的垃圾回收器由于无法判断其是否仍会再次被利用，导致这部分所占用的内存无法自动清除。如果全局变量持续增加，将会导致浏览器占用更多系统的内存空间，降低系统运行效率。

那么，如何才能看到这些“泄漏”的内存呢？以Chrome浏览器为例，首先打开上面的示例页面，然后找到"Developer Tool"下的"Memory"选项。在点击任何按钮前，先给内存来个快照。随后，点击几下“开始给全局变量分配内存”按钮，之后，再次给内存来个快照。最后，为了清除分配给全局变量的内存，点击“清理全局变量内存”按钮，然后再次快照。

在采用上述步骤后，会得到如下示例。

{{< figure src="/images/202008/js-global-variables-memory-leaking-example-1.png" title="JS全局变量内存使用快照" alt="JS全局变量内存泄漏示例">}}


可以清晰地看到，在给全局变量分配内存后，“Snapshot 2”所占用的内存明显增加。而当清除全局变量内存使用后，"Snapshot 3"显示内存又回复到初始时的状态，已分配的内存已得到释放。

在知道导致内存泄漏的原因后，在实际开发过程中，就可以避免这种情况的出现。首先，可以尽量少使用“全局变量”来储存数据。例如，如果把上例中的全局变量`GlobalVariableMemory`改为局部/本地变量，将其嵌入到子函数中，就可以有效的避免由于全局变量导致内存泄漏的状况出现。

其次，如果使用全局变量不可避免的话，那么，就如上例所示，在使用完成且无需再次使用全局变量后，可将这个全局变量分配为无效值`null`等，或者再次分配其他需要使用的数据。


### console输出数据

类似全局变量所储存的数据会占用浏览器内存，由console.log等方式输出的数据也会占用内存，而如果没有清除的话，同样会导致内存泄漏的情况出现。

例如，采用上例中相似的方式定义两个按钮和相关联的JS函数

```
<!-- 定义HTML按钮 -->
<div id="console-memory-leaking-example">
  <button id="start-console-memory-usage">开始给console分配内存</button>
  <button id="stop-console-memory-usage">清理console内存</button>
</div>
<!-- 省略其他HTML代码 -->

<script type="text/javascript" charset="utf-8">
  // 定义会导致内存泄漏的函数
  const ConsoleMemoryLeaker = function(){
    this.addData = function() {
      const tempData = [];
      for ( let i = 0; i <= 10000; i++) {
        tempData.push(new Date())
      }
      console.log('当前数据为：', tempData);
    };
  };
  
  // 定义按钮相关联的函数
  document.getElementById('start-console-memory-usage').onclick = function() {
    // 声明一个实例并console.log出临时数据
    const consoleMemoryLeakerInstance = new ConsoleMemoryLeaker();
    consoleMemoryLeakerInstance.addData();
  };
  
  // 清除console.log输出的数据
  document.getElementById('stop-console-memory-usage').onclick = function() {
    console.clear();
  };
</script>
```

重复上面所使用的步骤，首先进行内存快照，然后点击“开始给console分配内存”按钮，再次进行内存快照。最后，在点击“清理console内存”后，对内存进行再次快照。

{{< figure src="/images/202008/js-console-memory-leaking-example-1.png" title="JS console内存使用快照" alt="JS console内存泄漏示例">}}

如上图显示，由于console.log的数据输出所占用的内存会导致内存泄漏的情况出现。当采用`console.clear()`函数对console输出数据进行清空后，之前所占用的内存也会被释放。如果将代码中的`console.log`注释掉，重复上述所进行的内存快照步骤，会发现内存中的数据并未出现变化。可知，采用console输出会出现内存泄漏，而清除console所输出的数据，会避免其引发内存泄漏。

### 被遗忘的定时器(intervel)

在JS中比较常见的由'setIntervel'或是'setTimeout'设定的定时器，如果未能及时清除，也可能会导致内存泄漏的情况。如下例所示。

```
<!-- 定义HTML按钮 -->
  <div id="timer-memory-leaking-example">
    <button id="start-timer-memory-usage">开始timer分配内存</button>
    <button id="stop-timer-memory-usage">清理timer内存</button>
  </div>
<!-- 省略其他HTML代码 -->

<script type="text/javascript" charset="utf-8">
  // 定义可能导致计时器内存泄漏函数
  const TimerMemoryLeaker = function() {
    this.intervel = null;
    this.data = [];

    this.addData = function() {
      for ( let i = 0; i <= 10000; i++) {
        this.data.push(new Date())
      }
    };

    this.start = function() {
      const self = this;
      self.addData()
      this.intervel = setInterval(function() {
        self.runIntervel();
      }, 500);
    };

    this.runIntervel = function() {
      console.log('run intervel action');
    };

    this.stop = function() {
      if (this.intervel !== null) {
        clearInterval(this.intervel);
      }
    };
  };

  let timerMemoryLeakerInstance;

  document.getElementById('start-timer-memory-usage').onclick = function() {
    // 声明一个实例并调用函数
    timerMemoryLeakerInstance = new TimerMemoryLeaker();
    timerMemoryLeakerInstance.start();
  };

  document.getElementById('stop-timer-memory-usage').onclick = function() {
    // 停止执行函数并无效化已声明的实例
    timerMemoryLeakerInstance.stop();
    timerMemoryLeakerInstance = null;
  };

</script>

```

如上所示，若所定义的`timerMemoryLeakerInstance`没有清除所使用的计时器的话，即使无效化该变量，也可能会导致触发的计时器持续运行，而导致内存泄漏。即使随着浏览器垃圾回收机制的不断演进，由`setTimeout`所设定的计时器会在出发后自动回收，但采用相对应的清除函数`clearInterval`或`clearTimeout`扔可被认为是一种很好的开发实践。

在本例中，若要避免由定时器引发的内存泄漏，需在无效化实例前，先清除所定义的定时器。否则，通过内存快照就会发现，实例的内存不会被释放。

{{< figure src="/images/202008/js-timer-memory-leaking-example-1.png" title="JS timer内存使用快照" alt="JS timer内存泄漏示例">}}

当调用`clearIntervel`清除掉所触发的定时器后，会看到计时器实例所占用的内存已被释放。

### 无用的文档模型(DOM)参考

通过JS创建的页面节点元素，在使用过后，如果不及时清除原数据的话，会导致所占用的内存泄漏。例如下例所示。

```
  // ---------------------------------- DOM导致内存泄漏示例

  // 创建一个DOM节点元素，用于储存数据
  let domElement = document.createElement('p')
  domElement.id = 'dom-element-memory-leaker';

  const DomMemoryLeaker = function() {
    this.data = [];

    this.addData = function() {
      for ( let i = 0; i <= 10000; i++) {
        this.data.push(new Date())
      }
    };

    this.start = function(dom) {
      // 将数据添加至指定元素，并于页面中显示
      this.addData();
      dom.innerText = this.data.join(',')
      document.body.appendChild(dom);
    };

    this.stop = function(dom) {
      // 移除指定dom
      document.body.removeChild(dom);
    };
  };

  let domMemoryLeakerInstance;

  document.getElementById('start-dom-memory-usage').onclick = function() {
    // 声明一个DOM内存泄漏实例，并调用函数获取及显示数据
    domMemoryLeakerInstance = new DomMemoryLeaker();
    domMemoryLeakerInstance.start(domElement);
  };

  document.getElementById('stop-dom-memory-usage').onclick = function() {
    // 移除DOM节点元素，并无效化已声明的DOM内存泄漏实例
    domMemoryLeakerInstance.stop(domElement);
    domMemoryLeakerInstance = null;
    domElement = null;
  };
```

按照上面示例的步骤，进行内存快照，会看到如下图所示。

{{< figure src="/images/202008/js-dom-memory-leaking-example-1.png" title="JS 无用dom参考内存使用快照" alt="JS dom内存泄漏示例">}}

若将`domMemoryLeakerInstance = null`语句移除的话，会发现即使页面元素'dom-element-memory-leaker'已经从页面文档中移除的话，而'domMemoryLeakerInstance'变量所占用的内存也不会被释放，这就导致了泄漏情况的出现。为了避免泄漏，在数据已经加载如DOM后，无用的在JS中储存的原数据应该被移除。此外，已生成的DOM节点本身，也可能会占用内存，进而引发泄漏情况。一种解决方式就是，将JS所创建的节点声明为本地变量，而非全局变量，如在上例中将其包含在`DomMemoryLeaker`里。当节点不在使用后，无效化所声明的实例`domMemoryLeakerInstance`便可以释放所占用的内存。

### 事件监听器（Event Listener）

在一些老式的浏览器中，如果设定的event listener没有及时清除的话，可能会导致内存泄漏的情况。但现代的很多浏览器的垃圾回收机制都可以识别无效的event listener，并释放内存。然而，手动清理所声明的event listener被视为一种好的开发实践，可以避免内存泄漏，或导致不必要的交互事件出现。

例如下例所示：

```
  // ---------------------------------- EventListener导致内存泄漏示例

  function handleClick(n) {
    console.log('clicked')
  }

  const eventElement = document.getElementById('start-event-listener-memory-usage');
  // 添加事件监听器
  eventElement.addEventListener('click', handleClick);

  document.getElementById('stop-event-listener-memory-usage').onclick = function() {
    // 清除事件监听器
    eventElement.removeEventListener('click', handleClick);
  };
```

如果没有比较大的数据被调用的话，也许并不会带来明显的内存占用；若调用函数中存在较为复杂的逻辑和较大的对象参考，那么，将可能会带来严重的内存泄漏事件。

### 闭包(Closures)

在一个closure中，可能会出现对较大对象的参考，即使这些对象的一些函数或是数据可能在当下并无用途，但也会占用内存。例如下例所示。

```
  // ---------------------------------- closures导致内存泄漏示例
 
  const ClosuresMemoryLeaker = function() {
    this.data = [];

    this.addData = function() {
      for (let i = 0; i <= 10000; i++) {
        this.data.push(new Date());
      }
    };

    this.addData();
    
    this.unusedMethod = function() {
      console.log('data=', this.data);
    }

    this.start = function() {
      return this
    };
  };

  let closuresMemoryLeakerInstance;

  document.getElementById('start-closures-memory-usage').onclick = function() {
    // 声明一个clousure内存泄漏实例，并调用函数
    closuresMemoryLeakerInstance = new ClosuresMemoryLeaker().start();
  };

  document.getElementById('stop-closures-memory-usage').onclick = function() {
    // 无效化已声明的内存泄漏实例
    closuresMemoryLeakerInstance = null;
  };
```

对内存进行开始前后快照进行对比，会得到类似的情况，如下图。

{{< figure src="/images/202008/js-closures-memory-leaking-example-1.png" title="JS closures内存使用快照" alt="JS closures内存泄漏示例">}}

一个子函数closure被调用的同时，其外部语法环境的数据和函数即使没有被使用，但也可能导致内存泄漏。

## 结尾  

在了解了浏览器所采用的内存管理，垃圾回收机制与“标记和清除算法”的原理后，在实际JS开发中，就可以采取一定的措施来促使内存被回收，避免泄漏的情况出现。应该注意的事项有：

- 尽量避免使用全局变量，尤其是较大的对象；如无法避免，在使用后应该予以清除，以释放所占用的内存；
- 及时移除所设定的计时器(timer)和事件监听器（event listener）;
- 在使用过后，及时清除JS中创建的DOM节点元素；
- 在嵌套的closure环境中， 留意循环参考，特别是占用较大内存较大的对象，在使用过后及时清理；
- 尽量避免对同一个对象的多处参考和引用，如无法避免，在使用后记得清除；

等等。

随着JS运行环境的不断演进，垃圾回收机制已经可以及时清除一些常见的错误导致的内存泄漏；然而，如果不留意的话，仍可能会导致严重的内存泄漏事件，导致程序运行迟缓或崩溃等问题，尤其是在频繁处理较大的象时。


## 参考与延伸阅读

- [Memory Management - Javascript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)
- [Understand memory leaks in Javascript applications](https://www.ibm.com/developerworks/library/wa-jsmemory/index.html)
- [Eridicating Memory Leaks in Javascript | LambdaTest](https://www.lambdatest.com/blog/eradicating-memory-leaks-in-javascript/)
- [4 Types of Memory Leaks in Javascript and How to Get Rid of Them](https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/)
- [Fix Memory Problems | Chrome DevTools | Google Developers](https://developers.google.com/web/tools/chrome-devtools/memory-problems/)
- [Avoiding Memory Leaks in Node.js: Best Practices for Performance](https://blog.appsignal.com/2020/05/06/avoiding-memory-leaks-in-nodejs-best-practices-for-performance.html)
