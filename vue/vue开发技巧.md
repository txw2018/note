#### 一键生成.vue文件模板

我们借用vscode自带的功能

```js
文件 -> 首选项 -> 用户代码片段 -> 点击新建代码片段 -> 取名vue.json 确定
```

编辑自己写的.vue模板，进行保存

```json
{
  "Print to console": {
    "prefix": "vue",
    "body": [
      "<!-- $0 -->",
      "<template>",
      "  <div></div>",
      "</template>",
      "",
      "<script>",
      "export default {",
      "  components: {},",
      "",
      "  data () {",
      "    return {",
      "    }",
      "  },",
      "",
      "  computed: {},",
      "",
      "  created () {},",
      "",
      "  methods: {}",
      "}",
      "",
      "</script>",
      "<style lang='scss' scoped>",
      "",
      "</style>"
  ],
    "description": "Log output to console"
  }
}
```

我们新建.vue文件，在首行输入vue按键盘Enter就可以了



#### 重置data或者获取data初始值

```javascript
this.$data //获取当前状态的data
this.$options.data() //获取该组件初始化状态下的data
Object.assign(this.$data,this.$options.data()) //重置data
```



####  强制刷新组件

```javascript
this.$forceUpdate() //迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。

/*
  强制替换元素/组件而不是重复使用它。当你遇到如下场景时它可能会很有用：
  完整地触发组件的生命周期钩子
  触发过渡
*/
<Component :key="key"/>
data(){
  return{
      key:0
  }
}
//刷新key达到刷新组件的目的
key++;
```



#### 长列表优化

**`Object.freeze()`** 方法可以**冻结**一个对象。一个被冻结的对象再也不能被修改；

当我们把一个对象传给实例的data，Vue会使用`Object.defineProperty`把这些属性响应式，使用了 [Object.freeze](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)之后，不仅可以减少 `observer` 的开销，还能减少不少内存开销，Vue有人提了相关[issue](https://github.com/vuejs/vue/issues/4384)

```javascript
this.list = Object.freeze(Object.assign({}, this.list))
```



#### $attrs和$listeners的使用场景

```javascript
A -> B -> C
$attrs //跨组件传值 A传值给C 在B组件上写上 v-bind="$attrs"
$listeners //跨组件执行事件 在C执行emit传值给A组件 在B组件上写上 v-on="$listeners"
一般我在对UI组件进行二次封装时，只写上常用的一些属性跟方法，写上$attrs和$listners，我们使用组件时就可以直接使用原组件的属性跟方法
```



#### 自动引入route文件

当我们项目比较大的时候，我们就会把路由文件根据不同的业务模块拆分

![image-20191202152422050](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191202152422050.png)

每个子模块导出一个路由配置数组

```javascript
export default [
  {
    path: '/home/welcome',
    name: 'home',
    component: () => import(/* webpackChunkName: "home" */ '@/pages/home/index.vue')
  }
]

```



我们现在要实现的是每增加一个模块自动引入，我们借用`webpack`的`api`[require.context](https://webpack.js.org/guides/dependency-management/#requirecontext)

![image-20191202152953273](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191202152953273.png)

#### 更新缓存的组件

使用vue的生命周期函数[activated](https://cn.vuejs.org/v2/api/#activated)

```
 它在keep-alive 组件激活时调用。
 该钩子在服务器端渲染期间不被调用。
```



####  watch的高级用法

1.当我们watch一个值时，第一次不会指向，只有值发生变化时才会执行，此时需要我们将immediate设为true

2.普通的watch方法无法无法监听对象内部属性的改变，我们把deep设为true时就能进行深度监听了

```
new Vue({
  el: '#app',
  data: {
    obj: { name: '蜡笔小新'}
  },
  watch: {
    obj: {
      handler(newName, oldName) {       },
      deep: true,
      immediate: true
    }
  } 
})

```



#### 异步数据传值给子组件

这是新手都会遇到的问题，父组件异步获取数据传给子组件，子组件拿不到值，下面是我平时的解决方案

```js
在子组件使用watch监听prop
```

or

```javascript
在父组件的data上定义一个Boolean值为flag:false，在使用的子组件时写上v-if="flag",当异步数据获取到时，把flag改成true
```



#### 巧用slot

插槽是一个很好用的api



#### 开发插件

Vue.js 的插件应该暴露一个 `install` 方法。这个方法的第一个参数是 `Vue` 构造器，第二个参数是一个可选的选项对象：

```vue
//MyPlugin.js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
//main.js
Vue.use(MyPlugin)
```

然后在main.js引入， 通过全局方法 `Vue.use()` 使用插件,查看[vue文档](https://cn.vuejs.org/v2/guide/plugins.html)