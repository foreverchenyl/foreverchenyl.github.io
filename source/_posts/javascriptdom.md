---
title: JavaScript DOM编程艺术
date: 2019-08-06 17:31:03
categories:
- 前端
tags:
- JavaScript

---

整本书读起来通畅易懂，让人易于接受。。。

这本书通过一个个实例循序渐进，学到新的知识后立马会在前一个案例的基础上实现增强~

最后结束的一个综合示例，将本书学到的几乎全部知识都集中运用在同一个案例~~~

#### 笔记

<!--More-->

1.把`<script>`标签放到HTML文档的最后，`<\body>`标签之前，能使浏览器更快地加载页面。

2.如果对某个变量赋值之前未声明，赋值操作将自动声明该变量。

3.变量和其他语法元素的名字是区分大小写的。

4.不允许变量名中包含空格或标点符号（美元符号除外）。

5.通常驼峰格式是函数名，方法名和对象属性名命名的首选格式。

6.数组中的任何一个元素都可以把一个数组作为它的值。

7.在JavaScript中，所有的变量实际上都是某种类型的对象。

8.使用数组创建对象：
var lennon = Array();
lennon["name"] = "John";
lennon["year"] = 1940;
lennon["living"] = false; (不推荐)
应该使用通用的对象：
var lennon = Object();
lennon.name = "John";
lennon.year = 1940;
lennon.living = false;
更简洁的语法 ： var lennon = { name:"John",year:1940, living:false}
接下来：
var beatles = {};
beatles.vocalist = lennon;
就可以通过beatles.vocalist.name等来引用它的相关属性。

9.请记住，如果把字符串和数值拼接在一起，其结果将是一个更长的字符串，但如果用同样的操作符来“拼接”两个数值，其结果将是那两个数值的算术和。

10.相等操作符==认为空字符串与false的含义相同，要进行严格的比较，就要使用另一种等号（===），这个全等操作符会执行严格的比较，不仅比较值，而且会比较变量的类型。

11.函数的真正价值体现在，我们还可以把他们当作一种数据类型来使用，这意味着可以把一个函数的调用结果赋给一个变量。

12.在命名时，用下划线来分隔各个单词，在命名函数时，从第二个单词开始把每个单词的第一个字母写成大写形式。

13.使用var声明为局部变量。

14.属性是隶属于某个特定对象的变量；
方法是只有某个特定对象才能调用的函数。
对象就是由一些属性和方法组合在一起而构成的一个数据实体。
为给定对象创建一个新实例需要使用new关键字
如：var jeremy = new Person;
上面这条语句将创建出person对象的一个新实例jeremy。就可以像下面这样利用person对象的属性来检索关于jeremy的信息了：
jeremy.age jeremy.mood；

15.Javascript提供了一系列预先定义好的对象，这些可以拿来就用的对象称为内建对象。
如：Array（） Math对象 Data对象等。

16.由浏览器提供的预定义对象被成为宿主对象。如：Form，Image , Element还有document也是。

17.当创建了一个网页并把他加载到Web浏览器中时，DOM就会在幕后悄然而生，它把你编写的网页文档转换为一个文档对象。

18.window对象对应着浏览器窗口本身，这个对象的属性和方法通常统称为BOM（浏览器对象模型）
BOM提供了window.open和window.blur等方法。

19.事实上，文档中的每一个元素都是一个对象，利用DOM提供的方法可以得到任何一个对象。

20.getElementsByTagName允许把一个通配符作为它的参数，而这意味着文档里的每个元素都将在这个函数返回的数组里占有一席之地。

21.通过setAttribute对文档做出修改之后，在通过浏览器的view source选项去查看文档的源代码时看到的仍将是改变前的属性值，也就是说，setAttribute做出的修改不会反映在文档本身的源代码里，这种“表里不一”的现象源自DOM的工作模式：先加载文档的静态内容，再动态刷新，动态刷新不影响文档的静态内容。这正是DOM的真正威力:对页面内容进行刷新却不需要在浏览器里刷新页面。

