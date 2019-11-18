---
title: 前端笔试面试题
date: 2019-11-14 14:20:00
categories:
- 前端
tags:
- Web

---

- 笔试面试题~~~~~~~~~~~~

  <!-- more-->

  

单页面和多页面应用的区别

### 二、CSS

#### 1、a标签的href和onclick属性同时存在点击事件

- onclick的事件被先执行，其次是href中定义的（页面跳转或者javascript）
- 同时存在两个定义的时候（onclick与href都定义了），如果想阻止href的动作，在onclick必须加上return false; 一般是这样写οnclick="xxx();return false;".
- 在href中定义的函数如果有返回值的话，当前页面的内容将被返回值代替

* 如果页面过长有滚动条，且希望通过链接的 onclick 事件执行操作。应将它的 href 属性设为 javascript:void(0);，而不要是 #，这可以防止不必要的页面跳动；

* 所以，比较推荐的写法是<a href="javascript:void(0)"     οnclick="fn(this)"> 

* <a href="javascript:void(0);" οnclick="javascript:goUrl('http://www.sina.com');return false;">跳转3</a>

  [详细]( https://blog.csdn.net/qq_34507902/article/details/79091758 )

  

#### 2、样式选择器的优先级

!important > 内联 > ID选择器 > 类选择器 > 标签选择器。 

类选择器 同一级别中后写的会覆盖先写的样式 （指**定义的**顺序）



#### 3、行内元素和块级元素的布局特点

#### 4、盒子模型box-sizing有几种，都有什么区别？





### 三、ES6

#### 1、 let和const命令、块级作用域、变量

块级作用域：一个大括号{}所包起来的内容就是一个块级作用域；

let和const就是块级作用域，

严格模式：“use strict”；

如果在同一个块级作用域中，let一个变量**不能重复使用**，每一个变量名只能被let一次，const也是这样的。

const定义的是一个常量，这个常量**不能被修改**，但是如果const一个对象，对象的成员属性值**可以修改**，而这个对象那个最后返回的地址指针是**唯一**确定的。

var是有变量提升的，但是let并没有变量提升 。凡是在块级作用域中,let和const命令，在申明之前使用了，就会报错。 

[ES6的let与const命令](https://blog.csdn.net/Gbing1228/article/details/89335907)



### 四、JS

#### 1、手写一个promise 

```js
var promise = new Promise((resolve, reject) => {
  if (操作成功) {
    resolve(value)
  } else {
    reject(error)
  }
})
promise.then(function (value) {
  // success
}, function (value) {
  // failure
})
```



#### 2、简述原型。构造函数，实例的关系

#### 3、模拟定时采用setTimeout和setInterval的区别

setInterval函数的用法与setTimeout完全一致，区别仅仅在于setInterval指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行。 

setInterval指定的是，“开始执行”之间的间隔，因此实际上，两次执行之间的间隔会小于setInterval指定的时间。假定setInterval指定，每100毫秒执行一次，每次执行需要5毫秒，那么第一次执行结束后95毫秒，第二次执行就会开始。如果某次执行耗时特别长，比如需要105毫秒，那么它结束后，下一次执行就会立即开始。 

#### 4、js是什么类型的语言

#### 5、简述对象的深浅拷贝及实现

#### 6、简述防抖与节流

#### 7、document.write和innerHTML的区别

#### 8、axios内如果实现多个并发请求

```js
axios.all([
    axios.get('https://api.github.com/xxx/1'),
 	axios.get('https://api.github.com/xxx/2')
])
.then(axios.spread(function(userResp,reposResp){
    //上面两个请求都完成后，才执行这个回调方法
    console.log('User', userResp.data);
    console.log('Repositories', reposResp.data);
}))
```



#### 9、cookie和storage的区别

1. 共同点：

​    都是保存在浏览器端,且同源的



2. 区别

cookie数据始终在同源的http请求中携带(即使不需要)，即cookie在浏览器和服务器间来回传递

1) **存储大小限制不同**

cookie数据不能超过4K，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如回话标识。

webStorage虽然也有存储大小的限制，但是比cookie大得多，可以达到5M或更大

2) **数据的有效期不同**

 SessionStorage：仅在当前的浏览器窗口关闭有效；

 localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用 作持久数据；

 cookie：只在设置的cookie过期时间之前一直有效，即使窗口和浏览器关闭

 3) **作用域不同**

 sessionStorage：不在不同的浏览器窗口中共享，即使是同一个页面；

  localStorage：在所有同源窗口都是共享的；

  cookie：也是在所有同源窗口中共享的

4）**安全性问题**

由于在HTTP请求中的cookie是明文传递的（HTTPS不是），带来的安全性问题还是很大的。

 WebStorage并不作为HTTP header发送的浏览器，所以相对安全 

5）**网络负担**

cookie会被附加在每个HTTP请求中，在HttpRequest 和HttpResponse的header中都是要被传输的，所以无形中增加了一些不必要的流量损失。

 WebStorage不传送到服务器，所以不必要的流量可以节省，这样对于高频次访问或者针对手机移动设备的网页还是很不错的。 

 

这并不意味着WebStorage可以取代cookie，而是有了WebStorage后cookie能只做它应该做的事情了——作为客户端与服务器交互的通道，保持客户端状态。所以仅仅作为本地存储解决方案WebStorage是优于cookie的。 



### 五、性能优化

#### 1、前端常见的性能优化方法

[[WEB前端性能优化常见方法](https://segmentfault.com/a/1190000008829958)]