---
title: 前端部分知识
date: 2019-07-23 16:29:00
categories:
- 前端
tags:
- Web
---

部分前端技术做个汇总~~~~

<!-- more-->

# 一、JavaScript基础

一个完整的JavaScript 实现应该由下列三
个不同的部分组成（见图1-1）。
q 核心（ECMAScript）
q 文档对象模型（DOM）
q 浏览器对象模型（BOM）

## 1、声明提前类问题

```
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

// 请写出以下输出结果：
Foo.getName();
getName(); // 声明提前
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
复制代码
```

答案是：2、4、1、1、2、3、3。

这里考察声明提前的题目在代码中已经标出，这里声明getName方法的两个语句：

```
var getName = function () { alert (4) };
function getName() { alert (5) }
复制代码
```

实际上在解析的时候是这样的顺序：

```
function getName() { alert (5) }
var getName;
getName = function () { alert (4) };
复制代码
```

如果我们在代码中间再加两个断点：

```
getName(); // 5
var getName = function () { alert (4) };
getName(); // 4
function getName() { alert (5) }
复制代码
```

在第一次getName时，function的声明和var的声明都被提前到了第一次getName的前面，而getName的赋值操作并不会提前，单纯使用var的声明也不会覆盖function所定义的变量，因此第一次getName输出的是function声明的5； 而第二次getName则是发生在赋值语句的后面，因此输出的结果是4，所以实际代码的执行顺序是这样：

```
function getName() { alert (5) }
var getName;
getName(); // 5
getName = function () { alert (4) };
getName(); // 4
复制代码
```

## 2、浏览器存储

### localStorage，sessionStorage和cookie的区别

共同点：都是保存在浏览器端、仅同源可用的存储方式

1. 数据存储方面

- cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下
- sessionStorage和localStorage不会自动把数据发送给服务器，仅在本地保存。

1. 存储数据大小

- 存储大小限制也不同，cookie数据不能超过4K，同时因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识。
- sessionStorage和localStorage虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大

1. 数据存储有效期

- sessionStorage：仅在当前浏览器窗口关闭之前有效；
- localStorage：始终有效，窗口或浏览器关闭也一直保存，本地存储，因此用作持久数据；
- cookie：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭

1. 作用域不同

- sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；
- localstorage在所有同源窗口中都是共享的；也就是说只要浏览器不关闭，数据仍然存在
- cookie: 也是在所有同源窗口中都是共享的.也就是说只要浏览器不关闭，数据仍然存在

## 3、跨域

对同源策略及各种跨域的方式进行了总结：[什么是跨域，为什么浏览器会禁止跨域，及其引起的发散性学习](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fqq_35271556%2Farticle%2Fdetails%2F80340102)

## 4、Promise的使用及原理

