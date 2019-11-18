---
title: Web Front End
date: 2019-07-25 11:29:00
categories:
- 前端
tags:
- Web
---

部分前端技术做个汇总~~~~

一、JavaScript基础

二、ECMAScript

三、浏览器相关

四、AngularJs

五、CSS

六、HTML

...

<!-- more-->

## 一、JavaScript基础

一个完整的JavaScript 实现应该由下列三
个不同的部分组成（见图1-1）。
q 核心（ECMAScript）
q 文档对象模型（DOM）
q 浏览器对象模型（BOM）



### 1、声明提前类问题

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



### 2、浏览器存储

- **localStorage，sessionStorage和cookie的区别**

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



### 3、跨域

对同源策略及各种跨域的方式进行了总结：[什么是跨域，为什么浏览器会禁止跨域，及其引起的发散性学习](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fqq_35271556%2Farticle%2Fdetails%2F80340102)



### 4、Promise的使用及原理

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



### 5、JavaScript事件循环机制

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



### 6、ES6作用域及let和var的区别

[ECMAScript 6 入门](https://link.juejin.im?target=http%3A%2F%2Fes6.ruanyifeng.com%2F%23docs%2Flet)中的`let 和 const 命令`章节对这个问题作出了详细的说明。

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



### 7、闭包

待补充



###  8、原型及原型链

待补充



### 9、浏览器的回流与重绘

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

- 优化方案：

- CSS

- 避免使用`table`布局。
- 尽可能在`DOM`树的最末端改变`class`。
- 避免设置多层内联样式。
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上。
- 避免使用`CSS`表达式（例如：`calc()`）。

- JavaScript

- 避免频繁操作样式，最好一次性重写`style`属性，或者将样式列表定义为`class`并一次性更改`class`属性。
- 避免频繁操作`DOM`，创建一个`documentFragment`，在它上面应用所有`DOM`操作，最后再把它添加到文档中。
- 也可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在`display`属性为`none`的元素上进行的`DOM`操作不会引发回流和重绘。
- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
- 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。



### 10、JS对象的深复制

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



### 11、JS运算精度丢失

此前转载了一篇文章，对JavaScript运算精度丢失的原因及解决方案都有比较详细的说明： [blog.csdn.net/qq_35271556…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fqq_35271556%2Farticle%2Fdetails%2F80137474)



### 12、defer 和async

JS会阻塞 DOM 的加载和渲染。

`defer` ：

- 立即加载JS，但不执行，待DOM渲染完成后再执行JS。
- 多个 `defer` 按顺序执行，但实际上不一定，所以建议只有1个JS使用 `defer` 参数。
- `XHTML` 下需写成 `defer="defer"` 。

`async` ：

- 异步加载JS (不阻塞DOM)，加载完后执行JS(此时会阻塞DOM)，执行完后继续加载/渲染DOM。
- 不按顺序执行。
- `XHTML` 下需写成 `async="async"` 。

![img](https://jian2333.github.io/images/pj-1.jpg)



[浅谈script标签的defer和async](https://juejin.im/entry/5a7ad55ef265da4e81238da9)



### 13、垃圾收集方式

JavaScript 中最常用的垃圾收集方式是标记清除（mark-and-sweep）。另一种不太常见的垃圾收集策略叫做引用计数（reference counting）



### 14、JS函数类型

JS中函数有两种类型，具名函数（命名函数）和匿名函数。
创建方式有：1. 声明函数  2.创建匿名函数表达式  3.创建具名函数表达式  4.Function构造函数  5.自执行函数  6.其他创建函数的方法



### 15、apply()与call()的区别

[apply()与call()的区别](https://www.cnblogs.com/lengyuehuahun/p/5643625.html)

**它们各自的定义：**

apply：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.apply(A, arguments);即A对象应用B对象的方法。

call：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.call(A, args1,args2);即A对象调用B对象的方法。

**它们的共同之处：**

都“可以用来代替另一个对象调用一个方法，将一个函数的对象上下文从初始的上下文改变为由thisObj指定的新对象”。

**它们的不同之处：**

apply：最多只能有两个参数——新this对象和一个数组argArray。如果给该方法传递多个参数，则把参数都写进这个数组里面，当然，即使只有一个参数，也要写进数组里。如果argArray不是一个有效的数组或arguments对象，那么将导致一个TypeError。如果没有提供argArray和thisObj任何一个参数，那么Global对象将被用作thisObj，并且无法被传递任何参数。

call：它可以接受多个参数，第一个参数与apply一样，后面则是一串参数列表。这个方法主要用在js对象各方法相互调用的时候，使当前this实例指针保持一致，或者在特殊情况下需要改变this指针。如果没有提供thisObj参数，那么 Global 对象被用作thisObj。 

实际上，apply和call的功能是一样的，只是传入的参数列表形式不同。



## 二、ECMAScript



### 1、let 和 const 命令

ES6 新增了`let`命令，用来声明变量。它的用法类似于`var`，但是所声明的变量，只在`let`命令所在的代码块内有效。

- 不存在变量提升
- 暂时性死区
- 不允许重复声明

```javascript
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

- `let`实际上为 JavaScript 新增了块级作用域。

[LearnMore](http://es6.ruanyifeng.com/#docs/let)



### 2、声明变量方法

ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6 除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，ES6 一共有 6 种声明变量的方法。

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



## 三、浏览器相关

### 1、浏览器从加载到渲染的过程

大致可以分为如下7步：

1. 输入网址；
2. 发送到DNS服务器，并获取域名对应的web服务器对应的ip地址；
3. 与web服务器建立TCP连接；
4. 浏览器向web服务器发送http请求；
5. web服务器响应请求，并返回指定url的数据（或错误信息，或重定向的新的url地址）；
6. 浏览器下载web服务器返回的数据及解析html源文件；
7. 生成DOM树，解析css和js，渲染页面，直至显示完成；

**加载过程：**

- 浏览器根据 DNS 服务器解析得到域名的 IP 地址
- 向这个 IP 的机器发送 HTTP 请求
- 服务器收到、处理并返回 HTTP 请求
- 浏览器得到返回内容

**渲染过程：**

- 根据 HTML 结构生成 DOM 树
- 根据 CSS 生成 CSSOM
- 将 DOM 和 CSSOM 整合形成 RenderTree
- 根据 RenderTree 开始渲染和展示
- 遇到`<script>`时，会执行并阻塞渲染



### 2、浏览器缓存机制

参考文章：[segmentfault.com/a/119000001…](https://link.juejin.im?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000011212929)



### 3、输入 URL 到展现涉及的缓存环节

- 地址栏缓存
- 检查HSTS预加载列表
- DNS缓存
- ARP（地址解析协议）缓存

参考文章：[More](https://toutiao.io/posts/9tv7pf)



### 4、性能优化

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

   

### 5、跨域

- 同源策略

同源策略是浏览器有一个很重要的概念。所谓同源是指，域名，协议，端口相同。不同源的客户端脚本(javascript、ActionScript)在没明确授权的情况下，不能读写对方的资源。简单的来说，浏览器允许包含在页面A的脚本访问第二个页面B的数据资源，这一切是建立在A和B页面是同源的基础上。

- 跨域的几种方式

jsonp（利用script标签的跨域能力）跨域、websocket（html5的新特性，是一种新协议）跨域、设置代理服务器（由服务器替我们向不同源的服务器请求数据）、CORS（跨源资源共享，cross origin resource sharing）、iframe跨域、postMessage(包含iframe的页面向iframe传递消息)



## 四、AngularJs

### 1、factory、service 和 provider

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



## 五、CSS

### 1、CSS引入方式

​	内联: 这种是在标签内直接写的，style="   "

​	内嵌: 这种是在head标签里，加一个style标签，在style里添加样式

​	外联: 这种是新建一个.css文件，通过link来引入样式

​	导入: 这种是在head标签里，加一个style标签，再写@import  url（），跟用link的方式差不多

​	跟link实现的效果一样，不同是link是页面加载的时候同时加载引入的样式，而@import是页面加载完成后，再	加载引入的样式；并且link是xhtml中的标签，所以兼容所有浏览器，但@import是在CSS2.1提出的，所以低版	本的浏览器会不兼容；link是可以通过js来改变样式的，@import就是不可以的；还有就是link可以引入除了css	以为的其他文件，但@import只能引入css文件。



### 2、@charset作用

在外部样式表文件中使用，告诉浏览器用什么编码方式解码该样式文件



### 3、@import 作用

@import用来引入外部样式

<style> @import url('base.css') body{ margin:0; } </style>
### 4、id选择器和class选择器的使用场景

id选择器使用前确定此元素权限较高且在文档里具有唯一性才可用
class选择器是使用的最广最多的选择器，可以给任何元素添加class属性



### 5、CSS中常见的选择器

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

 **!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性** 

!important > 内联 > ID选择器 > 类选择器 > 标签选择器。 

 同一级别中后写的会覆盖先写的样式 

[选择器的优先级是如何计算](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)



### 6、src 和 href 的区别

- src用于替换当前元素， href 用于在当前文档和引用资源之间确立联系。
  src是 source 的缩写，指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本， img 图片和 frame 等元素。

<script src ='js.js'></script>
当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。

- href是 Hypertext Reference 的缩写，指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，如果我们在文档中添加

<link href='common.css' rel='stylesheet'/>

那么浏览器会识别该文档为css文件，就会并行下载资源并且不会停止对当前文档的处理。这也是为什么建议使用 link 方式来加载 css ，而不是使用 @import 方式。



### 7、CSS布局

```
margin: 10px 20px 10px 20px;
```

这四个值以顺时针方式排列：顶部、右侧、底部、左侧，简称：**上右下左**。

- 样式覆盖
  - id > class
  - class类的声明部分中的 class 声明的顺序是重要的，第二个声明将始终优先于第一个声明。
  - !important > 内联样式 >  id > class > 标签

- position：static，relatice，absolute，fixed，sticky

  relative定位原点是自己，absolute定位原点是离自己最近的父元素



### 8、CSS3中新添加的样式

阴影，rgba，圆角

- border-radius：允许向元素添加圆角

- box-shadow：阴影

- 设置多层阴影：box-shadow:10px 10px 5px 5px gray,15px 15px 5px 5px blue,20px 20px 5px 5px gray;/* 多层阴影*/

- border-image属性用于设置图片边框




## 六、HTML

### 1、HTML5新特性、移除元素

新特性：

画布canvas

用于媒介播放的video和audio

新的语义化标签：article，header，nav，section，footer

新的本地存储：localstorage，sessionstorage

新的表单控件：date，time，calendar，url

新的技术：websocket，web worker，geoloacation

移除得元素：

可以用css代替的元素，font，fontbase，center，s，tt，u



### 2、 HTML5 存储类型以及区别

cookies，localstorage，sessionstorage
cookies的存储容量比较小而且数量有限制，一般为4K左右，localstorage的可以高达5M以上
cookies在设置的时间之前有效，localstorage本地永久存储，sessionstorage在当前窗口有效
cookies每次http请求都会被携带，会造成带宽浪费，localstorage和sessionstorage是保存在本地



### 3、document load 和document ready 的区别

1.load是当页面所有资源全部加载完成后（包括DOM文档树，css文件，js文件，图片资源等），执行一个函数
问题：如果图片资源较多，加载时间较长，onload后等待执行的函数需要等待较长时间，所以一些效果可能受到影响
2.$(document).ready()是当DOM文档树加载完成后执行一个函数 （不包含图片，css等）所以会比load较快执行
在原生的jS中不包括ready()这个方法，只有load方法就是onload事件



### 4、pre和code标签的区别

谷歌浏览器关于这两个标签的用户样式：

- `<code>`：

```
code {
    font-family: monospace;
}
```

- `<pre>`：

```
pre {
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0px;
}
```

不难看出`code`标签仅仅是给文字设置了浏览器的默认等宽字体；而`pre`标签默认的`white-space`属性值是`pre`，即保留连续空白符；



## 七、Ajax

### 1、实现AJAX的基本步骤

要完整实现一个AJAX异步调用和局部刷新,通常需要以下几个步骤:

​      (1)创建XMLHttpRequest对象,也就是创建一个异步调用对象.

​      (2)创建一个新的HTTP请求,并指定该HTTP请求的方法、URL及验证信息.

​      (3)设置响应HTTP请求状态变化的函数.

​      (4)发送HTTP请求.

​      (5)获取异步调用返回的数据.

​      (6)使用JavaScript和DOM实现局部刷新.