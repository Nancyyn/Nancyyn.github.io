---
title: TypeScript 一
date: 2019-12-30 15:55:22
categories:
  - TypeScript
tags:
  - TypeScript
  - 技术总结
---

**TypeScript 总结分为两部分，本节为第一部分：**

### 1.基础类型

- 布尔值 boolean

```typescript
let isDone: boolean = false
```

- 数字 number 和 JavaScript 一样，TypeScript 里的所有数字都是浮点数。

```typescript
let decLiteral: number = 6
```

- BigInt

本节介绍的 bigint 数据类型是用来表示那些已经超出了 number 类型最大值的整数值，对于总是被诟病的整数溢出问题，使用了 bigint 后将完美解决。

1. 慕课解释
   bigint 是一种基本数据类型（primitive data type）。

JavaScript 中可以用 Number 表示的最大整数为 2^53 - 1，可以写为 Number.MAX_SAFE_INTEGER。如果超过了这个界限，可以用 BigInt 来表示，它可以表示任意大的整数。

<!--more-->

2. 语法

在一个整数字面量后加 n 的方式定义一个 BigInt，如：10n 或者调用函数 BigInt()：

```typescript
const theBiggestInt: bigint = 9007199254740991n
const alsoHuge: bigint = BigInt(9007199254740991)
const hugeString: bigint = BigInt('9007199254740991')

theBiggestInt === alsoHuge // true
theBiggestInt === hugeString // true
```

代码解释：

第 1-3 行，分别是三种表达整数 9007199254740991 的方式，方式不同含义相同，所以完全相等。

BigInt 与 Number 的不同点：

BigInt 不能用于 Math 对象中的方法。
BigInt 不能和任何 Number 实例混合运算，两者必须转换成同一种类型。
BigInt 变量在转换为 Number 变量时可能会丢失精度。

```typescript
const biggest: number = Number.MAX_SAFE_INTEGER

const biggest1: number = biggest + 1
const biggest2: number = biggest + 2

biggest1 === biggest2 // true 超过精度
```

代码解释：

第 1 行，声明了一个 number 类型最大值的变量 biggest，对于 number 类型来说，这个就是最大精度。

第 3-4 行，最大精度就是这个容器已经完全满了，无论往上加多少都会溢出，所以这两个值是相等的。

而使用 BigInt:

```typescript
const biggest: bigint = BigInt(Number.MAX_SAFE_INTEGER)

const biggest1: bigint = biggest + 1n
const biggest2: bigint = biggest + 2n

biggest1 === biggest2 // false
```

代码解释：

第 1 行，声明了一个 bigint 类型的变量 biggest。

第 3-4 行，bigint 类型就是用来表示那些已经超出了 number 类型最大值的整数值，也就是这个容器还没满，在此基础上加上两个不同的值，其结果不相等。

3. 类型信息
   使用 typeof 检测类型时，BigInt 对象返回 bigint:

```typescript
typeof 10n === 'bigint' // true
typeof BigInt(10) === 'bigint' // true

typeof 10 === 'number' // true
typeof Number(10) === 'number' // true
```

代码解释：

typeof 操作符返回一个字符串，表示未经计算的操作数的类型，用来判断基础数据类型。

第 1-2 行，两个书写方式都是 bigint 类型，所以相等。

第 4-5 行，两种书写方式都是 number 类型，所以相等。

4. 运算
   BigInt 可以正常使用 +、-、\*、/、\*\*、% 符号进行运算：

```typescript
const previousMaxSafe: bigint = BigInt(Number.MAX_SAFE_INTEGER)  // 9007199254740991n

const maxPlusOne: bigint = previousMaxSafe + 1n                  // 9007199254740992n

const multi: bigint = previousMaxSafe * 2n                       // 18014398509481982n

const subtr: bigint = multi – 10n                                // 18014398509481972n

const mod: bigint = multi % 10n                                  // 2n

const bigN: bigint = 2n ** 54n                                   // 18014398509481984n
```

Tip： 当使用 / 操作符时，会向下取整，不会返回小数部分：

```typescript
const divided: bigint = 5n / 2n // 2n, not 2.5n
```

5. 比较 与 条件

Number 和 BigInt 可以进行比较：

```typescript
0n === 0 // false

0n == 0 // true

1n < 2 // true

2n > 1 // true

2 > 2 // false

2n > 2 // false

2n >= 2 // true
```

条件判断：

```typescript
if (0n) {
  console.log('条件成立!');
} else {
  console.log('条件不成立!'); // 输出结果
}

0n || 10n    // 10n

0n &amp;&amp; 10n    // 0n

Boolean(0n)  // false

Boolean(10n) // true

!10n         // false

!0n          // true
```

代码解释：

后面会有一节介绍 Truthy 与 Falsy，这里先简单提下。除了明确的 true 和 false 两个 boolean 类型外，JavaScript 可以在需要用到布尔类型值的上下文中使用强制类型转换将值转换为布尔值。

比如 0 是假，10 是真。那么，同样的，0n 是假，10n 是真。

6. 小结

本小节介绍了 BigInt 类型相关知识，由于在 Number 与 BigInt 之间进行转换会损失精度，建议：

不要在两种类型之间进行相互转换。
仅在值可能大于 2^53 - 1 时使用 BigInt。

- 字符串 string

```typescript
let name: string = "bob";
let sentence: string = `Hello, my name is ${ name }.
```

- 数组 []

```typescript
let list: number[] = [1, 2, 3];

