# Vuex [![Build Status](https://circleci.com/gh/vuejs/vuex/tree/dev.png?style=shield)](https://circleci.com/gh/vuejs/vuex)

> Centralized State Management for Vue.js.

<p align="center">
  <img width="700px" src="https://raw.githubusercontent.com/vuejs/vuex/dev/docs/.vuepress/public/vuex.png">
</p>

- [What is Vuex?](https://vuex.vuejs.org/)
- [Full Documentation](http://vuex.vuejs.org/)

## vuex原理

[实现一版简易的vuex](./vuex.js)

### 前言

先抛出问题

- 使用Vuex只需执行 Vue.use(Vuex)，并在Vue的配置中传入一个store对象的示例，store是如何实现注入的？
- state内部是如何实现支持模块配置和模块嵌套的？
- 在执行dispatch触发action（commit同理）的时候，只需传入（type, payload），action执行函数中第一个参数store从哪里获取的？
- 如何区分state是外部直接修改，还是通过mutation方法修改的？
- 调试时的“时空穿梭”功能是如何实现的？

### 解惑

vuex核心功能如下

- Vue Components：Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。
- dispatch：操作行为触发方法，是唯一能执行action的方法。
- actions：操作行为处理模块。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。
- commit：状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。
- mutations：状态改变操作方法。是Vuex修改state的唯一推荐方法，其他修改方式在严格模式下将会报错。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。
- state：页面状态管理容器对象。集中存储Vue components中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。
- getters：state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。

### 源码阅读

核心功能在`store.js`，根据注释阅读便于理解。

### 总结

最后我们回过来看文章开始提出的5个问题。

1.  问：使用Vuex只需执行 Vue.use(Vuex)，并在Vue的配置中传入一个store对象的示例，store是如何实现注入的？

> 答：`Vue.use(Vuex)` 方法执行的是install方法，它实现了Vue实例对象的init方法封装和注入，使传入的store对象被设置到Vue上下文环境的$store中。因此在Vue Component任意地方都能够通过`this.$store`访问到该store。

2.  问：state内部支持模块配置和模块嵌套，如何实现的？

> 答：在store构造方法中有makeLocalContext方法，所有module都会有一个local context，根据配置时的path进行匹配。所以执行如`dispatch('submitOrder', payload)`这类action时，默认的拿到都是module的local state，如果要访问最外层或者是其他module的state，只能从rootState按照path路径逐步进行访问。

3.  问：在执行dispatch触发action(commit同理)的时候，只需传入(type, payload)，action执行函数中第一个参数store从哪里获取的？

> 答：store初始化时，所有配置的action和mutation以及getters均被封装过。在执行如`dispatch('submitOrder', payload)`的时候，actions中type为submitOrder的所有处理方法都是被封装后的，其第一个参数为当前的store对象，所以能够获取到 `{ dispatch, commit, state, rootState }` 等数据。

4.  问：Vuex如何区分state是外部直接修改，还是通过mutation方法修改的？

> 答：Vuex中修改state的唯一渠道就是执行 `commit('xx', payload)` 方法，其底层通过执行 `this._withCommit(fn)` 设置_committing标志变量为true，然后才能修改state，修改完毕还需要还原_committing变量。外部修改虽然能够直接修改state，但是并没有修改_committing标志位，所以只要watch一下state，state change时判断是否_committing值为true，即可判断修改的合法性。

5.  问：调试时的"时空穿梭"功能是如何实现的？

> 答：devtoolPlugin中提供了此功能。因为dev模式下所有的state change都会被记录下来，'时空穿梭' 功能其实就是将当前的state替换为记录中某个时刻的state状态，利用 `store.replaceState(targetState)` 方法将执行`this._vm.state = state` 实现。

### 引用

- [Vuex框架原理与源码分析 -美团](https://tech.meituan.com/vuex-code-analysis.html)


## Examples

- [Counter](https://github.com/vuejs/vuex/tree/dev/examples/counter)
- [Counter with Hot Reload](https://github.com/vuejs/vuex/tree/dev/examples/counter-hot)
- [TodoMVC](https://github.com/vuejs/vuex/tree/dev/examples/todomvc)
- [Flux Chat](https://github.com/vuejs/vuex/tree/dev/examples/chat)
- [Shopping Cart](https://github.com/vuejs/vuex/tree/dev/examples/shopping-cart)

Running the examples:

``` bash
$ npm install
$ npm run dev # serve examples at localhost:8080
```

## License

[MIT](http://opensource.org/licenses/MIT)
