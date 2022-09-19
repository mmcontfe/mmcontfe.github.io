---
title: "深拷贝和浅拷贝"
excerpt: "你不知道的深拷贝和浅拷贝"
coverImage: "/assets/blog/static/深拷贝与浅拷贝.jpeg"
date: "2022-09-10T13:35:07.322Z"
author:
  name: menglingyu
  picture: "/assets/blog/authors/menglingyu.jpeg"
ogImage:
  url: "/assets/blog/static/深拷贝与浅拷贝.jpeg"
---

## 基本数据类型

- Number
- BigInt
- String
- Boolean
- null
- undifned
- **Object**

**对象**与**原始类型**的根本区别之一是，对象是“**通过引用**”存储和复制的，而原始类型：字符串、数字、布尔值等 —— 总是“**作为一个整体”**复制。

赋值了对象的变量存储的不是对象本身，而是该对象“**在内存中的地址**” → 换句话说就是对该对象的“**引用**”。

当一个对象变量被复制 → **引用被复制，而该对象自身并没有被复制**。

```jsx
let obj = { name: 'xiaoming' };

let newObj = obj; // 复制引用

obj.name = 'xiaohong'; // 通过 "obj" 引用来修改

alert(newObj.name); // 'xiahong'，修改能通过 "user" 引用看到
```

这里仍然只有**一个对象**，但现在有**两个引用它的变量。**

我们可以通过其中**任意一个变量**来访问该对象并修改它的内容

<aside>
💡 就像我们有一个带有两把钥匙的柜子，使用其中一把钥匙（`obj`）打开柜子并更改了里面的东西。如果我们稍后用另一把钥匙（`newObj`），我们仍然可以打开同一个柜子并且可以访问更改的内容。

</aside>

对象什么时候相等？

仅当两个对象为**同一对象**时，两者才相等。

例如，这里 `obj` 和 `newObj` 两个变量都引用同一个对象，所以它们相等：

```jsx
let obj = {};
let newObj = obj; // 复制引用

alert( a == b ); // true，都引用同一对象
alert( a === b ); // true
```

而这里两个独立的对象则并不相等，即使它们都是空对象：

```jsx
let obj = {};
let newObj = {}; // 两个独立的对象

alert( obj == newObj ); // false
alert( obj === newObj ); // false
```

## 什么是浅拷贝？

创建一个新对象，这个对象有着源对象属性值的一份精确拷贝。如果属性是**基本类型**，拷贝的就是**基本类型的值**，如果属性是**引用类型**，拷贝的就是**内存地址** ，**所以如果其中一个对象改变了这个地址，就会影响到另一个对象。**

## 什么是深拷贝？

将一个对象从内存中完整的拷贝一份出来，从堆内存中开辟一个新的区域存放新对象，**且修改新对象不会影响原对象。**

<aside>
💡 **Hax**：JS里没有什么深拷贝的api，因为**不知道有多深**。。。

</aside>

# 浅拷贝

## Object.assign()

> **`Object.assign(**target,source**)**` 方法将所有[可枚举](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable)（`Object.propertyIsEnumerable()` 返回 `true`）和自有（`Object.hasOwnProperty()` 返回 `true`）属性从一个或多个源对象复制到目标对象，返回修改后的对象。 针对深拷贝, 需要使用其他办法，因为 `Object.assign()` 只复制属性值。**假如源对象是一个对象的引用，它仅仅会复制其引用值**。——MDN
>

```jsx
const obj = { a: 1, b: 2, c: { name: "c" } };
const newObj = Object.assign({}, obj);

obj.a = 3;
obj.c.name = "d";

console.log(obj); // {a: 3, b: 2,c: {name: 'd'}}
console.log(newObj); // {a: 1, b: 2,c: {name: 'd'}}
```

## 扩展运算符

