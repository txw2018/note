响应式的Vue框架独特的特性，当我们将一个JavaScript对象传给Vue的data选项，Vue将会递归变量此对象的所有属性，并使用`Object.defineProperty`把这些属性转为getter/setter(这也是Vue不支持ie8以及更低版本浏览器的原因)
我们先创建一个Vue的构造函数

```js
function Vue(options){
    // 数据响应式原理
    let data = options.data; // 用户传入的数据
    // vm._data 就是检测后的数据了
    data = vm._data = typeof data === 'function' ? data.call(vm) : data;
    observe(options.data)
}
```

#### 实现**observe**

使用类是为了可以看到当前数据是否被观测过，防止对象重复观测

```js
function observe(data){
      // 如果这个数据不是对象 或者是null 那就不用监控了
    if(!(typeof obj === 'object' && obj !== null)){
        return;
    }
   if(data.__ob__ instanceof Observer){ // 防止对象被重复观测
        return ;
    }
  //对数据进行defineProperty
   return new Observer(data) // 可以看到当前数据是否被观测过
}
```

#### 接下来实现**Observer**类

 Object.defineProperty本身是可以观察数组的，但是为了性能考虑只是劫持了数组的方法

```js

class Observer{
    constructor(data){
        
        Object.defineProperty(data,'__ob__',{ // __ob__ 是一个响应式表示 对象数组都有
            enumerable:false, // 不可枚举
            configurable:false,
            value:this
        })
        // data.__ob__ = this; // 相当于在数据上可以获取到__ob__这个属性 指代的是Observer的实例
        //  Object.defineProperty本身是可以观察数组的，但是为了性能考虑只是劫持了数组的方法
        if(Array.isArray(data)){
            // vue如何对数组进行处理呢？ 数组用的是重写数组的方法  函数劫持
            // 改变数组本身的方法我就可以监控到了
            data.__proto__ = arrayMethods; // 通过原型链 向上查找的方式
            this.observeArray(data);
        }else{
            this.walk(data); // 可以对数据一步一步的处理
        }
    }
    observeArray(data){
        for(let i =0 ; i< data.length;i++){
            observe(data[i]);// 检测数组的对象类型
        }
    }
    walk(data){
        // 对象的循环   data:{msg:'zf',age:11}
        Object.keys(data).forEach(key=>{
            defineReactive(data,key,data[key]);// 定义响应式的数据变化
        })
    }
}
```

#### 实现defineReactive

```js
function defineReactive(data,key,value){
    observe(value); // 如果传入的值还是一个对象的话 就做递归循环检测
    Object.defineProperty(data,key,{
        get(){
            return value
        },
        set(newValue){
            if(newValue == value) return;
            observe(newValue); // 监控当前设置的值，有可能用户给了一个新值
            value = newValue;
        }
    })
}


```

#### 实现数组方法劫持

```js
let oldArrayMethods = Array.prototype; // 获取数组原型上的方法

// 创建一个全新的对象 可以找到数组原型上的方法，而且修改对象时不会影响原数组的原型方法
let arrayMethods = Object.create(oldArrayMethods);

let methods = [ // 这七个方法都可以改变原数组
    'push',
    'pop',
    'shift',
    'unshift',
    'sort',
    'reverse',
    'splice' 
]
methods.forEach(method=>{
    arrayMethods[method] = function (...args) { // 函数劫持 AOP
        // 当用户调用数组方法时 会先执行我自己改造的逻辑 在执行数组默认的逻辑
        const ob = this.__ob__; //获取当前实例
        let result  = oldArrayMethods[method].apply(this,args);
        let inserted;
        // push unshift splice 都可以新增属性  （新增的属性可能是一个对象类型）
        // 内部还对数组中引用类型也做了一次劫持  [].push({name:'zf'})
        switch (method) {
            case 'push':
            case 'unshift':
                inserted = args
                break;
            case 'splice': // 也是新增属性  可以修改 可以删除  [].splice(arr,1,'div')
                inserted = args.slice(2);
                break;
            default:
                break;
        }
        inserted && ob.observeArray(inserted);
        return result;
    }
})
```

