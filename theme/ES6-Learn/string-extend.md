# 字符串扩展
ES6针对字符串做了一些新的扩展，比如API。本文只记录一些常用的API。

## Unicode表示法
JS允许使用`\uxxxx`表示字符，其中`xxxx`是字符Unicode的码点。该方法只允许表示`\uxxxx~\uFFFF`之间的双字节字符。
如下例子特殊的字符则必须使用2个双字节表示。超过4个码点的如`\u20BB7`无法正常表示，这里会展示一个乱码加数字7。

ES6中可以将码点放入大括号，即可正常解读该文字。`\u{20BB7}`

提供了新API`codePointAt()`，获取4个字节存储的十进制码点。
```js
  var s = "𠮷";

  s.length // 2
  s.charAt(0) // ''
  s.charAt(1) // ''
  s.charCodeAt(0) // 55362
  s.charCodeAt(1) // 57271
```

提供了新API`fromCodePoint`，将码点返回对应字符，可识别超出`0xFFFF`范围的字符
```js
  String.fromCodePoint(0x20BB7)
```

## 字符串增加了遍历器接口Iterator
```js
  for (let s of 'foo') {
    console.log(s)
  }
  // 'f' 'o' 'o'
```

## includes()，startsWith(), endsWith()
首先，不是include，startWith，endWith，都要加`s`。
```js
  let str = 'abc'
  str.includes('a') // 返回布尔值，是否存在a
  str.endsWith('a') // 返回布尔值，是否以a结束
  str.startsWith('a') // 返回布尔值，是否以a开始
```

## 's'.repeat(n)
返回一个新的字符串，s重复n次的字符串。
`注意`，字符串是不允许改变的，所以字符串的方法都是返回新字符串。
```js
  let r = 's'.repeat(3); // sss
```

## padStart(), padEnd()
字符串的开头和结尾，以某字符开始补齐。
```js
  let a = 'abc'
  a.padStart(6, 'z') // zzzabc 字符串长度补到6为止，以z补在开头部分
  a.padEnd(6, 'a') // abcaaa 字符串长度补到6为止，以a补在结尾部分
```

## 模板字符串
以```作为模板字符串的起始和结束位`。变量以`${x}`表示。
${}内部还接受一些表达式，可以运算，功能很强大。
```js
  let a = '名字'
  let b = 'Mike'
  `我的${a}是${b}`
```