> 扩展运算符 **(Spread syntax)**可以在函数调用/数组构造时，将数组表达式或者 string 在语法层面展开；还可以在构造**字面量**对象时，将对象表达式按 key-value 的方式展开。(字面量一般指 `[1, 2, 3]` 或者 `{name: "mdn"}` 这种**简洁的构造方式**)  ——MDN
>

```jsx
const obj = { a: 1, b: 2, c: { name: "c" } };
const newObj = { ...obj };

obj.a = 3;
obj.c.name = "d";

console.log(obj); // {a: 3, b: 2,c: {name: 'd'}}
console.log(newObj); // {a: 1, b: 2,c: {name: 'd'}}
```

<aside>
💡 实际上，展开语法和 `[Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)` 行为一致，执行的都是浅拷贝 (**只遍历一层**)。

</aside>

## JSON方法

> **JSON**（JavaScript Object Notation）是表示值和对象的通用格式。在 **RFC 4627** 标准中有对其的描述。最初它是为 JavaScript 而创建的，**但许多其他编程语言也有用于处理它的库。**因此，当客户端使用 JavaScript 而服务器端是使用 Ruby/PHP/Java 等语言编写的时，使用 JSON 可以很容易地进行数据交换。——MDN
>

可以使用以下方法进行**浅拷贝**：

- `JSON.stringify` 将对象转换为 JSON。
- `JSON.parse` 将 JSON 转换回对象。

```jsx
const obj = { a: 1, b: 2, c: { name: "c" } };
const newObj = JSON.parse(JSON.stringify(obj));

obj.a = 3;
obj.c.name = "d";

console.log(obj); //{a: 3, b: 2, c: {name: 'd'}}
console.log(newObj); //{a: 1, b: 2, c: {name: 'c'}}
```

然而JSON 是**语言无关的纯数据规范**，因此一些特定于 JavaScript 的对象属性会被 `JSON.stringify` 跳过。

- 函数会丢失
- 存储 `undefined` 的属性。
- `RegExp` 类型或者 `Error` 类型的时候拷贝变成空对象
- `Date` 类型的属性在深拷贝之后会变成字符串
- `undefined` 类型、`Symbol` 类型的时候，深拷后会丢失
- 对象的属性（key）是 `Symbol` 类型的时候，拷贝之后会丢失
- `NaN`、`Infinity`、`-Infinity` ，拷贝之后会变成 `null`

```jsx
const obj = {
  sayHi() {
    alert("Hello");
  }, // 被忽略
  [Symbol("id")]: 123, // 被忽略
  something: undefined, // 被忽略
};

console.log(JSON.stringify(obj)); // {}（空对象）
```

<aside>
💡 `JSON**.**stringify` 对**循环引用**的对象进行操作会报错：`Uncaught TypeError: Converting circular structure to JSON`

</aside>

# 深拷贝

## webAPI

### **structuredClone()**

全局的 **`structuredClone`**方法使用**结构化克隆算法**将给定的值进行**深拷贝**，支持**循环引用。**

```jsx
const obj = { a: 1, b: 2, c: { name: "c" } };
obj.itself = obj;

const newObj = structuredClone(obj);

obj.a = 3;
obj.c.name = "d";

console.log(obj); //{a: 3, b: 2, c: {name: 'c'}, itself:{...}}
console.log(newObj); //{a: 1, b: 2, c: {name: 'c'}, itself:{...}}
```

**结构化克隆算法**

