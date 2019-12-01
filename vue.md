#  Vue

#### vue响应式系统

Vue.js的响应式原理依赖于`Object.defineProperty`这个api，这也是Vue.js不支持IE8 以及更低版本浏览器的原因，具体使用方法可以参考[MDN文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

```javascript
function defineReactive (obj, key, val) {
    Object.defineProperty(obj, key, {
        enumerable: true,       /* 属性可枚举 */
        configurable: true,     /* 属性可被修改或删除 */
        get: function reactiveGetter () {
            return val;         
        },
        set: function reactiveSetter (newVal) {
            if (newVal === val) return;
            cb(newVal);
        }
    });
}
function observer (value) {
    if (!value || (typeof value !== 'object')) {
        return;
    }
    
    Object.keys(value).forEach((key) => {
        defineReactive(value, key, value[key]);
    });
}
class Vue {
    /* Vue构造类 */
    constructor(options) {
        this._data = options.data;
        observer(this._data);
    }
}
let vm = new Vue({
    data: {
        test: "I am test."
    }
});
vm._data.test = "hello,world.";  /* 视图更新啦～ */
```



####  为什么data要是一个函数

```javascript
因为组件可能被用来创建多个实例。如果 data 仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供 data 函数，每次创建一个新实例后，我们能够调用 data 函数，从而返回初始数据的一个全新副本数据对象
```



#### 为什么data里面的属性可以直接用this访问

```javascript
    class Vue {
      constructor(options){
        this._data = options.data
        _proxy.call(this, options.data);/*构造函数中*/

      }
    }
    const vm = new Vue({
      data:{
        msg:"hello Vue"
      }
    })
    console.log(vm.msg); // hello Vue

    /*代理*/
    function _proxy (data) {
      const that = this;
      Object.keys(data).forEach(key => {
          Object.defineProperty(that, key, {
              configurable: true,
              enumerable: true,
              get: function proxyGetter () {
                  return that._data[key];
              },
              set: function proxySetter (val) {
                  that._data[key] = val;
              }
          })
      });
    }
```



#### 为什么v-for跟v-if不要用在同一元素上

当它们处于同一节点，`v-for` 的优先级比 `v-if` 更高，这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。当你只想为*部分*项渲染节点时，这种优先级的机制会十分有用，如下：

```vue
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>


<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

优化

```vue
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
computed: {
  activeUsers: function () {
    return this.users.filter(function (user) {
      return user.isActive
    })
  }
}


<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```



#### 为什么methods里面的this都指向当前组件的实例

```js
  function initMethods (vm, methods) {
    var props = vm.$options.props;
    for (var key in methods) {
      {
        if (typeof methods[key] !== 'function') {
          warn(
            "Method \"" + key + "\" has type \"" + (typeof methods[key]) + "\" in the component definition. " +
            "Did you reference the function correctly?",
            vm
          );
        }
        if (props && hasOwn(props, key)) {
          warn(
            ("Method \"" + key + "\" has already been defined as a prop."),
            vm
          );
        }
        if ((key in vm) && isReserved(key)) {
          warn(
            "Method \"" + key + "\" conflicts with an existing Vue instance method. " +
            "Avoid defining component methods that start with _ or $."
          );
        }
      }
      vm[key] = typeof methods[key] !== 'function' ? noop : bind(methods[key], vm);
    }
  }


  function polyfillBind (fn, ctx) {
    function boundFn (a) {
      var l = arguments.length;
      return l
        ? l > 1
          ? fn.apply(ctx, arguments)
          : fn.call(ctx, a)
        : fn.call(ctx)
    }

    boundFn._length = fn.length;
    return boundFn
  }

  function nativeBind (fn, ctx) {
    return fn.bind(ctx)
  }

  var bind = Function.prototype.bind
    ? nativeBind
    : polyfillBind;
```

我们可以看到initMethods的时候遍历了methods给每个方法执行了bind把vm(当前的实例)传进去，并给不支持bind方法的提供了polyfill

#### 为什么循环的时候key避免用index