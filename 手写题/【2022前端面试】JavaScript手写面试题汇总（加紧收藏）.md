# 【2023前端面试】JavaScript手写面试题汇总（加紧收藏）

## 1、JavaScript基础

### 1.1、手写Object.create

`Object.create()`是`JavaScript`中的一个方法，可以创建一个新对象，并将其原型设置为指定的对象。

**代码实现：**

```javascript
function myCreate(proto) {
  function F() {}
  F.prototype = proto;
  return new F();
}
```

这个方法接受一个参数`proto`，表示新对象的原型。它首先创建一个空函数`F`，然后将`F`的原型设置为`proto`，最后返回一个通过`F函数`创建的新对象。这个新对象的原型指向了`proto`，因此可以继承`proto`对象上的属性和方法。

### 1.2、手写instanceof

`instanceof`是`JavaScript`中的一个运算符，用于检查一个对象是否是某个构造函数的实例。

**代码实现：**

```javascript
function myInstanceOf(obj, constructor) {
  let proto = Object.getPrototypeOf(obj);
  while (proto !== null) {
    if (proto === constructor.prototype) {
      return true;
    }
    proto = Object.getPrototypeOf(proto);
  }
  return false;
}
```

这个方法接受两个参数，`obj`表示要检查的对象，`constructor`表示要检查的构造函数。它首先获取`obj`对象的原型`proto`，然后不断向上遍历`proto`的原型链，直到`proto`为`null`或者找到了`constructor.prototype`为止。如果找到了`constructor.prototype`，就返回`true`，表示`obj`是`constructor`的实例；否则返回`false`。

### 1.3、手写new

`new`操作符是`JavaScript`中的一个运算符，用于创建一个新对象并调用一个构造函数来初始化它。

**代码实现：**

```javascript
function myNew(constructor, ...args) {
  const obj = Object.create(constructor.prototype);
  const result = constructor.apply(obj, args);
  return (typeof result === 'object' && result !== null) ? result : obj;
}
```

这个方法接受一个构造函数`constructor`和一些参数`args`，表示要创建的对象的构造函数和构造函数的参数。它首先使用`Object.create()`方法创建一个新对象`obj`，并将`obj`的原型设置为`constructor.prototype`。然后使用`apply()`方法调用`constructor`构造函数，将`obj`作为`this`对象，同时将`args`数组作为参数传递给`constructor`。最后，判断`constructor`返回的结果是否为对象，如果是对象则返回该对象，否则返回`obj`。

### 1.4、手写call

`call()`是`JavaScript`中的一个方法，用于调用一个函数并将其`this`指向指定的对象。

**代码实现**：

```javascript
Function.prototype.myCall = function (context, ...args) {
  context = context || window;
  const fn = Symbol('fn');
  context[fn] = this;
  const result = context[fn](...args);
  delete context[fn];
  return result;
}
```

这个方法是定义在`Function.prototype`对象上的，因此它可以作用于任何函数。它接受两个参数，`context`表示要指定的`this`对象，`args`表示要传递给函数的参数。它首先判断`context`是否为`null`或`undefined`，如果是则将其设置为全局对象`window`。然后使用`Symbol()`方法创建一个唯一的标识符`fn`，并将当前函数保存到`context[fn]`属性中。接着使用扩展运算符将`args`数组展开成一个个参数，调用`context[fn]`函数并传递这些参数。最后，删除`context[fn]`属性并返回函数的执行结果`result`。

### 1.5、手写apply

`apply()`是`JavaScript`中的一个方法，用于调用一个函数并将其`this`指向指定的对象，并且传递一个数组或类数组对象作为函数的参数。

**代码实现：**

```javascript
Function.prototype.myApply = function (context, argsArray) {
  context = context || window;
  const fn = Symbol('fn');
  context[fn] = this;
  const result = context[fn](...argsArray);
  delete context[fn];
  return result;
}
```

这个方法是定义在`Function.prototype`对象上的，因此它可以作用于任何函数。它接受两个参数，`context`表示要指定的`this`对象，`argsArray`表示要传递给函数的参数数组或类数组对象。它首先判断`context`是否为`null`或`undefined`，如果是则将其设置为全局对象`window`。然后使用`Symbol()`方法创建一个唯一的标识符`fn`，并将当前函数保存到`context[fn]`属性中。接着使用扩展运算符将`argsArray`数组或类数组对象展开成一个个参数，调用`context[fn]`函数并传递这些参数。最后，删除`context[fn]`属性并返回函数的执行结果`result`。

### 1.6、手写bind

