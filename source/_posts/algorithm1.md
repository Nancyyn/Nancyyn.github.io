---
title: 算法 一
date: 2019-11-04 17:29:22
categories:
  - 算法
tags:
  - 算法
  - 面试
---

**算法总结分为两部分，本节为第一部分：**

### 1.写出打印结果，并解释为什么

```javascript
var a = { k1: 1 }
var b = a
a.k3 = a = { k2: 2 }
console.log(a) // ?
console.log(b) // ?
```

打印结果为：

```javascript
{k2: 2}
{k1: 1, k3: {k2: 2}}
```

- **关键点**

> 点的优先级大于等号的优先级
> 对象以指针的形式进行存储，每个新对象都是一份新的存储地址

- **分析**

> var b = a；b 和 a 都指向同一个地址。  
> .的优先级高于=。所以先执行 a.k3，于是现在的 a 和 b 都是{k1:1,k3:undefined}。  
> =是从右向左执行。所以是执行 a={k2:2}，于是 a 指向了{k2:2}。  
> 在执行 a.k3=a。这里注意，a.k3 是最开始执行的，已经是{k1:1,k3:undefined}这个地址了，而不是最一开始的那个 a，所以也就不是{k2:2}了。而且 b 和旧的 a 是指向一个地址的，所以 b 也改变了。  
> 但是，=右面的 a，是已经指向了新地址的新 a。  
> 所以，a.k3=a 可以看成是{k1:1,k3:undefined}.k3={k2:2}。

<!--more-->

### 2.实现 add(1)(2)(3)

- **考点：函数柯里化**
  函数柯里化概念：柯里化(Currying)是把接受多个参数的函数转变为接受一个单一参数的函数，并且返回接受余下的参数且返回结果的新函数的技术。

- **参数长度不固定**

```javascript
function add(...args) {
  //求和
  return args.reduce((a, b) => a + b)
}

function currying(fn) {
  let args = []
  return function temp(...newArgs) {
    // newArgs 为 Arguments，依次为addCurry的参数[1]、[2]、[3]、[4,5]、[](以第一个console.log为例)
    if (newArgs.length) {
      args = [...args, ...newArgs]
      return temp
    } else {
      let val = fn.apply(this, args)
      args = [] //保证再次调用时清空
      return val
    }
  }
}

let addCurry = currying(add)
console.log(addCurry(1)(2)(3)(4, 5)()) //15
console.log(addCurry(1)(2)(3, 4, 5)()) //15
console.log(addCurry(1)(2, 3, 4, 5)()) //15
```

### 3.写一个 mySetInterVal(fn,a,b)，每次间隔 a,a+b,a+2b，然后写一个 myClear，停止上面的 mySetInterVal

```javascript
function mySetInterVal(fn, a, b) {
  this.a = a
  this.b = b
  this.time = 0
  this.handle = -1
  this.start = () => {
    this.handle = setTimeout(() => {
      fn()
      this.time++
      this.start()
      console.log(this.a + this.time * this.b)
    }, this.a + this.time * this.b)
  }

  this.stop = () => {
    clearTimeout(this.handle)
    this.time = 0
  }
}

var a = new mySetInterVal(
  () => {
    console.log('123')
  },
  1000,
  2000
)
a.start()
a.stop()
```

### 4.获取字符串中出现最多的字母

```javascript
const str1 = 'afjghdfraaaasdenas'
let arr = str1.split('')
let result = arr.reduce((pre, cur) => {
  pre[cur] ? pre[cur]++ : (pre[cur] = 1)
  return pre
}, {})
// result {a:6,f:2,j:1,g:1,h:1,d:2,r:1,s:2,e:1,n:1}

let numArr = Object.values(result) // [6,2,1,1,1,2,1,2,1,1]
let letterArr = Object.keys(result) // ['a','f','j','g','h','d','r','s','e','n']

// 获取最大值方法1
let maxNum = numArr.reduce((pre, cur) => {
  return Math.max(pre, cur)
}) // 6

// 获取最大值方法2
let maxNum = Math.max(...numArr) // 6

// 获取最大值方法3
let maxNum = Math.max.apply(Math, numArr) // 6

let resultLetter = letterArr[numArr.indexOf(maxNum)] // a
```

### 5. 判断一个单词是否是回文？

> 回文是指把相同的词汇或句子，在下文中调换位置或颠倒过来，产生首尾回环的情趣，叫做回文，也叫回环。比如 mamam redivider .

很多人拿到这样的题目非常容易想到用 for 将字符串颠倒字母顺序然后匹配就行了。其实重要的考察的就是对于 reverse 的实现。其实我们可以利用现成的函数，将字符串转换成数组，这个思路很重要，我们可以拥有更多的自由度去进行字符串的一些操作。

```javascript
function checkPalindrom(str) {
  return str == str.split('').reverse().join('')
}
```

### 6. 数组去重

- 方法一

```javascript
const arr = [1, 13, 24, 11, 11, 14, 1, 2]
let unique = function (arr) {
  let hashTable = {}
  let data = []
  for (let i = 0, l = arr.length; i < l; i++) {
    if (!hashTable[arr[i]]) {
      hashTable[arr[i]] = true
      data.push(arr[i])
    }
  }
  console.log(hashTable, 'hashTable')
  // {1: true, 2: true, 11: true, 13: true, 14: true, 24: true}
  return data
}
console.log(unique(arr)) // [1, 13, 24, 11, 14, 2]
```

- 方法二，给数组原型添加 unique 方法，那么数组就可以在任何地方像用 push 的方法一样，直接使用

```javascript
Array.prototype.unique = function () {
  let temp = {},
    newArr = []
  for (let item of this) {
    if (!temp[item]) {
      temp[item] = 'abc'
      newArr.push(item)
    }
  }
  return newArr
}
const arr = [1, 13, 24, 11, 11, 14, 1, 2]
console.log(arr.unique())
```

- 方法三， 使用 ES6 是 Set 数据结构没有重复元素的特性

```javascript
const arr = [1, 13, 24, 11, 11, 14, 1, 2]
let newArr = [...new Set(arr)]
console.log(newArr)
```
