---
title: JavaScript高级程序设计
date: 2019-07-17 09:15:51
categories:
- 学习笔记
tags:
- web前端
- js
---

JavaScript小结

<!--More-->

一个完整的JavaScript 实现应该由下列三
个不同的部分组成（见图1-1）。
q 核心（ECMAScript）
q 文档对象模型（DOM）
q 浏览器对象模型（BOM）

![Image](C:\Users\YT2016063\Desktop\Image.png)

无论如何包含代码，只要不存在defer 和async 属性，浏览器都会按照<script>元素在页面中出现的先后顺序对它们依次进行解析。换句话说，在第一个<script>元素包含的代码解析完成后，第二个<script>包含的代码才会被解析，然后才是第三个、第四个……

按照传统的做法，所有<script>元素都应该放在页面的<head>元素中, 这种做法的目的就是把所有外部文件（包括CSS 文件和JavaScript 文件）的引用都放在相同的地方。可是，在文档的<head>元素中包含所有JavaScript 文件，意味着必须等到全部JavaScript 代码都被下载、解析和执行完成以后，才能开始呈现页面的内容（浏览器在遇到<body>标签时才开始呈现内容）。对于那些需要很多JavaScript 代码的页面来说，这无疑会导致浏览器在呈现页面时出现明显的延迟，而延迟期间的浏览器窗口中将是一片空白。为了避免这个问题，现代Web 应用程序一般都把全部JavaScript 引用放在<body>元素中页面内容的后面.

defer 属性只适用于外部脚本文件。这一点在HTML5 中已经明确规定，因此支持HTML5 的实现会忽略给嵌入脚本设置的defer 属性。同样与defer 类似，async 只适用于外部脚本文件，并告诉浏览器立即下载文件。但与defer不同的是，标记为async 的脚本并不保证按照指定它们的先后顺序执行。
异步脚本一定会在页面的load 事件前执行，但可能会在DOMContentLoaded 事件触发之前或之后执行。

ECMAScript 中的语句以一个分号结尾

if (test){ // 推荐使用
alert(test);
}

var message = "hi";
message = 100; // 有效，但不推荐

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

JS中函数有两种类型，具名函数（命名函数）和匿名函数。
创建方式有：1. 声明函数  2.创建匿名函数表达式  3.创建具名函数表达式  4.Function构造函数  5.自执行函数  6.其他创建函数的方法