22.DOM是一种适用于多种环境和多种程序设计语言的通用型API。

23.childNodes属性返回的数组包含所有类型的节点，而不仅仅是元素节点。
每一个节点都有自己的nodeType属性：
元素节点：1
属性节点：2
文本节点：3

24.`<p>`元素本身的nodeValue属性是一个空值，而你真正需要的是<p>元素所包含的文本的值。包含在`<p>`元素里的文本是另一种节点，它是`<p>`元素的第一个节点，因此，你想得到的其实是它的第一个子节点的nodeValue的值。

25.window.open()方法的一种典型应用：
function popUp(winURL){
window.open(winURL,"popup","width=320,height=480");
}

26.下面是通过“javascript:”伪协议调用popUp()函数的具体做法：
<a href="javascript:popUp('http://www.example.com/');">Example</a>

27.文档将被加载到一个浏览器窗口里，document对象又是window对象的一个属性，当window对象触发onload事件时，document对象已经存在：window.onload = prepareLinks；

28.对象检测：只要把某个方法打包在一个if语句里，就可以根据这条if语句的条件表达式的求值结果来决定应该采取怎样的行动。

29.性能考虑：
（1）尽量少访问DOM和尽量减少标记；
（２）合并和放置脚步；
（３）压缩脚本：多数情况下应该有两个版本，一个是工作副本加上注释，一个是精简副本，用于放在站点上。

30.作者个人认为，如果一个函数有多个出口，只要这些出口集中出现在函数的开头部分，就是可以接受的。

31.在几乎所有的浏览器里，用Tab键移动到某个链接然后按下回车键的动作也会触发onclick事件。

32.DOM Core和HTML-DOM的区别：
document.getElementsByTagName("form")//DOM Core
document.forms //HTML-DOM

33.一旦你使用了innerHTML属性，它的全部内容都将被替换。

34.你并不是在创建标记，而是在改变ＤＯＭ节点树。

35.调用insertBefore()方法时，你必须告诉它三件事：
新元素
目标元素
父元素
如parentElement.insertBefore(newElement,targetElement)

36.XMLHttpRequest对象有许多的方法，其中最有用的是open方法，它用来指定服务器上将要访问的文件，指定请求类型：GET 、POST或SEND。这个方法的第三个参数用于指定是否以异步方式发送和处理。

37.onreadystatechange是一个时间处理函数，他会在服务器给XMLHttpRequest对象送回响应的时候被触发执行。在这个处理函数中，可以根据服务器的具体相应做响应的处理。

38.在指定了请求的目标，也明确了如何处理响应之后，就可以用send方法来发送请求了。、

39.访问服务器发送回来的数据要通过两个属性完成。一个是responseText属性，这个属性用于保存文本字符串形式的数据。另一个是responseXML，用于保存Content-Type头部中指定为“text/xml”的数据，其实是一个DocumentFragment对象。

40.使用XMLHttpRequest对象发送的请求只能访问与其所在的HTML处于同一个域中的数据。

41.脚本在发送XMLHttpRequest请求之后，仍然会继续执行，不会等待响应返回。

42.当你需要引用一个中间带减号的CSS属性时，DOM要求你用驼峰命名法。

43.DOM style属性不能用来检索在外部CSS文件里声明的样式。

44.style对象的属性的值必须放在引号里。

45.在脚本中设置style的属性会覆盖文档的外部样式表和内嵌样式的样式。

46.与其使用DOM直接改变某个元素的样式，不如通过JavaScript代码去更新这个元素的class属性。

47.（其他）样访问函数的指针而不执行函数的话必须去掉函数名后的那对圆括号。

48.如果把position属性值是absolute的元素A放入一个position属性值是relative的元素B，B就成为A的容器元素，而A将在B的显示区域里按absolute方式进行摆放。

49.typeof可以告诉我们它的操作数是一个对象、数值、布尔值、字符串或是一个函数。