结构化克隆算法是[由 HTML5 规范定义](https://www.w3.org/html/wg/drafts/html/master/infrastructure.html#safe-passing-of-structured-data)的用于复制复杂 JavaScript 对象的算法。是调用`structuredClone()` 时内部使用的算法。用于在 [Workers](https://developer.mozilla.org/en-US/docs/Web/API/Worker)之间通过`[postMessage()](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage)` 传递数据，在 [IndexedDB](https://developer.mozilla.org/en-US/docs/Glossary/IndexedDB) 内储存对象，或者用于为其他API复制对象。

**不能用结构化克隆的东西**

- `[Error](https://developer.mozilla.org/zh-CN/JavaScript/Reference/Global_Objects/Error)` 以及 `[Function](https://developer.mozilla.org/zh-CN/JavaScript/Reference/Global_Objects/Function)` 对象是不能被结构化克隆算法复制的；如果你尝试这样子去做，这会导致抛出 `DATA_CLONE_ERR` 的异常。
- 企图去克隆 DOM 节点同样会抛出 `DATA_CLONE_ERR` 异常。
- 对象的某些特定参数也不会被保留
  - `[RegExp](https://developer.mozilla.org/zh-CN/JavaScript/Reference/Global_Objects/RegExp)` 对象的 `lastIndex` 字段不会被保留
  - 属性描述符，`setters` 以及 `getters`（以及其他类似元数据的功能）同样不会被复制。例如，如果一个对象用属性描述符标记为 `read-only`，它将会被复制为 `read-write`，因为这是默认的情况下。
  - **原形链**上的属性也不会被追踪以及复制。
  - `Symbol`

**对象说好的深不可测呢？**

```jsx
    const obj = {
      a: 1,
      b: 2,
      c: {},
    };
    obj.itself = obj;

    const getC = (obj) => {
      if (obj && obj.c) {
        return getC(obj.c);
      } else {
        return obj;
      }
    };

    for (let i = 0; i < 2107; i++) {
      const nc = getC(obj.c);
      if (!nc["c"]) {
        nc["c"] = { i: i };
      }
    }

    const newObj = structuredClone(obj);

    obj.a = 3;
    obj.c.name = "d";

    console.log(obj);
    console.log(newObj); //null 
```

<aside>
💡 自测 chrome 103 浏览器在对象嵌套**2107层**的时候会**深克隆**失败，返回 `null`，所以对象仍然是深不可测的。

</aside>

## 可参考的源码

### lodash

`lodash.cloneDeep` 源码

[lodash/cloneDeep.js at master · lodash/lodash](https://github.com/lodash/lodash/blob/master/cloneDeep.js)

### core-js polyfill

`structuredClone` 的 core-js polyfill

[GitHub - zloirock/core-js: Standard Library](https://github.com/zloirock/core-js#structuredclone)

## 实现

实现一个深拷贝方法，特别注意需要支持如下数据结构：

- 支持 `Date`
- 支持 `Symbol`
- 支持 `Function`
- 支持 `RegExp`

**环状引用**参考如下数据结构：

```jsx
const a = {};
a.a = a;
```

**代码示例：**

```jsx
function deepClone(obj) {
 // ...实现
}
```

### **基础版本**

深拷贝的核心就是**浅拷贝** + **递归**，即通过不断的递归到达对象的最里层，完成基本类型属性的拷贝。

先从最基础的深拷贝版本看起，即只考虑**数组**和**对象字面量**的情况：

```jsx
function deepClone(target){
    if(typeof target === 'object'){
        let cloneTarget = Array.isArray(target) ? []:{}
        Reflect.ownKeys(target).forEach(key => {
            cloneTarget[key] = deepClone(target[key])
        })
        return cloneTarget
    } else {
        return target
    }
}
```

根据初始传入的 `target` 是一个对象字面量还是数组，决定最终返回的 `cloneTarget` 是对象还是数组。接着遍历 `target` 的每一个自身可枚举属性，递归调用 `deepClone`，如果属性已经是基本类型，则直接返回；如果还是对象或者数组，就和初始的 `target` 进行一样的处理。最后，把处理好的结果一一拷贝给 `cloneTarget`。

<aside>
💡 `Object.keys(obj)`   对象上所有**可枚举**的 key                                                                       `Reflect.ownKeys(obj)`   对象上**所有的** key

</aside>

### **解决循环引用**

对于初次传入的对象或者数组，使用一个 **WeakMap** 记录当前目标和拷贝结果的映射关系，当检测到再次传入相同的目标时，不再进行重复的拷贝，而是直接从 WeakMap 中取出它对应的拷贝结果返回。

改进后的代码如下：

```jsx
function deepClone(target,map = new WeakMap()){
    if(typeof target === 'object'){
        let cloneTarget = Array.isArray(target) ? []:{}

        // 处理循环引用的问题
        if(map.has(target)){
           return map.get(target)
        } 
        map.set(target,cloneTarget)

        Reflect.ownKeys(target).forEach(key => {
            cloneTarget[key] = deepClone(target[key],map)
        })
        return cloneTarget
    } else {
        return target
    }
}
```

<aside>
💡 **Object ？Map ？ WeakMap ？**

</aside>

### **处理其它数据类型**

深拷贝对象，这个对象应该理解为引用类型，所以它其实还包括了很多种类：除了上面已经处理的**对象字面量**和**数组**，还有 **Set**、**Map**、**类数组对象**、**函数**、**基本类型的包装类型**等。

### **1. 类型判断函数**

为了更好地判断是**引用数据类型**还是**基本数据类型**，可以使用一个 `isObject` 函数：

```jsx
function isObject(o){
    return o !== null && (typeof o === 'object' || typeof o === 'function')
}
```

这样，所有的**对象字面量、数组、Set、Map、类数组对象、函数、基本类型的包装类型**等，都视为 **object**。

为了更准确地判断具体是什么数据类型，可以使用一个 `getType` 函数：

```jsx
function getType(o){
    return Object.prototype.toString.call(o).slice(8,-1)
}
// getType(1)      "Number"  ([object Number])
// getType(null)   "Null" ([object Null])
```

### 2. ****初始化函数****

之前深拷贝**对象字面量**或者**数组**的时候，首先会将最终返回的结果 `cloneTarget` 初始化为 `[]` 或者 `{}`。同样地，对于 **Set、Map 以及类数组对象**，也需要进行相同的操作，所以最好用一个函数统一实现 `cloneTarget` 的**初始化**。

```jsx
function initCloneTarget(target){
    return new target.constructor()
}
```

通过 `target.constructor` 可以获得传进来的**实例的构造函数**，利用这个构造函数新创建一个同类型的实例并返回。

### 3. ****处理可以继续遍历的对象：Set、Map、类数组对象****

处理 **Set** 和 **Map** 的流程基本和**对象字面量**以及**数组**差不多，但是不能采用直接赋值的方式，而要使用 `add` 方法或者 `set` 方法，所以稍微改进一下。至于**类数组对象**，其实和数组以及对象字面量的形式差不多，所以可以一块处理。

代码如下：

```jsx
function deepClone(target,map = new WeakMap()){
    // 如果是基本类型，直接返回即可
    if(!isObject(target))    return target   
    
    // 初始化返回结果
    let type = getType(target)
    let cloneTarget = initCloneTarget(target)
    
    // 处理循环引用
    if(map.has(target)){
        return map.get(target)
    } else {
        map.set(target,cloneTarget)
    }   
    
    // 处理 Set    
    if(type === 'Set'){
        target.forEach(value => {
            cloneTarget.add(deepClone(value,map))
        })
    }

    // 处理 Map
    else if(type === 'Map'){
        target.forEach((value,key) => {
            cloneTarget.set(key,deepClone(value,map))
        })
    }
    
    // 处理对象字面量、数组、类数组对象
    else if(type === 'Object' || type === 'Array' || type === 'Arguments'){
        Reflect.ownKeys(target).forEach(key => {
            cloneTarget[key] = deepClone(target[key],map)
        })
    }
    return cloneTarget
}
```

### 4. ****处理不能继续遍历的对象：函数、错误对象、日期对象、正则对象、基本类型的包装对象****

对于上面这些对象，我们不能像基本数据类型那样直接返回，否则将返回相同的引用，并没有达到拷贝的目的。正确的做法，**应该是拷贝一份副本，再直接返回**。

如何拷贝呢？这里又分为两种情况。其中，**String、Boolean、Number、错误对象、日期对象**都可以通过 **new 的方式返回一个实例副本**；而 **Symbol、函数、正则对象**的拷贝则**无法通过简单的 new 拷贝副本，需要单独处理。**

### 5. **拷贝 Symbol**

```jsx
function cloneSymbol(target){
    return new Object(target.valueOf())    
    // 或者
    return new Object(Symbol.prototype.valueOf.call(target)) //lodash
    // 或者
    return new Object(Symbol(target.description))
}
```

PS：这里的 `target` 是 Symbol 基本类型的包装类型，调用 `valueOf` 可以获得它对应的拆箱结果，再把这个拆箱结果传给 Object，就可以构造原包装类型的副本了；为了保险起见，可以通过 Symbol 的原型调用 `valueOf`；可以通过 description 获得 symbol 的描述符，基于此也可以构造原包装类型的副本。

### 6. **拷贝正则对象（参考 lodash 的做法）**

```jsx
function cloneReg(target) {
    const reFlags = /\w*$/;
    const result = new RegExp(target.source, reFlags.exec(target));
    result.lastIndex = target.lastIndex;
    return result;
}
```

### 7. **拷贝函数（实际上函数没有必要拷贝）**

```jsx
function cloneFunction(target){
    return eval(`(${target})`);
}
```

<aside>
💡 **`eval()`** 函数会将传入的字符串当做 JavaScript 代码进行执行。（**永远不要使用 eval！**）

</aside>

### 8. 结合以上

接下来，用一个 `directCloneTarget` 函数处理以上所有情况：

```jsx
function directCloneTarget(target,type){
    let constructor = target.constructor
 switch(type){
        case 'String':
        case 'Boolean':
        case 'Number':
        case 'Error':
        case 'Date':
         return new constructor(target.valueOf())
        case 'RegExp':
            return cloneReg(target)        
        case 'Symbol':
            return cloneSymbol(target)        
        case 'Function':            
            return cloneFunction(target) 
        default:            
            return null    
    }            
}
```

PS：注意这里有一些坑。

- 为什么使用 `return new _constructor(target.valueOf())` 而不是 `return new _constructor(target)` 呢？因为如果传进来的 `target` 是 `new Boolean(false)`，那么最终返回的实际上是 `new Boolean(new Boolean(false))` ，由于参数并非空对象，因此它的值对应的不是期望的 false，而是 true。所以，最好使用 `valueOf` 获得包装类型对应的真实值。
- 也可以不使用基本类型对应的构造函数 `_constructor`，而是直接 `new Object(target.valueOf())` 对基本类型进行包装
- 考虑到 `valueOf` 可能被重写，为了保险起见，可以通过基本类型对应的构造函数 `_constructor` 去调用 `valueOf` 方法

## ****最终版本****

```jsx
// 可以继续遍历的类型
const objectToInit = ['Object','Array','Set','Map','Arguments']

// 判断是否是引用类型
function isObject(o){
  return o !== null && (typeof o === 'object' || typeof o === 'function')
}
// 判断具体的数据类型
function getType(o){
  return Object.prototype.toString.call(o).slice(8,-1)
}
// 初始化函数
function initCloneTarget(target){
  return new target.constructor()
}
// 拷贝 Symbol
function cloneSymbol(target){
 return new Object(target.valueOf())    
    // 或者
    return new Object(Symbol.prototype.valueOf.call(target))
    // 或者
    return new Object(Symbol(target.description))
}
// 拷贝正则对象
function cloneReg(target) {
  const reFlags = /\w*$/;
  const result = new RegExp(target.source, reFlags.exec(target));
  result.lastIndex = target.lastIndex;
  return result;
}
// 拷贝函数
function cloneFunction(target){
  return eval(`(${target})`);
}

// 处理不能继续遍历的类型
function directCloneTarget(target,type){
  let constructor = target.constructor
  switch(type){
    case 'String':
    case 'Boolean':
    case 'Number':
    case 'Error':
    case 'Date':
      return new constructor(target.valueOf())
    case 'RegExp':
        return cloneReg(target)        
    case 'Symbol':
        return cloneSymbol(target)        
    case 'Function':            
        return cloneFunction(target) 
    default:            
        return null    
  }        
}

// 深拷贝的核心代码
function deepClone(target,map = new WeakMap()){
    if(!isObject(target)){
     return target    
    }   
    // 初始化
    let type = getType(target)
    let cloneTarget
    if(objectToInit.includes(type)){
        cloneTarget = initCloneTarget(target)
    } else {
        return directCloneTarget(target,type)
    }
  // 解决循环引用   
    if(map.has(target)){
      return map.get(target)

    }       
    map.set(target,cloneTarget)
    // 拷贝 Set
    if(type === 'Set'){
        target.forEach(value => {
            cloneTarget.add(deepClone(value,map))
        })
    }
    // 拷贝 Map
    else if(type === 'Map'){
        target.forEach((value,key) => {
            cloneTarget.set(key,deepClone(value,map))
        })
    }
    // 拷贝对象字面量、数组、类数组对象
    else if(type === 'Object' || type === 'Array' || type === 'Arguments'){
        Reflect.ownKeys(target).forEach(key => {
            cloneTarget[key] = deepClone(target[key],map)
        })
    }
    return cloneTarget
}
```

## 测试用例

```jsx
let obj = {
  name: '小明',
  age: 24,
  male: true,
  birthday: new Date('1998-04-21'),
  skills: ['唱','跳','rap','篮球'],
  classmate: [
    {
      name: '小红',
      age: 24
    },
    {
      name: '小张',
      age: 24
    },
  ],
  map: new Map([['a', 1,'b',2]]),
  set: new Set([1,2,3,4,5]),
  getName: function(){
    console.log(`名字是${this.name}`)
  },
  reg : /\d+/gi,
  error : new Error(),
  a: undefined,
  b: Symbol(),
  [Symbol()]: 123,
  c:NaN,
  d:Infinity,
  e:-Infinity,
}
obj.f = obj
let cloneObj = deepClone(obj)

obj.age = 100
obj.classmate[1].age = 100
obj.set.add('1123')
obj.skills.push('计算机')
obj.name = '小梅'
obj.map.set('name', '小明')

console.log(obj)
console.log(cloneObj)
obj.getName()
cloneObj.getName()
```

经过测试，测试结果满足以下条件：

1. 修改原对象的任意属性，拷贝对象不受影响，反之亦然。符合深拷贝的定义
2. 支持拷贝如下数据结构：`Date`、`Symbol`、`Function`、`RegExp` 等
3. 环状引用不会报错
4. `Date` 类型的属性在深拷贝之后不会变成字符串（`JSON.parse(JSON.stringify(obj))` 深拷贝存在的问题）
5. 对象属性值是 `RegExp` 类型或者 `Error` 类型的时候，深拷贝之后不会变成空对象（`JSON.parse(JSON.stringify(obj))` 深拷贝存在的问题）
6. 对象属性值是函数类型、 `undefined` 类型、`Symbol` 类型的时候，深拷贝之后不会丢失（`JSON.parse(JSON.stringify(obj))` 深拷贝存在的问题）
7. 对象的属性（key）是 `Symbol` 类型的时候，深拷贝之后不会丢失（`JSON.parse(JSON.stringify(obj))` 深拷贝存在的问题）
8. 对象的属性值是 `NaN`、`Infinity`、`-Infinity` 的时候，深拷贝之后不会变成 `null`（`JSON.parse(JSON.stringify(obj))` 深拷贝存在的问题）

> 参考：
>

[https://segmentfault.com/a/1190000039862872](https://segmentfault.com/a/1190000039862872)

[https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Structured_clone_algorithm](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)

[https://developer.mozilla.org/zh-CN/docs/Web/API/structuredClone](https://developer.mozilla.org/zh-CN/docs/Web/API/structuredClone)

[https://github.com/BetaSu/fe-hunter/issues/53](https://github.com/BetaSu/fe-hunter/issues/53)
