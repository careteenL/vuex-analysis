# 解析

## 前置知识

- 实现每一层都可以引用`$store`
  - 实现方式： 是用哪个`Vue.mixin`为每个组件实例去加，根组件直接加，子组件取父组件的。

- 类原型上面的方法如果是被解构使用，里面`this`指向就不是类实例的`this`了
  - 解决方法： 在`constructor`中为`this`重新绑定方法，在调用该方法之前获取到原型上的该方法并做保留。当解构该方法时，是使用的实例上的而非原型上。然后在重写的方法中调用原型上的方法，并为此指定`this`。即可保证this指向始终为类的实例。

- 递归实现无限命名空间
  - ？？？

- 确保只能通过`mutation`修改`state`，而不能直接修改。
  - 实现方式：通过`watch`监听`state`，其实在`commit`某一个`mutation`同时，设置一个标志标量`committing`为`true`，监听`state`变更时，会判断这个标志变量是否为`true`去更改`state`，否则提示不能修改。

- 提供的`api`
  - state
  - getters
  - mutations
  - actions
  - 配合组件使用语法糖
    - mapState
    - mapGetters
    - mapMutations
    - mapActions

- 提供插件的能力

- `vuex`和`redux`的区别
  - 相同点：出发点一样，都是为了解决组件间通信问题。
  - 不同点：实现思路和使用方式不一样。
