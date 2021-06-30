---
title: JS 一
date: 2019-11-03 14:43:50
categories:
  - 面试
tags:
  - Js
  - 面试
---

**JS 面试总结分为五部分，本节为第一部分：**

### 1.字符串方法

**所有字符串方法都会返回新字符串。它们不会修改原始字符串。**

**正式地说：字符串是不可变的：字符串不能更改，只能替换。**

#### 1.1 indexOf 与 lastIndexOf

- indexOf() 方法返回字符串中指定文本*首次*出现的索引（位置）
- lastIndexOf() 方法返回指定文本在字符串中*最后*一次出现的索引

#### 1.2 search

- search() 方法搜索特定值的字符串，并返回匹配的位置

```javascript
var str = "The full name of China is the People's Republic of China."
var pos = str.search('China')
console.log(pos) // 17
```

- indexOf() 与 search() 这两种方法是不相等的。区别在于：
  - search() 方法无法设置第二个开始位置参数。
  - indexOf() 方法无法设置更强大的搜索值（正则表达式）。

<!--more-->

#### 1.3 slice、substring 与 substr

- **slice()** 提取字符串的某个部分并在新字符串中返回被提取的部分。

  该方法设置两个参数：起始索引（开始位置），终止索引（结束位置）。

  如果某个参数为负，则从字符串的结尾开始计数。

- **substring()** 类似于 slice()。

  不同之处在于 substring() 无法接受负的索引。

- **substr()** 类似于 slice()。

  不同之处在于第二个参数规定被提取部分的*长度*。

#### 1.4 replace

- replace() 方法用另一个值替换在字符串中指定的值

  ```javascript
  str = 'Please visit Microsoft!'
  var n = str.replace('Microsoft', 'W3School')
  console.log(n) // "Please visit W3School!"
  console.log(str) // "Please visit Microsoft!"
  ```

- replace() 方法不会改变调用它的字符串。它返回的是新字符串。

  默认地，replace()只替换**首个**匹配

- 默认地，replace() 对大小写敏感。

  如需执行大小写不敏感的替换，请使用正则表达式 /i（大小写不敏感）

  ```javascript
  str.replace(/MICROSOFT/i, 'W3School')
  ```

- 如需替换所有匹配，请使用正则表达式的 g 标志（用于全局搜索）

#### 1.5 toUpperCase 与 toLowerCase

- 通过 toUpperCase() 把字符串转换为大写
- 通过 toLowerCase() 把字符串转换为小写

#### 1.6 concat

- concat() 连接两个或多个字符串

#### 1.7 String.trim()

- trim() 方法删除字符串两端的空白符

#### 1.8 charAt 与 charCodeAt

- charAt() 方法返回字符串中指定下标（位置）的字符串

  ```javascript
  var str = 'HELLO WORLD'
  str.charAt(0) // 返回 H
  ```

- charCodeAt() 方法返回字符串中指定索引的字符 unicode 编码

  ```javascript
  var str = 'HELLO WORLD'
  str.charCodeAt(0) // 返回 72
  ```

#### 1.9 split

- 可以通过 split() 将字符串转换为数组

  ```javascript
  var str = 'Hello'
  var arr1 = str.split('')
  var arr2 = str.split()
  console.log(arr1) // ["H","e","l","l","o"]
  console.log(arr2) // ["Hello"]
  ```

- 如果省略分隔符，被返回的数组将包含 index [0] 中的整个字符串。

  如果分隔符是 ""，被返回的数组将是间隔单个字符的数组：

可以将字符串转为真正的数组

```javascript
;[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

### 2.数组方法

#### 2.1 push 与 pop

- **push** 向数组尾部添加一个元素，返回值为新数组的长度，改变原数组

- **pop** 删除数组中最后一个元素，返回值为被删除的元素，改变原数组

#### 2.2 unshift 与 shift

- **unshift** 向数组开头添加一个元素，返回值为新数组的长度，改变原数组

- **shift** 删除数组中第一个元素，返回值为被删除的元素，改变原数组

#### 2.3 splice 与 join

##### 2.3.1 **splice**

```javascript
var fruits = ['Banana', 'Orange', 'Apple', 'Mango']
fruits.splice(2, 0, 'Lemon', 'Kiwi')
```

- 第一个参数（2）定义了应添加新元素的位置（拼接）。

- 第二个参数（0）定义应删除多少元素。

- 其余参数（“Lemon”，“Kiwi”）定义要添加的新元素。

- splice() 方法返回一个包含已删除项的数组，改变原数组

##### 2.3.2 **join**

join() 方法用于把数组中的所有元素放入一个字符串。

元素是通过指定的分隔符进行分隔的。

#### 2.4 concat

```javascript
var myGirls = ['Emma', 'Isabella']
var myBoys = ['Jacob', 'Michael', 'Ethan']
var myChildren = myGirls.concat(myBoys)

