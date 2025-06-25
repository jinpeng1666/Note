# 1-Vue的组件生命周期有哪些

- 创建阶段：`beforeCreate`和`created`
- 挂载阶段：`beforeMount`和`mounted`
- 更新阶段：`beforeUpdate`和`updated`
- 销毁阶段：`beforeDestroy` (Vue 2.x) / `beforeUnmount` (Vue 3)和`destroyed` (Vue 2.x) / `unmounted` (Vue 3)



# 2-在哪个生命周期内调用异步请求

- 在`cread`阶段调用异步请求
- 原因：
    - 组件实例已创建，可访问 data/methods
    - 服务端渲染 (SSR) 兼容性好



# 3-v-if 和 v-show 的区别

- `v-if` ：
    - 如果 `v-if="false"` 在初始渲染时：不会渲染任何内容到 DOM，不会创建组件实例，不会执行任何相关生命周期钩子
    - 条件由 true 转变为 false：
        - 触发组件的`beforeDestroy` 和 `destroyed` 钩子
        - 移除 DOM 节点
        - 销毁所有事件监听器
        - 子组件实例被完全销毁
    - 条件由 false 转变为 true：
        - 触发组件的 `beforeCreate`, `created`, `beforeMount` 和 `mounted` 钩子
        - 新建组件实例
        - 重新渲染 DOM
        - 重新绑定事件监听器
- `v-show` 不管初始条件是什么，元素总是会被渲染，通过元素的`display`属性进行控制
- 区别：
    - <font color="red">成本开销</font>：`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销
    - <font color="red">使用场景</font>：如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好