`bind()`是`JavaScript`中的一个方法，用于创建一个新函数，其中`this`指向指定的对象，并且初始参数可以被预置。

**代码实现**：

```js
/**
 * 手写bind
 * @returns {function(): any}
 */
Function.prototype.myBind = function () {
  // 处理函数
  let args = Array.from(arguments);
  let thisArg = args.shift();
  // 暂存this
  let thisFunc = this;
  // 因为需要构造函数，所以不能是匿名函数了
  const fBound = function () {
    const newArgs = args.concat(Array.from(arguments));
    // 判断是否为构造函数
    thisArg = this instanceof fBound ? this : thisArg;
    return thisFunc.apply(thisArg, newArgs);
  }
  // 直接将原函数的prototype赋值给绑定函数
  fBound.prototype = this.prototype;
  // 返回
  return fBound;
}
```

**简述代码：**

1. 通过`Array.from()`将`arguments`转化为数组对象，通过`shift()`取出`thisArg`。
2. 使用`thisFunc`暂存当前函数的`this`。
3. 创建一个闭包函数`fBound`，`newArgs`接收合并处理的`arguments`。
4. 判断`fBound`是否为构造函数，如果是构造函数，返回闭包的`this`，反之，返回外部拿到的`thisArg`，使用`thisArg`来接收。
5. 使用`thisFunc.apply`传递`thisArg`值和参数`newArgs`。
6. 直接将原函数的`prototype`赋值给`fBound`。
7. 返回`fBound`。

## 2、功能函数

### 2.1、实现浅拷贝

`浅拷贝`可以通过**手动拷贝对象属性**来达到浅拷贝的效果。

**代码实现：**

```js
const shallowClone = target => {
  // 1、只拷贝对象
  if (!target && typeof target !== 'object') return
  // 2、根据传入的参数判断是对象还是数组
  let res = Array.isArray(target) ? [] : {}
  // 3、遍历res，只拷贝object上的属性
  for (let key in target) {
  		if (obj.hasOwnProperty(objKey)) {
      		res[key] = target[key]
    	}
  }
  return res
}
```

该方法接收一个参数，`target`表示要拷贝的目标对象。判断传入的参数是对象还是数组，初始化一个空数组或者空对象`res`，通过`for...in`循环遍历对象的所有属性，通过`hasOwnProperty`来判断是否是属于对象自身的属性。

### 2.2、实现深拷贝

`浅拷贝`可以通过**手动拷贝对象属性**，但要注意与浅拷贝的区别。深拷贝相对浅拷贝而言，如果遇到属性值为引用类型的时候，它新建一个引用类型并将对应的值复制给它，因此对象获得的一个新的引用类型而不是一个原有类型的引用，以达到深拷贝的效果。

**代码实现：**

```js
const sampleDeepClone = target => {
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

该方法接收一个参数，`target`表示要拷贝的目标对象。判断传入的参数是对象还是数组，初始化一个空数组或者空对象`res`，通过`for...in`循环遍历对象的所有属性，通过`hasOwnProperty`来判断是否是属于对象自身的属性。如果遇到属性值为`object`类型，则进行递归处理，否则正常拷贝。

### 2.3、手写防抖

`防抖（Debounce）`是一种常用的性能优化方式，它可以用于减少某些频繁触发的事件（比如`resize`、`scroll`、`input`等）的触发次数，从而减少资源的使用和页面的重绘。当事件被触发后，**防抖会等待一段时间（比如100ms），如果在这段时间内事件再次被触发，则重新计时；如果在这段时间内事件没有再次被触发，则执行事件处理函数**。

**代码实现**：

```js
function debounce(fn, wait) {
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

`debounce()`函数接受两个参数：`fn`表示要进行防抖处理的函数，`wait`表示等待的时间（以毫秒为单位）。它返回一个新的函数，这个函数会在等待时间内被多次调用时，只执行最后一次调用，并在等待时间结束后执行一次。

### 2.4、手写节流

`节流（Throttle）`是一种常用的性能优化方式，它可以用于减少某些频繁触发的事件（比如`resize`、`scroll`、`input`等）的触发次数，从而减少资源的使用和页面的重绘。当事件被触发后，**节流会在一段时间内（比如100ms）只执行一次事件处理函数，而忽略在此期间内的其他触发**。

**代码实现**：

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

`throttle()`函数接受两个参数：`fn`表示要进行节流处理的函数，`wait`表示等待的时间（以毫秒为单位）。它返回一个新的函数，这个函数会在等待时间内被多次调用时，只执行一次，并在等待时间结束后再次执行。

## 3、Promise
