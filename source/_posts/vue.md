---
title: Vue Learning
date: 2019-07-30 16:29:00
categories:
- 前端
tags:
- vue
---

Start Vue ~~~

<!-- more-->

### 1、组件间通信方式

Vue的官方文档对组件间的通信方式做了详细的说明：[cn.vuejs.org](https://link.juejin.im?target=https%3A%2F%2Fcn.vuejs.org)

#### 父组件向子组件传输

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

#### 子组件向父组件传输

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

  

#### 非父子组件之间的数据传递

- 对于非父子组件间，且具有复杂组件层级关系的情况，可以通过 `Vuex` 进行组件间数据传递： [vuex.vuejs.org/zh/](https://link.juejin.im?target=https%3A%2F%2Fvuex.vuejs.org%2Fzh%2F)

- 在 `Vue 1.0` 中常用的 `event bus` 方式进行的全局数据传递，在 `Vue 2.0` 中已经被移除，官方文档中有说明：`$dispatch` 和 `$broadcast` 已经被弃用。请使用更多简明清晰的组件间通信和更好的状态管理方案，如：`Vuex`。

  

### 2、双向绑定原理

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



### 3、路由导航钩子

[router.vuejs.org/zh-cn/advan…](https://link.juejin.im?target=https%3A%2F%2Frouter.vuejs.org%2Fzh-cn%2Fadvanced%2Fnavigation-guards.html)



### 4、对MVVM开发模式的理解

MVVM分为Model、View、ViewModel三者。
**Model** 代表数据模型，数据和业务逻辑都在Model层中定义；
**View** 代表UI视图，负责数据的展示；
**ViewModel** 负责监听 **Model** 中数据的改变并且控制视图的更新，处理用户交互操作；
**Model** 和 **View** 并无直接关联，而是通过 **ViewModel** 来进行联系的，**Model** 和 **ViewModel** 之间有着双向数据绑定的联系。因此当 **Model** 中的数据改变时会触发 **View** 层的刷新，**View** 中由于用户交互操作而改变的数据也会在 **Model** 中同步。
这种模式实现了 **Model** 和 **View** 的数据自动同步，因此开发者只需要专注对数据的维护操作即可，而不需要自己操作 **dom**。



### 5、vue的响应式原理

当一个Vue实例创建时，vue会遍历data选项的属性，用 **Object.defineProperty** 将它们转为 getter/setter并且在内部追踪相关依赖，在属性被访问和修改时通知变化。
每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。



### 6、vue指令及属性

**(1) 常见指令:**

- v-text、v-on

- v-if(判断是否隐藏)、v-for(把数据遍历出来)、v-bind(绑定属性)、v-model(实现双向绑定)

- v-model.trim、v-model.number、v-model.lazy

- v-html、v-show、v-if、v-for等等

- v-if 和 v-show 的区别: 

  v-show 仅仅控制元素的显示方式，将 display 属性在 block 和 none 来回切换；而v-if会控制这个 DOM 节点的存在与否。当我们需要经常切换某个元素的显示/隐藏时，使用v-show会更加节省性能上的开销；当只需要一次显示或隐藏时，使用v-if更加合理.

  - 相同点： 两者都是在判断DOM节点是否要显示
  
  - 不同点：
  
     a.实现方式： v-if是根据后面数据的真假值判断直接从Dom树上删除或重建元素节点。  v-show只是在修改元素的css样式，也就是display的属性值，元素始终在Dom树上。
  
      b.编译过程：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；  v-show只是简单的基于css切换；
  
      c.编译条件：v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译； v-show是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素始终被保留；
  
      d.性能消耗：v-if有更高的切换消耗，不适合做频繁的切换；  v-show有更高的初始渲染消耗，适合做频繁的额切换
  
   `v-show` 不支持 `<template>` 元素，也不支持 `v-else`
  
    - v-for 与 v-if 的优先级
  
      v-for的优先级比v-if高
      
      

**(2) 计算属性、  侦听属性、侦听器(watch)**

- [计算属性的 setter](https://cn.vuejs.org/v2/guide/computed.html#计算属性的-setter)

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：

```js
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
```

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。



### 7、vue 组件中 data 

在 `new Vue()` 中，`data` 是可以作为一个对象进行操作的，然而在 `component` 中，`data` 只能以函数的形式存在，不能直接将对象赋值给它。

当data选项是一个函数的时候，**每个实例可以维护一份被返回对象的独立的拷贝**，这样各个实例中的data不会相互影响，是独立的



### 8、vue生命周期钩子函数

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



### 9、computed 和 watched 的区别

computed 是计算属性，依赖其他属性计算值，并且 computed 的值有缓存，只有当计算值变化才会返回内容。**计算属性是基于它们的响应式依赖进行缓存的**，只在相关响应式依赖发生改变时它们才会重新求值。

watch 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。

所以一般来说需要依赖别的属性来动态获得值的时候可以使用 computed，对于监听到值的变化需要做一些复杂业务逻辑的情况可以使用 watch。



### 10、vuex

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。

![vuex](https://vuex.vuejs.org/vuex.png)

Vuex 通过 `store` 选项，提供了一种机制将状态从根组件“注入”到每一个子组件中（需调用 `Vue.use(Vuex)`）：

```js
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

通过在根实例中注册 `store` 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 `this.$store` 访问到。让我们更新下 `Counter` 的实现：

```js
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```



1、vuex的属性？

答：有五种，分别是 State、 Getter、Mutation 、Action、 Module



1.1、state

​        state为单一状态树，在state中需要定义我们所需要管理的数组、对象、字符串等等，只有在这里定义了，在vue.js的组件中才能获取你定义的这个对象的状态。

- mapState

- State特性

Vuex就是一个仓库，仓库里面放了很多对象。其中state就是数据源存放地，对应于与一般Vue对象里面的data。
state里面存放的数据是响应式的，Vue组件从store中读取数据，若是store中的数据发生改变，依赖这个数据的组件也会发生更新。
它通过mapState把全局的 state 和 getters 映射到当前组件的 computed 计算属性中。



1.2、getter

​        getter有点类似vue.js的计算属性，当我们需要从store的state中派生出一些状态，那么我们就需要使用getter，getter会接收state作为第一个参数，而且getter的返回值会根据它的依赖被缓存起来，只有getter中的依赖值（state中的某个需要派生状态的值）发生改变的时候才会被重新计算。

虽然在组件内也可以做计算属性，但是getters 可以在多组件之间复用

- 也可以通过让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用。

  ```js
  getters: {
    // ...
    getTodoById: (state) => (id) => {
      return state.todos.find(todo => todo.id === id)
    }
  }
  ```

- mapGetters



1.3、mutation

​        更改store中state状态的唯一方法就是提交mutation，就很类似事件。每个mutation都有一个字符串类型的事件类型和一个回调函数，我们需要改变state的值就要在回调函数中改变。我们要执行这个回调函数，那么我们需要执行一个相应的调用方法：store.commit。

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。

mutation 都是同步事务。

[mutation](https://vuex.vuejs.org/zh/guide/mutations.html)



1.4、action

​        action可以提交mutation，在action中可以执行store.commit，而且action中可以有任何的异步操作。在页面中如果我们要嗲用这个action，则需要执行store.dispatch

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

调用异步 API 和分发多重 mutation



1.5、module

​        module其实只是解决了当state中很复杂臃肿的时候，module可以将store分割成模块，每个模块中拥有自己的state、mutation、action和getter。



### 11、Vuex的作用以及应用场景

项目数据状态的集中管理，复杂组件(如兄弟组件、远房亲戚组件)的数据通信问题。

​        如果不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果你的应用够简单，那最好不要使用 Vuex。一个简单的 global event bus 就足够所需了。但是，如果需要构建是一个中大型单页应用，很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。

vuex 一般用于中大型 web 单页应用中对应用的状态进行管理，对于一些组件间关系较为简单的小型应用，使用 vuex 的必要性不是很大，因为完全可以用组件 prop 属性或者事件来完成父子组件之间的通信，**vuex 更多地用于解决跨组件通信以及作为数据中心集中式存储数据。**

对于多层级组件嵌套等较为复杂的场景，使用 vuex 能更好地应对。**vuex 是通过将 state 作为数据中心、各个组件共享 state 实现跨组件通信的**，此时的数据完全独立于组件，因此将组件间共享的数据置于 State 中能有效解决多层级组件嵌套的跨组件通信问题。

目前主要有两种数据会使用 vuex 进行管理：
1、组件之间全局共享的数据
2、通过后端异步请求的数据  (**即把通过后端异步请求的数据都纳入 vuex 状态管理，在 Action 中封装数据的增删改查等逻辑，这样可以一定程度上对前端的逻辑代码进行分层，使组件中的代码更多地关注页面交互与数据渲染等视图层的逻辑，而异步请求与状态数据的持久化等则交由 vuex 管理。**)

**使用 vuex 解决跨组件通信问题**
跨组件通信一般指非父子组件间的通信，父子组件的通信一般可以通过以下方式：
**1、通过 prop 属性实现父组件向子组件传递数据**
**2、通过在子组件中触发事件实现向父组件传递数据**
非父子组件之间的通信一般通过一个空的 Vue 实例作为 中转站，也可以称之为 事件中心、event bus。  



### 12、vuex工作原理

[文章](https://www.cnblogs.com/changk/p/8657465.html)

### 13、为虚拟 dom会提高性能，解释一下它的工作原理

虚拟DOM其实就是一个JavaScript对象。通过这个JavaScript对象来描述真实DOM，真实DOM的操作，一般都会对某块元素的整体重新渲染，采用虚拟DOM的话，当数据变化的时候，只需要局部刷新变化的位置就好了



### 14、一些 package.json 里面的配置

[详细](https://blog.csdn.net/weixin_42420703/article/details/81870815)

### 15、Vue是一套渐进式框架

每个框架都不可避免会有自己的一些特点，从而会对使用者有一定的要求，这些要求就是主张，主张有强有弱，它的强势程度会影响在业务开发中的使用方式。 使用vue，你 可以在原有

每个框架都不可避免会有自己的一些特点，从而会对使用者有一定的要求，这些要求就是主张，主张有强有弱，它的强势程度会影响在业务开发中的使用方式。
 使用vue，你可以在原有大系统的上面，把一两个组件改用它实现，当jQuery用；也可以整个用它全家桶开发，当Angular用；
 还可以用它的视图，搭配你自己设计的整个下层用。你可以在底层数据逻辑的地方用OO和设计模式的那套理念。
 也可以函数式，都可以。
 它只是个轻量视图而已，只做了自己该做的事，没有做不该做的事，仅此而已

你不必一开始就用Vue所有的全家桶，根据场景，官方提供了方便的框架供你使用。



### 16、vue-cli提供的几种脚手架模板有哪些，区别

webpack模板与webpack-simple

区别见：https://blog.csdn.net/Lisunlight/article/details/80999948



### 17、计算属性的缓存和方法调用的区别

1、我们可以将同一函数定义为一个方法或是一个计算属性。两种方式的最终结果确实是完全相同的。不同的是**计算属性是基于它们的依赖进行缓存的**。只在相关依赖发生改变时它们才会重新求值。相比之下，每当触发重新渲染时，调用方法将**总会**再次执行函数。

2、使用计算属性还是`methods`取决于是否需要缓存，当遍历大数组和做大量计算时，应当使用计算属性，除非你不希望得到缓存。

我们为什么需要缓存？假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用方法来替代。

3、计算属性是根据依赖自动执行的，`methods`需要事件调用。



### 18、axios、fetch与ajax的区别

[详见1](https://www.jianshu.com/p/8bc48f8fde75)

[详见2](https://www.cnblogs.com/zhang134you/p/10636061.html)

### 19、vue中央事件总线的使用

[简易使用Vue中的中央事件总线(eventBus)](https://juejin.im/post/5d358280e51d4556bc06704d)

[详见](https://www.cnblogs.com/zeroes/p/vue-run-dev.html)

### 20、Vue开发命令 npm run dev 输入后的执行过程

[简述](https://www.cnblogs.com/zeroes/p/vue-run-dev.html)



### 21、vue-cli中常用到的加载器

​		1.模板:

　　　　(1)html-loader:将HTML文件导出编译为字符串，可供js识别的其中一个模块

　　　　(2)pug-loader : 加载pug模板

　　　　(3)jade-loader : 加载jade模板(是pug的前身，由于商标问题改名为pug)

　　　　(4)ejs-loader : 加载ejs模板

　　　　(5)handlebars-loader : 将Handlebars模板转移为HTML

　　2.样式:

　　　　(1)css-loader : 解析css文件中代码

　　　　(2)style-loader : 将css模块作为样式导出到DOM中

　　　　(3)less-loader : 加载和转义less文件

　　　　(4)sass-loader : 加载和转义sass/scss文件

　　　　(5)postcss-loader : 使用postcss加载和转义css/sss文件

　　3. 脚本转换编译:

　　　　(1)script-loader : 在全局上下文中执行一次javascript文件，不需要解析

　　　　(2)babel-loader : 加载ES6+ 代码后使用Babel转义为ES5后浏览器才能解析

　　　　(3)typescript-loader : 加载Typescript脚本文件

　　　　(4)coffee-loader : 加载Coffeescript脚本文件

　　4. JSON加载:

　　　　(1)json-loader : 加载json文件（默认包含）

　　　　(2)json5-loader : 加载和转义JSON5文件

　　5.Files文件

　　　　(1)raw-loader : 加载文件原始内容(utf-8格式)

　　　　(2)url-loader : 多数用于加载图片资源,超过文件大小显示则返回data URL

　　　　(3)file-loader : 将文件发送到输出的文件夹并返回URL(相对路径)

　　　　(4)jshint-loader : 检查代码格式错误

　　6.加载框架:

　　　　(1)vue-loader : 加载和转义vue组件

　　　　(2)angualr2-template--loader : 加载和转义angular组件

　　　　(3)react-hot-loader : 动态刷新和转义react组件中修改的部分,基于webpack-dev-server插件需先安装,然后在webpack.config.js中引用react-hot-loader

[详细](https://www.jianshu.com/p/ac8e685577cd)

### 22、Vue中如何利用 keep-alive 标签实现某个组件缓存功能

[详见](https://www.php.cn/js-tutorial-398645.html)

### 23、pc端页面刷新时如何实现vuex缓存

[详见](https://www.csdn.net/gather_22/MtTaAgysNTY3NC1ibG9n.html)

### 24、vue-router如何响应 路由参数 的变化

[详见](https://www.cnblogs.com/mengfangui/p/9154253.html)

### 25、Vue 组件中 data 为什么必须是函数

[详见](https://www.jianshu.com/p/839cbef3be41)