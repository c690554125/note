# 对象扩展
ES6对象扩展的内容

## 属性简洁表示
```js
  // 简写属性
  let name = 'hello'
  const obj = {
    name
  }
  // 相当于
  const obj = {
    name: name
  }

  // 简写方法
  const fn = {
    method() {
      return 'method'
    }
  }
  // 相当于
  const fn = {
    method: function(){
      return 'method
    }
  }
```
属性的存取器属性`setter`和`getter`实际上也是采用了简写方式.
```js
  const cart = {
    _wheels: 4,
    get wheels() {
      return this._wheels
    },
    set wheels(val) {
      this._wheels = val
    }
  }
```
## 属性名表达式
```js
  // 用作属性
  let foo = 'name'
  let obj = {
    name: 'jack'
  }
  console.log(obj[foo]) // jack
```

```js
  // 用作函数名
  let baz = 'test'
  let obj = {
    test() {
      console.log('test')
    }
  }
  obj[baz]()
```

## Object.is方法
判断2个值是否相等，如果是引用类型，比较指针。
```js
  Object.is('foo', 'foo') // true

  Object.is({}, {}) // false

  Object.is(+0, -0) // false

  Object.is(NaN, NaN) // true
```

## Object.assign方法
`Object.assign(target, ...sources)`，同名属性合并，后者覆盖前者。
```js
  let a = {a: 1}
  let b = {b: 2}
  let c = {a: 2, b: 3, c: 4}
  Object.assign(a, b, c) // {a: 2, b: 3, c: 4}
```
*注意*，这里是浅复制，如果某个属性的值是对象，则拷贝的是引用。
*注意*，只拷贝源对象自身属性，只拷贝可枚举的属性
*注意*，无法处理set和get.（使用`getOwnPropertyDescriptors`配合`defineProperties`，可以实现正确复制get,set）

```js
  // 同名属性替换需注意的
  // 会直接替换掉同名的引用属性，而不是添加，记得上面所说的浅复制
  let a = {a: {b: 'c', d: 'e'}}
  let b = {a: {b: 'hello'}}
  Object.assign(a, b) // {a: {b: 'hello'}}
```

```js
  // 处理数组
  Object.assign([1,2,3], [4,5])
  // 当成对象处理
  // 相当于
  Object.assign({
    0: 1,
    1: 2,
    2: 3
  }, {
    0: 4,
    1: 5
  }) // [4,5,3]
```

## 属性的可枚举性和遍历
**忽视可枚举性（enumerable为false）的几个操作**：`for..in`, `Object.keys()`, `JSON.stringify()`, `Object.assign()`

**遍历属性的5种方法**
`for...in`：循环遍历对象自身和继承的可枚举属性，不含Symbol属性
`Object.keys(obj)`： 返回一个数组，包含自身的可枚举属性（不含Symbol属性）的键名
`getOwnPropertyNames(obj)`：返回一个数组，包含对象自身的所有属性（不含Symbol属性，但包含不可枚举）的键名
`Object.getOwnPropertySymbols(obj)`：返回一个数组，包含自身的所有Symbol属性的键名
`Reflect.ownKeys(obj)`：返回一个数组，包含对象自身的所有键名，包含Symbol和不可枚举属性

## Object.getOwnPropertyDescriptors()
ES2017中新增的方法，多一个s，返回一个对象，对象包含了该对象中所有属性描述符。
```js
  const obj = {
    foo: 123,
    get bar() {
      return 'abc'
    }
  }
  Object.getOwnPropertyDescriptors(obj)
```

## 操作原型对象__proto__
`__proto__`只是浏览器普遍支持的一个私有属性，只部署在浏览器环境中。非标准，因此ES6为操作原型提供了如下方法
`Object.getPrototypeOf(obj)`：获取原型对象
`Object.setPrototypeOf(obj, prototype)`：设置某个对象的原型

## super关键字
`this`大家都知道用来指向函数所被调用时的对象。`super`则用来指向当前对象的原型对象
```js
  const proto = {
    foo: 'hello'
  }

  const obj = {
    foo: 'world',
    find() {
      return super.foo
    }
  }

  Object.setPrototypeOf(obj, proto)
  obj.find() // hello 因为这里的super指代obj的原型，而在上一步骤，我们设置了其原型对象未proto
```

## 对象的遍历keys,values,entries
`Object.keys(obj)`：返回数组，包含可遍历的自身属性的键
`Object.values(obj)`: 返回数组，包含可遍历的自身属性的值
`Object.entries(obj)`：返回数组，包含可遍历的自身属性的键值对数组 [[key: val], [key: val]]

## ES2018增加了对象的扩展运算符...
*注意*解构的必须是最后一个参数，否则会报错
```js
  let {x, y, ...z} = {x: 1, y: 2, a: 3, b: 4}
  console.log(z) // {a: 3, b: 4}
```
可以利用`...`实现对象的拷贝，类似`Object.assign()`
```js
  let z = {a: 3, b: 4};
  let n = {...z};
  console.log(n) // {a: 3, b: 4}
```
具体详细的看下阮一峰著《ES6入门》。