console.log(myChildren) // ["Emma", "Isabella", "Jacob", "Michael", "Ethan"]
console.log(myGirls) // ["Emma", "Isabella"]
```

- concat() 方法不会更改现有数组。它总是返回一个新数组

#### 2.5 slice

```javascript
var fruits = ['Banana', 'Orange', 'Lemon', 'Apple', 'Mango']
var citrus = fruits.slice(3)
console.log(fruits) // ["Banana", "Orange", "Lemon", "Apple", "Mango"]
console.log(citrus) // ["Apple", "Mango"]
```

- slice() 方法创建新数组， 可接受两个参数，比如 (1, 3)。

  该方法会从开始参数选取元素，直到结束参数（不包括）为止。

- 它不会从源数组中删除任何元素。

#### 2.6 toString

- 所有 JavaScript 对象都拥有 toString() 方法。

```javascript
var fruits = ['Banana', 'Orange', 'Apple', 'Mango']
console.log(fruits.toString()) // Banana,Orange,Apple,Mango
```

#### 2.7 sort 数组排序

```javascript
var fruits = ['Banana', 'Orange', 'Apple', 'Mango']
console.log(fruits.sort()) // ["Apple", "Banana", "Mango", "Orange"]
```

- sort() 方法以字母顺序对数组进行排序。

- 如果数字按照字符串来排序，则 "25" 大于 "100"，因为 "2" 大于 "1"。

  正因如此，sort() 方法在对数值排序时会产生不正确的结果

- 我们通过一个*比值函数*来修正此问题：（升序）

  ```javascript
  var points = [40, 100, 1, 5, 25, 10]
  points.sort(function (a, b) {
    return a - b
  }) // a为100，b为40，依次往下
  console.log(points) // [1, 5, 10, 25, 40, 100]
  ```

- 降序

  ```javascript
  var points = [40, 100, 1, 5, 25, 10]
  points.sort(function (a, b) {
    return b - a
  })
  console.log(points) // [100, 40, 25, 10, 5, 1]
  ```

- 比值函数

  ```javascript
  function(a, b){return a-b}
  ```

  比较函数的目的是定义另一种排序顺序。

  比较函数应该返回一个负，零或正值，这取决于参数。

  当 sort() 函数比较两个值时，会将值发送到比较函数，并根据所返回的值（负、零或正值）对这些值进行排序。

  ```javascript
  points.sort(function (a, b) {
    return 0.5 - Math.random()
  }) // 随机排序
  ```

- 排序对象数组

  ```javascript
  var cars = [
    { type: 'Volvo', year: 2016 },
    { type: 'Saab', year: 2001 },
    { type: 'BMW', year: 2010 }
  ]
  cars.sort(function (a, b) {
    return a.year - b.year
  })
  ```

#### 2.8 reverse() 反转数组

```javascript
var fruits = ['Banana', 'Orange', 'Apple', 'Mango']
fruits.sort() // 对 fruits 中的元素进行排序
//["Apple", "Banana", "Mango", "Orange"]
fruits.reverse() // 反转元素顺序
//["Orange", "Mango", "Banana", "Apple"]
```

#### 2.9 Math.max()与 Math.min()

- Math.max.apply(null,[1, 2, 3]) 等于 Math.max(1, 2, 3)。

#### 2.10 数组迭代方法

##### 2.10.1 Array.forEach()

- forEach() 方法为每个数组元素调用一次函数（回调函数）
- 该函数接受 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.2 Array.map()

- map() 方法通过对每个数组元素执行函数来创建新数组。
- map() 方法不会对没有值的数组元素执行函数。
- map() 方法不会更改原始数组。
- 该函数有 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.3 Array.filter()

- filter() 方法创建一个包含通过测试的数组元素的新数组。
- 请注意此函数接受 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.4 Array.reduce()

reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。

###### 2.10.4.1 语法

```javascript
arr.reduce(function(prev,cur,index,arr){
...
}, init);
```

**arr** 表示原数组；
**prev** 表示上一次调用回调时的返回值，或者初始值 init;
**cur** 表示当前正在处理的数组元素；
**index** 表示当前正在处理的数组元素的索引，若提供 init 值，则索引为 0，否则索引为 1；
**init** 表示初始值。

###### 2.10.4.2 实例

先提供一个原始数组：

```javascript
var arr = [3, 9, 4, 3, 6, 0, 9]
```

实现以下需求的方式有很多，其中就包含使用 reduce()的求解方式，也算是实现起来比较简洁的一种吧。

- 求数组项之和

```javascript
var sum = arr.reduce(function (prev, cur) {
  return prev + cur
}, 0)
```

由于传入了初始值 0，所以开始时 prev 的值为 0，cur 的值为数组第一项 3，相加之后返回值为 3 作为下一轮回调的 prev 值，然后再继续与下一个数组项相加，以此类推，直至完成所有数组项的和并返回。

- 求数组项最大值

```javascript
var max = arr.reduce(function (prev, cur) {
  return Math.max(prev, cur)
})
```

由于未传入初始值，所以开始时 prev 的值为数组第一项 3，cur 的值为数组第二项 9，取两值最大值后继续进入下一轮回调。

- 数组去重

```javascript
var newArr = arr.reduce(function (prev, cur) {
  prev.indexOf(cur) === -1 && prev.push(cur)
  return prev
}, [])
```

- 实现的基本原理如下：

> ① 初始化一个空数组
> ② 将需要去重处理的数组中的第 1 项在**初始化数组**中查找，如果找不到（空数组中肯定找不到），就将该项添加到**初始化数组**中
> ③ 将需要去重处理的数组中的第 2 项在**初始化数组**中查找，如果找不到，就将该项继续添加到**初始化数组**中
> ④ ……
> ⑤ 将需要去重处理的数组中的第 n 项在**初始化数组**中查找，如果找不到，就将该项继续添加到**初始化数组**中
> ⑥ 将这个**初始化数组**返回

###### 2.10.4.3 常见用法

- 数组求和

```javascript
const arr = [12, 34, 23];
const sum = arr.reduce((total, num) => total + num);
<!-- 设定初始值求和 -->
const arr = [12, 34, 23];
const sum = arr.reduce((total, num) => total + num, 10);  // 以10为初始值求和
<!-- 对象数组求和 -->
var result = [
  { subject: 'math', score: 88 },
  { subject: 'chinese', score: 95 },
  { subject: 'english', score: 80 }
];
const sum = result.reduce((accumulator, cur) => accumulator + cur.score, 0);
const sum = result.reduce((accumulator, cur) => accumulator + cur.score, -10);  // 总分扣除10分
```

- 数组最大值

```javascript
const a = [23, 123, 342, 12]
const max = a.reduce(function (pre, cur, inde, arr) {
  return pre > cur ? pre : cur
}) // 342
```

###### 2.10.4.4 进阶用法

- 数组对象中的用法

```javascript
<!-- 比如生成“老大、老二和老三” -->
```

```javascript
const objArr = [{ name: '老大' }, { name: '老二' }, { name: '老三' }]
const res = objArr.reduce((pre, cur, index, arr) => {
  if (index === 0) {
    return cur.name
  } else if (index === arr.length - 1) {
    return pre + '和' + cur.name
  } else {
    return pre + '、' + cur.name
  }
}, '')
```

- 求字符串中字母出现的次数

```javascript
const str = 'sfhjasfjgfasjuwqrqadqeiqsajsdaiwqdaklldflas-cmxzmnha'
const res = str.split('').reduce((accumulator, cur) => {
  accumulator[cur] ? accumulator[cur]++ : (accumulator[cur] = 1)
  return accumulator
}, {})
```

- 数组转数组

```javascript
<!-- 按照一定的规则转成数组 -->
var arr1 = [2, 3, 4, 5, 6]; // 每个值的平方
var newarr = arr1.reduce((accumulator, cur) => {accumulator.push(cur * cur); return accumulator;}, []);
```

- 数组转对象

```javascript
<!-- 按照id 取出stream -->
var streams = [{name: '技术', id: 1}, {name: '设计', id: 2}];
var obj = streams.reduce((accumulator, cur) => {accumulator[cur.id] = cur; return accumulator;}, {});
```

- 扁平一个二维数组

```javascript
var arr = [
  [1, 2, 8],
  [3, 4, 9],
  [5, 6, 10]
]
var res = arr.reduce((x, y) => x.concat(y), [])
```

##### 2.10.5 Array.every()

- every() 方法检查所有数组值是否通过测试。

  这个例子检查所有数组值是否大于 18：

```javascript
var numbers = [45, 4, 9, 16, 25]
var allOver18 = numbers.every(myFunction)