Promise是ES6加入的新特性，用于更合理的解决异步编程问题，关于用法阮一峰老师在[ECMAScript 6 入门](https://link.juejin.im?target=http%3A%2F%2Fes6.ruanyifeng.com%2F%23docs%2Fpromise)中作出了详细的说明。

[让你彻底明白Promise原理](https://link.juejin.im?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000009478377)

这篇文章对Promise的原理进行的详细的说明，提取最简单的Promise实现方式来对Promise的原理进行说明：

```
function Promise(fn) {
    var value = null,
        callbacks = [];  // callbacks为数组，因为可能同时有很多个回调

    this.then = function (onFulfilled) {
        callbacks.push(onFulfilled);
    };

    function resolve(value) {
        callbacks.forEach(function (callback) {
            callback(value);
        });
    }

    fn(resolve);
}
```

首先，`then`里面声明的单个或多个函数，将被推入`callbacks`列表，在Promise实例调用`resolve`方法时遍历调用，并传入`resolve`方法中传入的参数值。

以下，使用一个简单的例子来对Promise的执行流程进行分析：

```
functionm func () {
	return new Promise(function (resolve) {
		setTimeout(function () {
			resolve('complete')
		}, 3000);
	})
}

func().then(function (res) {
	console.log(res); // complete
})
```

`func`函数的定义是返回了一个Promise实例，声明实例时传入的回调函数加入了一个`resolve`参数（这个`resolve`参数在Promise中的`fn(resolve)`定义中获取`resolve`的函数实体），回调中执行了一个异步操作，在异步操作完成的回调中执行了`resolve`函数。

再看执行步骤，`func`函数返回了一个Promise实例，实例则可以执行Promise构造函数中定义的`then`方法，`then`方法中传入的回调则会在`resolve`（即异步操作完成后）执行，由此实现了通过`then`方法执行异步操作完成后回调的功能。

## 5、JavaScript事件循环机制

原文中贴出的文章具有很大参考价值，链接：[详解JavaScript中的Event Loop（事件循环）机制](https://link.juejin.im?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F33058983)。

JavaScript是一种单线程、非阻塞的语言，这是由于它当初的设计就是用于和浏览器交互的：

- 单线程：`JavaScript`设计为单线程的原因是，最开始它最大的作用就是和`DOM`进行交互，试想一下，如果`JavaScript`是多线程的，那么当两个线程同时对`DOM`进行一项操作，例如一个向其添加事件，而另一个删除了这个`DOM`，此时该如何处理呢？因此，为了保证不会 发生类似于这个例子中的情景，`JavaScript`选择只用一个主线程来执行代码，这样就保证了程序执行的一致性。
- 非阻塞：当代码需要进行一项异步任务（无法立刻返回结果，需要花一定时间才能返回的任务，如I/O事件）的时候，主线程会挂起（`pending`）这个任务，然后在异步任务返回结果的时候再根据一定规则去执行相应的回调。而`JavaScript`实现异步操作的方法就是使用Event Loop。

```
setTimeout(function () {
    console.log(1);
});

new Promise(function(resolve,reject){
    console.log(2)
    resolve(3)
}).then(function(val){
    console.log(val);
})
```

下面通过一段代码来分析这个问题，首先`setTimeout`和`Promise`中的`then`回调都是异步方法，而`new Promise`则是一个同步操作，所以这段代码应该首先会立即输出`2`；`JavaScript`将异步方法分为了`marco task`（宏任务：包括`setTimeout`和`setInterval`等）和`micro task`（微任务：包括`new Promise`等），在`JavaScript`的执行栈中，如果同时存在到期的宏任务和微任务，则会将微任务先全部执行，再执行第一个宏任务，因此，两个异步操作中`then`的回调会率先执行，然后才执行`setTimeout`的回调，因此会依次输出3、1，所以最终输出的结果就是2、3、1。

## 6、ES6作用域及let和var的区别

阮一峰老师在[ECMAScript 6 入门](https://link.juejin.im?target=http%3A%2F%2Fes6.ruanyifeng.com%2F%23docs%2Flet)中的`let 和 const 命令`章节对这个问题作出了详细的说明，下面提取一些我认为关键的点进行讲解。

ES6引入了使用`{}`包裹的代码区域作为块级作用域的声明方式，其效果与ES5中`function`声明的函数所生成的函数作用域具有相同的效果，作用域外部不能访问作用域内部声明的函数或变量，这样的声明在ES6中对于包括`for () {}`、`if () {}`等大括号包裹的代码块中都会生效，生成一个单独的作用域。

ES6新增的`let`声明变量的方式相比`var`具有以下几个重要特点：

- `let`声明的变量只在作用域内有效，如下方代码，`if`声明生成了一个块级作用域，在这个作用域内声明的变量在作用域外部无法访问，假如访问会产生错误：

```
if (true) {
	let me = 'handsome boy';
}
console.log(me); // ReferenceError
```

- `let`声明的变量与`var`不同，不会产生变量提升，如下方代码，在声明之前输出代码，会产生错误：

```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;
// let 的情况
console.log(bar); // ReferenceError
let bar = 2;
```

- `let`的声明方式不允许重复声明，如重复声明会报错，而`var`声明变量时，后声明的语句会对先声明的语句进行覆盖：

```
// 报错
function func() {
  let a = 10;
  var a = 1;
}
// 报错
function func() {
  let a = 10;
  let a = 1;
}
```

- 只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（`binding`）这个区域，不再受外部的影响，这个特性称为`暂时性死区`。

```
var tmp = 123;
if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

## 7、闭包

待补充

## 8、原型及原型链

待补充

## 9、浏览器的回流与重绘 (Reflow & Repaint)

参考：[juejin.im/post/5a9923…](https://juejin.im/post/5a9923e9518825558251c96a)

浏览器在接收到 `html` 与 `css` 后，渲染的步骤是：`html` 经过渲染生成 `DOM` 树， `css` 经过渲染生成 `css` 渲染树，两者再经过结合，生成 `render tree`，浏览器就可以根据 `render tree` 进行画面绘制。

如果浏览器从服务器接收到了新的 `css` ，需要更新页面时，需要经过什么操作呢？这就是回流 `reflow` 与重绘 `repaint`。由于浏览器在重新渲染页面时会先进行 `reflow` 再进行 `repaint`，因此，回流必将引起重绘，而重绘不一定会引起回流。

重绘：当前元素的样式(背景颜色、字体颜色等)发生改变的时候，我们只需要把改变的元素重新的渲染一下即可，重绘对浏览器的性能影响较小。发生重绘的情形：改变容器的外观风格等，比如 `background：black` 等。改变外观，不改变布局，不影响其他的 `DOM`。    回流：是指浏览器为了重新渲染部分或者全部的文档而重新计算文档中元素的位置和几何构造的过程。

因为回流可能导致整个 `DOM` 树的重新构造，所以是性能的一大杀手，一个元素的回流导致了其所有子元素以及 `DOM` 中紧随其后的祖先元素的随后的回流。下面贴出会触发浏览器 `reflow` 的变化：

- 页面首次渲染
- 浏览器窗口大小发生改变
- 元素尺寸或位置发生改变
- 元素内容变化（文字数量或图片大小等等）
- 元素字体大小变化
- 添加或者删除可见的DOM元素
- 激活CSS伪类（例如：:hover）
- 查询某些属性或调用某些方法

### 优化方案：

#### CSS

- 避免使用`table`布局。
- 尽可能在`DOM`树的最末端改变`class`。
- 避免设置多层内联样式。
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上。
- 避免使用`CSS`表达式（例如：`calc()`）。

#### JavaScript

- 避免频繁操作样式，最好一次性重写`style`属性，或者将样式列表定义为`class`并一次性更改`class`属性。
- 避免频繁操作`DOM`，创建一个`documentFragment`，在它上面应用所有`DOM`操作，最后再把它添加到文档中。
- 也可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在`display`属性为`none`的元素上进行的`DOM`操作不会引发回流和重绘。
- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
- 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。

## 10、JS对象的深复制

一般的思路就是递归解决，对不同的数据类型做不同的处理：

```
function deepCopy (obj) {
  let result = {}
  for (let key in obj) {
    if (obj[key] instanceof Object || obj[key] instanceof Array) {
      result[key] = deepCopy(obj[key])
    } else {
      result[key] = obj[key]
    }
  }
  return result
}
```

这个只能复制内部有数组、对象或其他基础数据类型的对象，假如有一些像`RegExp`、`Date`这样的复杂对象复制的结果就是一个`{}`，无法正确进行复制，因为没有对这些特殊对象进行单独的处理。若要参考对复杂对象进行复制，可以参考`lodash`中数组深复制方法`_.cloneDeep()`的实现方案，下面这篇文章对数组深复制的方法进行了详细的解析，有一定参考价值： [jerryzou.com/posts/dive-…](https://link.juejin.im?target=http%3A%2F%2Fjerryzou.com%2Fposts%2Fdive-into-deep-clone-in-javascript%2F)

另外如果要复制的对象数据结构较为简单，没有复杂对象的数据，那么可以用最简便的方法：

```
let cloneResult = JSON.parse(JSON.stringify(targetObj))
```

## 11、JS运算精度丢失

此前转载了一篇文章，对JavaScript运算精度丢失的原因及解决方案都有比较详细的说明： [blog.csdn.net/qq_35271556…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fqq_35271556%2Farticle%2Fdetails%2F80137474)

## 12、defer 和async

无论如何包含代码，只要不存在defer 和async 属性，浏览器都会按照<script>元素在页面中出现的先后顺序对它们依次进行解析。换句话说，在第一个<script>元素包含的代码解析完成后，第二个<script>包含的代码才会被解析，然后才是第三个、第四个……

按照传统的做法，所有<script>元素都应该放在页面的<head>元素中, 这种做法的目的就是把所有外部文件（包括CSS 文件和JavaScript 文件）的引用都放在相同的地方。可是，在文档的<head>元素中包含所有JavaScript 文件，意味着必须等到全部JavaScript 代码都被下载、解析和执行完成以后，才能开始呈现页面的内容（浏览器在遇到<body>标签时才开始呈现内容）。对于那些需要很多JavaScript 代码的页面来说，这无疑会导致浏览器在呈现页面时出现明显的延迟，而延迟期间的浏览器窗口中将是一片空白。为了避免这个问题，现代Web 应用程序一般都把全部JavaScript 引用放在<body>元素中页面内容的后面.

defer 属性只适用于外部脚本文件。这一点在HTML5 中已经明确规定，因此支持HTML5 的实现会忽略给嵌入脚本设置的defer 属性。同样与defer 类似，async 只适用于外部脚本文件，并告诉浏览器立即下载文件。但与defer不同的是，标记为async 的脚本并不保证按照指定它们的先后顺序执行。
异步脚本一定会在页面的load 事件前执行，但可能会在DOMContentLoaded 事件触发之前或之后执行。

## 13、垃圾收集方式

JavaScript 中最常用的垃圾收集方式是标记清除（mark-and-sweep）。另一种不太常见的垃圾收集策略叫做引用计数（reference counting）

## 14、JS函数类型

JS中函数有两种类型，具名函数（命名函数）和匿名函数。
创建方式有：1. 声明函数  2.创建匿名函数表达式  3.创建具名函数表达式  4.Function构造函数  5.自执行函数  6.其他创建函数的方法



# 二、ECMAScript

ECMAScript 中有5 种简单数据类型（也称为基本数据类型）：Undefined、Null、Boolean、Number
和String。还有1 种复杂数据类型——Object，Object 本质上是由一组无序的名值对组成的。ECMAScript
不支持任何创建自定义类型的机制，而所有值最终都将是上述6 种数据类型之一。

即便未初始化的变量会自动被赋予undefined 值，但显式地初始化变量依然是
明智的选择。如果能够做到这一点，那么当typeof 操作符返回"undefined"值时，
我们就知道被检测的变量还没有被声明，而不是尚未初始化。

undefined 值是派生自null 值的，因此ECMA-262 规定对它们的相等性测试要返回true：
alert(null == undefined); //true

var n=null; typeof(n) ->object
typeof检测正则表达式时，Safari 5以及之前版本和Chrome 7及之前版本中这个操作符返回"function", 在IE和Firefox中返回"Object"

在局部作用域中定义的变量可以在局部环境中与全局变量互换使用
内部环境可以通过作用域链访问所有的外部环境，但外部环境不能访问内部环境中的任何变量和函数。
var color = "blue";
function changeColor(){
var anotherColor = "red";
function swapColors(){
var tempColor = anotherColor;
anotherColor = color;
color = tempColor;
// 这里可以访问color、anotherColor 和tempColor
}
// 这里可以访问color 和anotherColor，但不能访问tempColor
swapColors();
}
// 这里只能访问color
changeColor();

JavaScript 中最常用的垃圾收集方式是标记清除（mark-and-sweep）。另一种不太常见的垃圾收集策略叫做引用计数（reference counting）

数组中已经存在两个可以直接用来重排序的方法：reverse()和sort()。在默认情况下，sort()方法按升序排列数组项

ECMAScript 5 还新增了两个归并数组的方法：reduce()和reduceRight()。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。其中，reduce()方法从数组的第一项开始，逐个遍历到最后。而reduceRight()则从数组的最后一项开始，向前遍历到第一项。

var stringValue = "hello world";
alert(stringValue.slice(3)); //"lo world"
alert(stringValue.substring(3)); //"lo world"
alert(stringValue.substr(3)); //"lo world"
alert(stringValue.slice(3, 7)); //"lo w"
alert(stringValue.substring(3,7)); //"lo w"
alert(stringValue.substr(3, 7)); //"lo worl"

indexOf()  lastIndexOf()
ECMAScript 5 为所有字符串定义了trim()方法。这个方法会创建一个字符串的副本，删除前置及
后缀的所有空格，然后返回结果

sayHi(); //错误：函数还不存在 
var sayHi = function(){   //这种情况下创建的函数叫做匿名函数
alert("Hi!");
};

arguments.callee 是一个指向正在执行的函数的指针。

function factorial(num){
if (num <= 1){
return 1;
} else {
return num * factorial(num-1);
}
}

function factorial(num){   // 在严格模式下，不能通过脚本访问arguments.callee，访问这个属性会导致错误。
if (num <= 1){
return 1;
} else {
return num * arguments.callee(num-1);
}
}

var factorial = (function f(num){  //这种方式在严格模式和非严格模式下都行得通。
if (num <= 1){
return 1;
} else {
return num * f(num-1);
}
});



# 三、浏览器相关

## 1、浏览器从加载到渲染的过程

大致可以分为如下7步：

1. 输入网址；
2. 发送到DNS服务器，并获取域名对应的web服务器对应的ip地址；
3. 与web服务器建立TCP连接；
4. 浏览器向web服务器发送http请求；
5. web服务器响应请求，并返回指定url的数据（或错误信息，或重定向的新的url地址）；
6. 浏览器下载web服务器返回的数据及解析html源文件；
7. 生成DOM树，解析css和js，渲染页面，直至显示完成；

### 加载过程：

- 浏览器根据 DNS 服务器解析得到域名的 IP 地址
- 向这个 IP 的机器发送 HTTP 请求
- 服务器收到、处理并返回 HTTP 请求
- 浏览器得到返回内容

### 渲染过程：

- 根据 HTML 结构生成 DOM 树
- 根据 CSS 生成 CSSOM
- 将 DOM 和 CSSOM 整合形成 RenderTree
- 根据 RenderTree 开始渲染和展示
- 遇到`<script>`时，会执行并阻塞渲染

## 2、浏览器缓存机制

参考文章：[segmentfault.com/a/119000001…](https://link.juejin.im?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000011212929)

## 3、性能优化

参考文章：[blog.csdn.net/na_sama/art…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fna_sama%2Farticle%2Fdetails%2F51291798)

1. 减少 HTTP 请求数量

在浏览器与服务器进行通信时，主要是通过 HTTP 进行通信。浏览器与服务器需要经过三次握手，每次握手需要花费大量时间。而且不同浏览器对资源文件并发请求数量有限（[不同浏览器允许并发数](http://www.stevesouders.com/blog/2008/03/20/roundup-on-parallel-connections/)），一旦 HTTP 请求数量达到一定数量，资源请求就存在等待状态，这是很致命的，因此减少 HTTP 的请求数量可以很大程度上对网站性能进行优化。

- CSS Sprites：国内俗称 CSS 精灵，这是将多张图片合并成一张图片达到减少 HTTP 请求的一种解决方案，可以通过 CSS background 属性来访问图片内容。这种方案同时还可以减少图片总字节数。
- 合并 CSS 和 JS 文件：现在前端有很多工程化打包工具，如：grunt、gulp、webpack等。为了减少 HTTP 请求数量，可以通过这些工具再发布前将多个 CSS 或者 多个 JS 合并成一个文件。
- 采用 lazyLoad：俗称懒加载，可以控制网页上的内容在一开始无需加载，不需要发请求，等到用户操作真正需要的时候立即加载出内容。这样就控制了网页资源一次性请求数量。

1. 控制资源文件加载优先级

浏览器在加载 HTML 内容时，是将 HTML 内容从上至下依次解析，解析到 link 或者 script 标签就会加载 href 或者 src 对应链接内容，为了第一时间展示页面给用户，就需要将 CSS 提前加载，不要受 JS 加载影响。
一般情况下都是 CSS 在头部，JS 在底部。

1. 利用浏览器缓存
   浏览器缓存是将网络资源存储在本地，等待下次请求该资源时，如果资源已经存在就不需要到服务器重新请求该资源，直接在本地读取该资源。
2. 减少重排（Reflow）
   基本原理：重排是 DOM 的变化影响到了元素的几何属性（宽和高），浏览器会重新计算元素的几何属性，会使渲染树中受到影响的部分失效，浏览器会验证 DOM 树上的所有其它结点的 visibility 属性，这也是 Reflow 低效的原因。如果 Reflow 的过于频繁，CPU 使用率就会急剧上升。

减少 Reflow，如果需要在 DOM 操作时添加样式，尽量使用 增加 class 属性，而不是通过 style 操作样式。

1. 减少 DOM 操作

2. 图标使用 IconFont 替换



## 4、跨域

- 同源策略

同源策略是浏览器有一个很重要的概念。所谓同源是指，域名，协议，端口相同。不同源的客户端脚本(javascript、ActionScript)在没明确授权的情况下，不能读写对方的资源。简单的来说，浏览器允许包含在页面A的脚本访问第二个页面B的数据资源，这一切是建立在A和B页面是同源的基础上。

- 跨域的几种方式

jsonp（利用script标签的跨域能力）跨域、websocket（html5的新特性，是一种新协议）跨域、设置代理服务器（由服务器替我们向不同源的服务器请求数据）、CORS（跨源资源共享，cross origin resource sharing）、iframe跨域、postMessage(包含iframe的页面向iframe传递消息)




# 四、AngularJs

## 1、factory、service 和 provider

- **factory**

把 service 的方法和数据放在一个对象里，并返回这个对象

```
app.factory('FooService', function(){
    return {
        target: 'factory',
        sayHello: function(){
            return 'hello ' + this.target;
        }
    }
});
```

- **service**

通过构造函数方式创建 service，返回一个实例化对象

```
app.service('FooService', function(){
    var self = this;
    this.target = 'service';
    this.sayHello = function(){
        return 'hello ' + self.target;
    }
});
```

- **provider**

创建一个可通过 config 配置的 service，$get 中返回的，就是用 factory 创建 service 的内容

```
app.provider('FooService', function(){
    this.configData = 'init data';
    this.setConfigData = function(data){
        if(data){
            this.configData = data;
        }
    }
    this.$get = function(){
        var self = this;
        return {
            target: 'provider',
            sayHello: function(){
                return self.configData + ' hello ' + this.target;
            }
        }
    }
});

// 此处注入的是 FooService 的 provider
app.config(function(FooServiceProvider){
    FooServiceProvider.setConfigData('config data');
});
```

从底层实现上来看，service 调用了 factory，返回其实例；factory 调用了 provider，返回其 `$get` 中定义的内容。factory 和 service 功能类似，只不过 factory 是普通 function，可以返回任何东西（return 的都可以被访问）；service 是构造器，可以不返回（绑定到 this 的都可以被访问）；provider 是加强版 factory，返回一个可配置的 factory。

详见 [AngularJS 之 Factory vs Service vs Provider](http://www.oschina.net/translate/angularjs-factory-vs-service-vs-provider)



# 五、Vue

## 1、组件间通信方式

Vue的官方文档对组件间的通信方式做了详细的说明：[cn.vuejs.org](https://link.juejin.im?target=https%3A%2F%2Fcn.vuejs.org)

### 父组件向子组件传输

- 最常用的方式是在子组件标签上传入数据，在子组件内部用`props`接收：

```
// 父组件
<template>
  <children name="boy"></children>
</template>
<script>
// 子组件：children
export default {
  props: {
    name: String
  }
}
</script>
复制代码
```

- 还可以在子组件中用 `this.$parent` 访问父组件的实例，不过官方文档有这样一段文字，很好的说明了 `$parent` 的意义：节制地使用 `$parent` 和 `$children` —— 它们的主要目的是作为访问组件的应急方法。更推荐用 `props` 和 `events` 实现父子组件通信。

### 子组件向父组件传输

- 一般在子组件中使用 `this.$emit('eventName', 'data')`，然后在父组件中的子组件标签上监听 `eventName` 事件，并在参数中获取传过来的值。

```
// 子组件
export default {
  mounted () {
    this.$emit('mounted', 'Children is mounted.')
  }
}
复制代码
<template>
  <children @mounted="mountedHandle"></children>
</template>
<script>
// 父组件
export default {
  methods: {
    mountedHandle (data) {
      console.log(data) // Children is mounted.
    }
  }
}
</script>
复制代码
```

- 与 `$parent` 一样，在父组件中可以通过访问 `this.$children` 来访问组件的所有子组件实例。

### 非父子组件之间的数据传递

- 对于非父子组件间，且具有复杂组件层级关系的情况，可以通过 `Vuex` 进行组件间数据传递： [vuex.vuejs.org/zh/](https://link.juejin.im?target=https%3A%2F%2Fvuex.vuejs.org%2Fzh%2F)
- 在 `Vue 1.0` 中常用的 `event bus` 方式进行的全局数据传递，在 `Vue 2.0` 中已经被移除，官方文档中有说明：`$dispatch` 和 `$broadcast` 已经被弃用。请使用更多简明清晰的组件间通信和更好的状态管理方案，如：`Vuex`。

## 2、双向绑定原理

[blog.seosiwei.com/detail/35](https://link.juejin.im?target=https%3A%2F%2Fblog.seosiwei.com%2Fdetail%2F35) [blog.seosiwei.com/detail/36](https://link.juejin.im?target=https%3A%2F%2Fblog.seosiwei.com%2Fdetail%2F36) [blog.seosiwei.com/detail/37](https://link.juejin.im?target=https%3A%2F%2Fblog.seosiwei.com%2Fdetail%2F37)

- 在组件内部实现一个双向数据绑定

假设有一个输入框组件，用户输入时，同步父组件页面中的数据
具体思路：父组件通过 props 传值给子组件，子组件通过 $emit 来通知父组件修改相应的props值，具体实现如下：

```
import Vue from 'vue'

const component = {
  props: ['value'],
  template: `
    <div>
      <input type="text" @input="handleInput" :value="value">
    </div>
  `,
  data () {
    return {
    }
  },
  methods: {
    handleInput (e) {
      this.$emit('input', e.target.value)
    }
  }
}

new Vue({
  components: {
    CompOne: component
  },
  el: '#root',
  template: `
    <div>
      <comp-one :value1="value" @input="value = arguments[0]"></comp-one>
    </div>
  `,
  data () {
    return {
      value: '123'
    }
  }
})
```

我们在父组件中做了两件事，一是给子组件传入props，二是监听input事件并同步自己的value属性。那么这两步操作能否再精简一下呢？答案是可以的，你只需要修改父组件：

```
template: `
    <div>
      <!--<comp-one :value1="value" @input="value = arguments[0]"></comp-one>-->
      <comp-one v-model="value"></comp-one>
    </div>
  `
```

v-model 实际上会帮我们完成上面的两步操作。

## 3、路由导航钩子

[router.vuejs.org/zh-cn/advan…](https://link.juejin.im?target=https%3A%2F%2Frouter.vuejs.org%2Fzh-cn%2Fadvanced%2Fnavigation-guards.html)

## 4、对MVVM开发模式的理解

MVVM分为Model、View、ViewModel三者。
**Model** 代表数据模型，数据和业务逻辑都在Model层中定义；
**View** 代表UI视图，负责数据的展示；
**ViewModel** 负责监听 **Model** 中数据的改变并且控制视图的更新，处理用户交互操作；
**Model** 和 **View** 并无直接关联，而是通过 **ViewModel** 来进行联系的，**Model** 和 **ViewModel** 之间有着双向数据绑定的联系。因此当 **Model** 中的数据改变时会触发 **View** 层的刷新，**View** 中由于用户交互操作而改变的数据也会在 **Model** 中同步。
这种模式实现了 **Model** 和 **View** 的数据自动同步，因此开发者只需要专注对数据的维护操作即可，而不需要自己操作 **dom**。

- Vue指令

v-html、v-show、v-if、v-for等等

* v-if 和 v-show 的区别: 

```v-show 仅仅控制元素的显示方式，将 display 属性在 block 和 none 来回切换；而v-if会控制这个 DOM 节点的存在与否。当我们需要经常切换某个元素的显示/隐藏时，使用v-show会更加节省性能上的开销；当只需要一次显示或隐藏时，使用v-if更加合理。```

## 5、Vue的响应式原理

当一个Vue实例创建时，vue会遍历data选项的属性，用 **Object.defineProperty** 将它们转为 getter/setter并且在内部追踪相关依赖，在属性被访问和修改时通知变化。
每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。



# 五、CSS

## 1、CSS引入方式

​	内联: 这种是在标签内直接写的，style="   "

​	内嵌: 这种是在head标签里，加一个style标签，在style里添加样式

​	外联: 这种是新建一个.css文件，通过link来引入样式

​	导入: 这种是在head标签里，加一个style标签，再写@import  url（），跟用link的方式差不多

​	跟link实现的效果一样，不同是link是页面加载的时候同时加载引入的样式，而@import是页面加载完成后，再	加载引入的样式；并且link是xhtml中的标签，所以兼容所有浏览器，但@import是在CSS2.1提出的，所以低版	本的浏览器会不兼容；link是可以通过js来改变样式的，@import就是不可以的；还有就是link可以引入除了css	以为的其他文件，但@import只能引入css文件。

## 2、@charset作用

在外部样式表文件中使用，告诉浏览器用什么编码方式解码该样式文件

## 3、@import 作用

@import用来引入外部样式

<style> @import url('base.css') body{ margin:0; } </style>
## 4、id选择器和class选择器的使用场景

id选择器使用前确定此元素权限较高且在文档里具有唯一性才可用
class选择器是使用的最广最多的选择器，可以给任何元素添加class属性

## 5、CSS中常见的选择器

通配符选择器
*{ margin:0; }
元素选择器
Id选择器
类（class）选择器
属性选择器
后代选择器
父子选择器
群组选择器
伪类选择器
伪元素选择器

[选择器的优先级是如何计算](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)

## 6、src 和 href 的区别

- src用于替换当前元素， href 用于在当前文档和引用资源之间确立联系。
  src是 source 的缩写，指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本， img 图片和 frame 等元素。

<script src ='js.js'></script>
当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。

- href是 Hypertext Reference 的缩写，指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，如果我们在文档中添加

<link href='common.css' rel='stylesheet'/>

那么浏览器会识别该文档为css文件，就会并行下载资源并且不会停止对当前文档的处理。这也是为什么建议使用 link 方式来加载 css ，而不是使用 @import 方式。