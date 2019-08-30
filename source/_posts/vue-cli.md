---
title: vue-cli脚手架目录
date: 2019-08-30 16:29:00
categories:
- 前端
tags:
- vue
---

把cli配置的vue项目目录和配置文件理清楚 ~~~

<!-- more-->

### 一、vue-cli项目目录

#### 1、整个目录结构

![img](/images/vueallstructure.png)



#### 2、build文件夹下相关文件及目录

![img](/images/vuebuild.png)



#### 3、config文件夹下目录和文件

![img](/images/vueconfig.png)



### 二、相关的主要文件

#### 1、index.html

一般只定义一个空的根节点，在main.js里面定义的实例将挂载在#app节点下，内容通过vue组件填充。

![img](/images/index.png)



#### 2、App.vue文件

app.vue是项目的主组件，所有页面都是在app.vue下切换的。一个标准的vue文件，分为三部分。

```html
<template>
</template>

<script>
</script>

<style scoped>
</style>
```

<router-view></router-view>是子路由视图，后面的路由页面都显示在此处，相当于一个指示标，指引显示哪个页面。



#### 3、main.js

入口文件来着，主要作用是初始化vue实例并使用需要的插件。比如下面引用了4个插件，但只用了app（components里面是引用的插件）。

![img](/images/mainjs.png)



#### 4、router下面的index.js文件

路由配置文件

定义了三个路由，分别是路径为/，路径为/msg，路径为/detail。

![img](/images/router_index.png)



#### 5、package.json文件

[详解](https://www.cnblogs.com/tzyy/p/5193811.html#_h1_0)

#### 6、vue-cli脚手架之其他文件详解

[vue-cli脚手架之其他文件](https://www.cnblogs.com/hongdiandian/p/8321741.html)