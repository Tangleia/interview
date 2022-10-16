# 【2022前端面试】JavaScript手写面试题汇总（加紧收藏）



## JavaScript 基础

### 1、手写 Object.create

思路：将传入的对象作为原型

```js
function create(obj) {
  function F() {}
  F.prototype = obj
  return new F()
}
```

### 2. 手写 instanceof 方法

instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

实现步骤：

1. 首先获取类型的原型
2. 然后获得对象的原型
3. 然后一直循环判断对象的原型是否等于类型的原型，直到对象原型为 `null`，因为原型链最终为 `null`

具体实现：

```js
const _instanceof = (target, Fn) => {
	// 1、获取到对象的原型
  let proto = target.__proto__, prototype = Fn.prototype
	// 2、循环判断
  while (proto) {
  if (proto === prototype) return true
     proto = proto.__proto__
   }
   return false
}
```

### 3. 手写 new 操作符

在调用 `new` 的过程中会发生以上四件事情：

（1）首先创建了一个新的空对象

（2）设置原型，将对象的原型设置为函数的 prototype 对象。

（3）让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）

（4）判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。

```js
function myNew() {
  let newObject = null;
  let constructor = Array.prototype.shift.call(arguments)
  let result = null
  
  if (typeof constructor !== 'function') {
    console.error('type error')
    return
  }
  
  newObject = Object.create(constructor.prototype)
  
  result = constructor.prototype.apply(newObject, arguments)
  
  let flag = result && (typeof result === 'object' || typeof result === 'function')
  
  return flag ? result : newObject;
}
```

### 4、手写Promise

```js
const PENDING = "pending";
const RESOLVED = "resolved";
const REJECTED = "rejected";

function myPromise(fn) {
  var self = this
  this.state = PENDING
  this.value = null
  this.resolvedCallbacks = []
  this.rejectedCallbacks = []
  
  function resolve(value) {
    
  }
}
```

5、手写call

```js
// 补全代码
Function.prototype._call = function(obj, ...arguments) {
    // 1.判断调用对象是否是函数
    if (typeof this !== 'function') console.error('type error')
    // 2.获取参数
    let args = [...arguments].slice(1), res = null
    // 3、判断上下文是否存在，不存在设置为window
    obj = obj || window
    // 4、将调用函数设置为对象的方法
    obj.fn = this
    // 5、调用方法
    res = obj.fn(...args)
    // 6、移除属性
    delete obj.fn
    return res
}
```

6、手写apply

```js
// 补全代码
Function.prototype._apply = function(obj, ...arguments) {
    // 1.判断调用对象是否是函数
    if (typeof this !== 'function') console.error('type error')
    // 2.获取参数
    let res = null
    // 3、判断上下文是否存在，不存在设置为window
    obj = obj || window
    // 4、将调用函数设置为对象的方法
    obj.fn = this
    // 5、调用方法
    if (arguments[1]) {
    	res = obj.fn(...arguments[1])
    } else {
    	res = obj.fn()
    }
    // 6、移除属性
    delete obj.fn
    return res
}
```

7、手写bind

```js
// 补全代码
Function.prototype._bind = function(obj) {
    // 1、判断当前环境是否是函数
    if (typeof this !== 'function') console.error('type error')
    // 2、保留当前函数的引用
    let fn = this
    // 3、处理参数
    let args = [...arguments].slice(1)
    return function Fn() {
        return fn.apply(this instanceof Fn ? this : obj, args.concat(...arguments))
    }
}
```

8、实现浅拷贝

```js
const _shallowClone = target => {
  // 补全代码
  // 1、只拷贝对象
  if (!target && typeof target !== 'object') return
  // 2、根据传入的参数判断是对象还是数组
  let res = Array.isArray(target) ? [] : {}
  // 3、遍历res，只拷贝object上的属性
  for (let key in target) {
      if (target.hasOwnProperty(key)) {
          res[key] = target[key]
      }
  }
  return res
}
```

9、实现深拷贝

```js
const _sampleDeepClone = target => {
  // 补全代码
  // 1、只拷贝对象
  if (!target && typeof target !== 'object') return
  // 2、根据传入的参数判断是对象还是数组
  let res = Array.isArray(target) ? [] : {}
  // 3、遍历res，只拷贝object上的属性
  for (let key in target) {
      if (target.hasOwnProperty(key)) {
          res[key] = typeof target[key] === 'object' : _sampleDeepClone(target[key]) : target[key]
      }
  }
  return res
}
```

10、手写防抖

```js
function debounce(fn, wait) {
	// 
  let timer = null
  return function() {
    let content = this, args = arguments
    if (timer) {
      clearTimeout(timer)
      timer = null
    }
    timer = setTimeout(() => {
      fn.apply(content, args)
    }, wait)
  }
}
```

11、手写节流

```js
function throttle(fn, delay) {
  let curTime = Date.now()
  return function() {
    let content = this, args = arguments, nowTime = Date.now()
    // 判断时间间隔是否超过，超过了则执行函数
    if (nowTime - curTime >= delay) {
      curTime = Date.now()
      return fn.apply(content, args)
    }
  }
}
```

