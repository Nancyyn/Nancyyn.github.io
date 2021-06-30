---
title: 算法 二
date: 2019-11-04 17:32:22
categories:
  - 算法
tags:
  - 算法
  - 面试
---

**算法总结分为两部分，本节为第二部分：**

### 7. 数组排序

#### 7.1 冒泡排序（Bubble Sort）

1. 冒泡排序它在所有排序算法中最简单。然而， 从运行时间的角度来看，冒泡排序是最差的一个，它的**复杂度是 O(n2)**。
2. 冒泡排序比较任何两个相邻的项，如果第一个比第二个大，则交换它们。元素向上移动至正确的顺序，就好像**气泡升至表面**一样，冒泡排序因此得名。
3. 交换时，我们用一个中间值来存储某一交换项的值。其他排序法也会用到这个方法，因此我们声明一个方法放置这段交换代码以便重用。使用 ES6（ECMAScript 2015）**增强的对象属性——对象数组的解构赋值语法**，这个函数可以写成下面 这样：

```javascript
;[array[index1], array[index2]] = [array[index2], array[index1]]
```

<!--more-->

具体实现：

```javascript
function bubbleSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    //外循环（行{2}）会从数组的第一位迭代 至最后一位，它控制了在数组中经过多少轮排序
    for (let j = 0; j < arr.length - i; j++) {
      //内循环将从第一位迭代至length - i位，因为后i位已经是排好序的，不用重新迭代
      if (arr[j] > arr[j + 1]) {
        //如果前一位大于后一位
        ;[arr[j], arr[j + 1]] = [arr[j + 1], arr[j]] //交换位置
      }
    }
  }
  return arr
}
```

辅助草图：![bubbleSort1](bubbleSort1.png)

**冒泡排序是稳定的排序算法，它的最差时间复杂度 o(n^2) 即已排序数组；平均时间复杂度 o(n^2)，最优时间复杂度 o(n)即在内部循环第一次运行时，使用一个旗标来表示该数组是不是已经是最小的了。**

将上面代码做如下改动

```javascript
/**
 * @param    {nums} 未排序数组
 * @return   {nums} 已排序数组
 * @description 冒泡排序
 */
function bubbleSort(nums) {
  do {
    var swapped = false
    for (var i = 0; i < nums.length; i++) {
      if (nums[i] > nums[i + 1]) {
        var temp = nums[i]
        nums[i] = nums[i + 1]
        nums[i + 1] = temp
        swapped = true
      }
    }
  } while (swapped)
}
```

辅助草图：![bubbleSort2](bubbleSort2.png)

#### 7.2 快速排序

"快速排序"的思想很简单，整个排序过程只需要三步：

> 1. 在数据集之中，选择一个元素作为"基准"（pivot）。
>
> 2. 所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。
>
> 3. 对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

快速排序的平均时间复杂度也是 O(nlog2n)

```javascript
var quickSort = function (arr) {
  if (arr.length <= 1) {
    return arr
  }
  var pivotIndex = Math.floor(arr.length / 2) //四舍五入
  var pivot = arr.splice(pivotIndex, 1)[0] //获取中间基准数组。splice返回删除的数组（必需。规定从何处添加/删除元素，）
  var left = []
  var right = []
  for (var i = 0; i < arr.length; i++) {
    if (arr[i] < pivot) {
      left.push(arr[i])
    } else {
      right.push(arr[i])
    }
  }
  return quickSort(left).concat([pivot], quickSort(right)) //连接左，右和中间基准数组
}
```

#### 7.3 插入排序

> 当插入第 i(i ≥ 1)个元素时，假设前面从 arr[0]到 arr[i-1]已经有序，那么只需将 arr[i]和前面那些有序的数值进行比较，找到自己应该插入的位置即可，原来位置上的元素一次向后顺移。

```javascript
let arr = [0, 99, 2, 6, 1, 10, 2, 3, 1, 9, 0]

function insertSort(arr) {
  let idx = 1
  while (idx < arr.length) {
    while (idx > 0) {
      if (arr[idx] >= arr[idx - 1]) break
      ;[arr[idx], arr[idx - 1]] = [arr[idx - 1], arr[idx]]
      idx--
    }
    idx++
  }
}

insertSort(arr)
```

**插入排序稳定，最差时间复杂度 o(n^2) 待排序列是降序序列；最优时间复杂度 o(n)即待排序列是升序，平均时间复杂度 o(n^2)**

### 8. 不借助临时变量，进行两个整数的交换

```javascript
输入 a = 2, b = 4 输出 a = 4, b =2
```

![exchange](exchange.png)

这种问题非常巧妙，需要大家跳出惯有的思维，利用 a , b 进行置换。

主要是利用 + - 去进行运算，类似 a = a + ( b - a) 实际上等同于最后 的 a = b;

```javascript
function swap(a, b) {
  b = b - a
  a = a + b
  b = a - b
  return [a, b]
}
module.exports = swap
```

