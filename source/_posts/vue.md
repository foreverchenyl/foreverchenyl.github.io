---
title: vue学习笔记
date: 2019-07-30 16:29:00
categories:
- 前端
tags:
- vue
---

Start Vue

vue.js汇总~~~~

<!-- more-->

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
- 

### 子组件向父组件传输

vue中子组件调用父组件的方法：

参考： https://www.cnblogs.com/jin-zhe/p/9523782.html

第一种方法是直接在子组件中通过this.$parent.event来调用父组件的方法

第二种方法是在子组件里用`$emit`向父组件触发一个事件，父组件监听这个事件就行了。

第三种是父组件把方法传入子组件中，在子组件里直接调用这个方法

- 一般在子组件中使用 `this.$emit('eventName', 'data')`，然后在父组件中的子组件标签上监听 `eventName` 事件，并在参数中获取传过来的值。

```
// 子组件
export default {
  mounted () {
    this.$emit('mounted', 'Children is mounted.')
  }
}

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
```

- 与 `$parent` 一样，在父组件中可以通过访问 `this.$children` 来访问组件的所有子组件实例。

  

### 非父子组件之间的数据传递

- 对于非父子组件间，且具有复杂组件层级关系的情况，可以通过 `Vuex` 进行组件间数据传递： [vuex.vuejs.org/zh/](https://link.juejin.im?target=https%3A%2F%2Fvuex.vuejs.org%2Fzh%2F)

- 在 `Vue 1.0` 中常用的 `event bus` 方式进行的全局数据传递，在 `Vue 2.0` 中已经被移除，官方文档中有说明：`$dispatch` 和 `$broadcast` 已经被弃用。请使用更多简明清晰的组件间通信和更好的状态管理方案，如：`Vuex`。

  

## 2、双向绑定原理

vue.js 是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty()来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

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

vue数据双向绑定是通过数据劫持结合发布者-订阅者模式的方式来实现的，vue是通过Object.defineProperty()来实现数据劫持的。通过Object.defineProperty( )设置了对象Book的name属性，对其get和set进行重写操作，顾名思义，get就是在读取name属性这个值触发的函数，set就是在设置name属性这个值触发的函数。

**实现过程**:

我们已经知道实现数据的双向绑定，首先要对数据进行劫持监听，所以我们需要设置一个监听器Observer，用来监听所有属性。如果属性发上变化了，就需要告诉订阅者Watcher看是否需要更新。因为订阅者是有很多个，所以我们需要有一个消息订阅器Dep来专门收集这些订阅者，然后在监听器Observer和订阅者Watcher之间进行统一管理的。接着，我们还需要有一个指令解析器Compile，对每个节点元素进行扫描和解析，将相关指令对应初始化成一个订阅者Watcher，并替换模板数据或者绑定相应的函数，此时当订阅者Watcher接收到相应属性的变化，就会执行对应的更新函数，从而更新视图。因此接下去我们执行以下3个步骤，实现数据的双向绑定：

1.实现一个监听器Observer，用来劫持并监听所有属性，如果有变动的，就通知订阅者。

2.实现一个订阅者Watcher，可以收到属性的变化通知并执行相应的函数，从而更新视图。

3.实现一个解析器Compile，可以扫描和解析每个节点的相关指令，并根据初始化模板数据以及初始化相应的订阅器。

流程图如下：

![img](https://images2015.cnblogs.com/blog/938664/201705/938664-20170522225458132-1434604303.png)



## 3、路由导航钩子

[router.vuejs.org/zh-cn/advan…](https://link.juejin.im?target=https%3A%2F%2Frouter.vuejs.org%2Fzh-cn%2Fadvanced%2Fnavigation-guards.html)



## 4、对MVVM开发模式的理解

MVVM分为Model、View、ViewModel三者。
**Model** 代表数据模型，数据和业务逻辑都在Model层中定义；
**View** 代表UI视图，负责数据的展示；
**ViewModel** 负责监听 **Model** 中数据的改变并且控制视图的更新，处理用户交互操作；
**Model** 和 **View** 并无直接关联，而是通过 **ViewModel** 来进行联系的，**Model** 和 **ViewModel** 之间有着双向数据绑定的联系。因此当 **Model** 中的数据改变时会触发 **View** 层的刷新，**View** 中由于用户交互操作而改变的数据也会在 **Model** 中同步。
这种模式实现了 **Model** 和 **View** 的数据自动同步，因此开发者只需要专注对数据的维护操作即可，而不需要自己操作 **dom**。



## 5、vue的响应式原理

当一个Vue实例创建时，vue会遍历data选项的属性，用 **Object.defineProperty** 将它们转为 getter/setter并且在内部追踪相关依赖，在属性被访问和修改时通知变化。
每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。



## 6、vue指令及属性

（1）**常见指令**

- v-text、v-on
-  v-if(判断是否隐藏)、v-for(把数据遍历出来)、v-bind(绑定属性)、v-model(实现双向绑定)
- v-model.trim、v-model.number、v-model.lazy

- v-html、v-show、v-if、v-for等等

  - v-if 和 v-show 的区别: 

  ```v-show 仅仅控制元素的显示方式，将 display 属性在 block 和 none 来回切换；而v-if会控制这个 DOM 节点的存在与否。当我们需要经常切换某个元素的显示/隐藏时，使用v-show会更加节省性能上的开销；当只需要一次显示或隐藏时，使用v-if更加合理。```

  - 相同点： 两者都是在判断DOM节点是否要显示

  - 不同点：

  a.实现方式： v-if是根据后面数据的真假值判断直接从Dom树上删除或重建元素节点。  v-show只是在修改元素的css样式，也就是display的属性值，元素始终在Dom树上。

  b.编译过程：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；  v-show只是简单的基于css切换；

  c.编译条件：v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译； v-show是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素始终被保留；

  d.性能消耗：v-if有更高的切换消耗，不适合做频繁的切换；  v-show有更高的初始渲染消耗，适合做频繁的额切换；

  `v-show` 不支持 `<template>` 元素，也不支持 `v-else`

  - v-for 与 v-if 的优先级

    v-for的优先级比v-if高

  

（2）**计算属性、  侦听属性、侦听器(watch)**

- [计算属性的 setter](https://cn.vuejs.org/v2/guide/computed.html#计算属性的-setter)

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：

```
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。



## 7、vue 组件中 data 

在 `new Vue()` 中，`data` 是可以作为一个对象进行操作的，然而在 `component` 中，`data` 只能以函数的形式存在，不能直接将对象赋值给它。

当data选项是一个函数的时候，**每个实例可以维护一份被返回对象的独立的拷贝**，这样各个实例中的data不会相互影响，是独立的



## 8、vue生命周期钩子函数

参考： https://www.jianshu.com/p/8b7373362b4c

总共分为8个阶段创建前/后，载入前/后，更新前/后，销毁前/后。

　　创建前/后

　　在beforeCreated阶段，vue实例的挂载元素$el和数据对象data都为undefined，还未初始化。

　　在created阶段，vue实例的数据对象data有了，$el还没有。

　　载入前/后

　　在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前为虚拟的dom节点，data.message还未替换。

　　在mounted阶段，vue实例挂载完成，data.message成功渲染。

　　更新前/后

　　当data变化时，会触发beforeUpdate和updated方法。

　　销毁前/后

　　在执行destroy方法后，对data的改变不会再触发周期函数，说明此时vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在



## 9、computed 和 watched 的区别

computed 是计算属性，依赖其他属性计算值，并且 computed 的值有缓存，只有当计算值变化才会返回内容。**计算属性是基于它们的响应式依赖进行缓存的**，只在相关响应式依赖发生改变时它们才会重新求值。

watch 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。

所以一般来说需要依赖别的属性来动态获得值的时候可以使用 computed，对于监听到值的变化需要做一些复杂业务逻辑的情况可以使用 watch。