console.log(allOver18) // false

function myFunction(value, index, array) {
  return value > 18
}
```

- 请注意此函数接受 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.6 Array.some()

- some() 方法检查某些数组值是否通过了测试。

  这个例子检查某些数组值是否大于 18：

```javascript
var numbers = [45, 4, 9, 16, 25]
var someOver18 = numbers.some(myFunction)

console.log(someOver18) // true

function myFunction(value, index, array) {
  return value > 18
}
```

- 请注意此函数接受 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.7 Array.indexOf()

- indexOf() 方法在数组中搜索元素值并返回其位置。

  **注释：**第一个项目的位置是 0，第二个项目的位置是 1，以此类推。

```javascript
var fruits = ['Apple', 'Orange', 'Apple', 'Mango']
var a = fruits.indexOf('Apple')
console.log(a) // 0
```

##### 2.10.8 Array.find()

- find() 方法返回通过测试函数的第一个数组元素的值。

  这个例子查找（返回）大于 18 的第一个元素的值：

```javascript
var numbers = [4, 9, 16, 25, 29]
var first = numbers.find(myFunction)
console.log(a) // 25

function myFunction(value, index, array) {
  return value > 18
}
```

- 请注意此函数接受 3 个参数：
  - 项目值
  - 项目索引
  - 数组本身

##### 2.10.9 Array.findIndex()

- findIndex() 方法返回通过测试函数的第一个数组元素的索引。