数组泛型，Array<元素类型>：
let list: Array<number> = [1, 2, 3];
```

- 元组 Tuple
  元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string 和 number 类型的元组。

```typescript
// Declare a tuple type
let x: [string, number]
// Initialize it
x = ['hello', 10] // OK
// Initialize it incorrectly
x = [10, 'hello'] // Error
```

当访问一个已知索引的元素，会得到正确的类型：

```typescript
console.log(x[0].substr(1)) // OK
console.log(x[1].substr(1)) // Error, 'number' does not have 'substr'
```

当访问一个越界的元素，会使用联合类型替代：

```typescript
x[3] = 'world' // OK, 字符串可以赋值给(string | number)类型
console.log(x[5].toString()) // OK, 'string' 和 'number' 都有 toString
x[6] = true // Error, 布尔不是(string | number)类型
```

- 枚举 enum

```typescript
enum Color {
  Red = 1,
  Green,
  Blue
}
let c: Color = Color.Green
```

枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为 2，但是不确定它映射到 Color 里的哪个名字，我们可以查找相应的名字：

```typescript
enum Color {
  Red = 1,
  Green,
  Blue
}
let colorName: string = Color[2]

alert(colorName) // 显示'Green'因为上面代码里它的值是2
```

- 任意值 any

```typescript
let notSure: any = 4
notSure = 'maybe a string instead'
notSure = false // okay, definitely a boolean

let list: any[] = [1, true, 'free']
list[1] = 100
```

- 空值 void
  某种程度上来说，void 类型像是与 any 类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void：

```typescript
function warnUser(): void {
    alert("This is my warning message");
}
声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null：

let unusable: void = undefined;
```

- null 与 undefined
  TypeScript 里，undefined 和 null 两者各自有自己的类型分别叫做 undefined 和 null。 和 void 相似，它们的本身的类型用处不是很大：

```typescript
// Not much else we can assign to these variables!
let u: undefined = undefined
let n: null = null
```

默认情况下 null 和 undefined 是所有类型的子类型。 就是说你可以把 null 和 undefined 赋值给 number 类型的变量。

- never
  never 类型表示的是那些永不存在的值的类型。 例如，never 类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never 类型，当它们被永不为真的类型保护所约束时。

never 类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是 never 的子类型或可以赋值给 never 类型（除了 never 本身之外）。 即使 any 也不可以赋值给 never。

下面是一些返回 never 类型的函数：

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
  throw new Error(message)
}

// 推断的返回值类型为never
function fail() {
  return error('Something failed')
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
  while (true) {}
}
```

- 类型断言
  有时候你会遇到这样的情况，你会比 TypeScript 更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。

通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。

类型断言有两种形式。 其一是“尖括号”语法：

```typescript
let someValue: any = 'this is a string'
let strLength: number = (<string>someValue).length
```

另一个为 as 语法：

```typescript
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在 TypeScript 里使用 JSX 时，只有 as 语法断言是被允许的。

- let

### 2.变量声明

- var 与 let

```typescript
for (var i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i)
  }, 100 * i)
}
```

返回结果：

```typescript
10
10
10
10
10
10
10
10
10
10
```

如果要得到 0-9 的输出结果，可使用立即执行的函数表达式（IIFE）来捕获每次迭代时 i 的值：

```typescript
for (var i = 0; i < 10; i++) {
  // capture the current state of 'i'
  // by invoking a function with its current value
  ;(function (i) {
    setTimeout(function () {
      console.log(i)
    }, 100 * i)
  })(i)
}
```

或者使用 let 声明：

```typescript
for (let i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i)
  }, 100 * i)
}
```

- 属性重命名

你也可以给属性以不同的名字：

```typescript
let { a: newName1, b: newName2 } = o
```

这里的语法开始变得混乱。 你可以将 a: newName1 读做 “a 作为 newName1“。 方向是从左到右，好像你写成了以下样子：

```typescript
let newName1 = o.a
let newName2 = o.b
```

令人困惑的是，这里的冒号不是指示类型的。 如果你想指定它的类型， 仍然需要在其后写上完整的模式。

```typescript
let { a, b }: { a: string; b: number } = o
```

- 展开

出现在展开对象后面的属性会覆盖前面的属性

```typescript
let defaults = { food: 'spicy', price: '$$', ambiance: 'noisy' }
let search = { food: 'rich', ...defaults }
```

对象展开还有其它一些意想不到的限制。 首先，它仅包含对象 自身的可枚举属性。 大体上是说当你展开一个对象实例时，你会丢失其方法：

```typescript
class C {
  p = 12
  m() {}
}
let c = new C()
let clone = { ...c }
clone.p // ok
clone.m() // error!
```

其次，TypeScript 编译器不允许展开泛型函数上的类型参数。 这个特性会在 TypeScript 的未来版本中考虑实现。

### 3.接口

- 接口初探

```typescript
interface LabelledValue {
  label: string
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label)
}

let myObj = { size: 10, label: 'Size 10 Object' }
printLabel(myObj)
```

只要传入的对象满足上面提到的必要条件，那么它就是被允许的。

还有一点值得提的是，类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。

- 可选属性

```typescript
interface SquareConfig {
  color?: string
  width?: number
}
```

带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个?符号。

- 只读属性
  一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 readonly 来指定只读属性:

```typescript
interface Point {
  readonly x: number
  readonly y: number
}
```