### 9.数组扁平化

数组扁平化概念：**数组扁平化是指将一个多维数组变为一维数组**

[1, [2, 3, [4, 5]]] ------> [1, 2, 3, 4, 5]
实现

#### 1. reduce

遍历数组每一项，若值为数组则递归遍历，否则 concat。

```javascript
function flatten(arr) {
  return arr.reduce((result, item) => {
    return result.concat(Array.isArray(item) ? flatten(item) : item)
  }, [])
}
```

reduce 是数组的一种方法，它接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。

reduce 包含两个参数：回调函数，传给 total 的初始值

```javascript
// 求数组的各项值相加的和：
arr.reduce((total, item) => {
  // total为之前的计算结果，item为数组的各项值
  return total + item
}, 0)
```

#### 2. toString & split

调用数组的 toString 方法，将数组变为字符串然后再用 split 分割还原为数组

```javascript
function flatten(arr) {
  return arr
    .toString()
    .split(',')
    .map(function (item) {
      return Number(item)
    })
}
```

因为 split 分割后形成的数组的每一项值为字符串，所以需要用一个 map 方法遍历数组将其每一项转换为数值型

#### 3. join & split

和上面的 toString 一样，join 也可以将数组转换为字符串

```javascript
function flatten(arr) {
  return arr
    .join(',')
    .split(',')
    .map(function (item) {
      return parseInt(item)
    })
}
```

#### 4. 递归

递归的遍历每一项，若为数组则继续遍历，否则 concat

```javascript
function flatten(arr) {
  var res = []
  arr.map((item) => {
    if (Array.isArray(item)) {
      res = res.concat(flatten(item))
    } else {
      res.push(item)
    }
  })
  return res
}
```

#### 5. 扩展运算符

es6 的扩展运算符能将二维数组变为一维

[].concat(...[1, 2, 3, [4, 5]]); // [1, 2, 3, 4, 5]
根据这个结果我们可以做一个遍历，若 arr 中含有数组则使用一次扩展运算符，直至没有为止。

```javascript
function flatten(arr) {
  while (arr.some((item) => Array.isArray(item))) {
    arr = [].concat(...arr)
  }
  return arr
}
```

总结
虽然说写了 5 种方法，但是核心也只有一个：

遍历数组 arr，若 arr[i]为数组则递归遍历，直至 arr[i]不为数组然后与之前的结果 concat。

### 10. 深度优先遍历与广度优先遍历

#### 1. 区别

> 1） 二叉树的深度优先遍历的非递归的通用做法是采用栈，广度优先遍历的非递归的通用做法是采用队列。  
> 2） 深度优先遍历：对每一个可能的分支路径深入到不能再深入为止，而且每个结点只能访问一次。要特别注意的是，二叉树的深度优先遍历比较特殊，可以细分为先序遍历、中序遍历、后序遍历。具体说明如下：

- 先序遍历：对任一子树，先访问根，然后遍历其左子树，最后遍历其右子树。
- 中序遍历：对任一子树，先遍历其左子树，然后访问根，最后遍历其右子树。
- 后序遍历：对任一子树，先遍历其左子树，然后遍历其右子树，最后访问根。
- 广度优先遍历：又叫层次遍历，从上往下对每一层依次访问，在每一层中，从左往右（也可以从右往左）访问结点，访问完一层就进入下一层，直到没有结点可以访问为止。

> 3）深度优先搜素算法：不全部保留结点，占用空间少；有回溯操作(即有入栈、出栈操作)，运行速度慢。

> 广度优先搜索算法：保留全部结点，占用空间大； 无回溯操作(即无入栈、出栈操作)，运行速度快。

> 通常深度优先搜索法不全部保留结点，扩展完的结点从数据库中弹出删去，这样，一般在数据库中存储的结点数就是深度值，因此它占用空间较少。

所以，当搜索树的结点较多，用其它方法易产生内存溢出时，深度优先搜索不失为一种有效的求解方法。

> 广度优先搜索算法，一般需存储产生的所有结点，占用的存储空间要比深度优先搜索大得多，因此，程序设计中，必须考虑溢出和节省内存空间的问题。

但广度优先搜索法一般无回溯操作，即入栈和出栈的操作，所以运行速度比深度优先搜索要快些。

#### 2. 二叉树的遍历

![binary-tree](binary-tree.png)

- 先序遍历（递归）：35 20 15 16 29 28 30 40 50 45 55
- 中序遍历（递归）：15 16 20 28 29 30 35 40 45 50 55
- 后序遍历（递归）：16 15 28 30 29 20 45 55 50 40 35
- 先序遍历（非递归）：35 20 15 16 29 28 30 40 50 45 55
- 中序遍历（非递归）：15 16 20 28 29 30 35 40 45 50 55
- 后序遍历（非递归）：16 15 28 30 29 20 45 55 50 40 35
- 广度优先遍历：35 20 40 15 29 50 16 28 30 45 55
