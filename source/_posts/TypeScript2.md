---
title: TypeScript 二
date: 2019-12-30 15:55:22
categories:
  - TypeScript
tags:
  - TypeScript
  - 技术总结
---

**TypeScript 总结分为两部分，本节为第二部分：**

### 4.类

- 继承

> 类从基类中继承了属性和方法

```typescript
class Animal {
  name: string
  constructor(theName: string) {
    this.name = theName
  }
  move(distanceInMeters: number = 0) {
    console.log(`${this.name} moved ${distanceInMeters}m.`)
  }
}

class Snake extends Animal {
  constructor(name: string) {
    super(name)
  }
  move(distanceInMeters = 5) {
    console.log('Slithering...')
    super.move(distanceInMeters)
  }
}

class Horse extends Animal {
  constructor(name: string) {
    super(name)
  }
  move(distanceInMeters = 45) {
    console.log('Galloping...')
    super.move(distanceInMeters)
  }
}

let sam = new Snake('Sammy the Python')
let tom: Animal = new Horse('Tommy the Palomino')

sam.move()
tom.move(34)
```

<!--more-->

> 这里，Snake 和 Horse 是一个派生类，它派生自 Animal 基类，通过 extends 关键字。 派生类通常被称作子类，基类通常被称作超类。
> 派生类包含了一个构造函数，它必须调用 super()，它会执行基类的构造函数。
> 这个例子演示了如何在子类里可以重写父类的方法。 Snake 类和 Horse 类都创建了 move 方法，它们重写了从 Animal 继承来的 move 方法，使得 move 方法根据不同的类而具有不同的功能。 注意，即使 tom 被声明为 Animal 类型，但因为它的值是 Horse，调用 tom.move(34)时，它会调用 Horse 里重写的方法：

```typescript
Slithering...
Sammy the Python moved 5m.
Galloping...
Tommy the Palomino moved 34m.
```

- 公共，私有与受保护的修饰符

> 在 TypeScript 里，成员都默认为 public。
> 当成员被标记成 private 时，它就不能在声明它的类的外部访问。比如：

```typescript
class Animal {
  private name: string
  constructor(theName: string) {
    this.name = theName
  }
}

new Animal('Cat').name // 错误: 'name' 是私有的.
```

当我们比较带有 private 或 protected 成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个 private 成员，那么只有当另外一个类型中也存在这样一个 private 成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 protected 成员也使用这个规则。
下面来看一个例子，更好地说明了这一点：

```typescript
class Animal {
  private name: string
  constructor(theName: string) {
    this.name = theName
  }
}

class Rhino extends Animal {
  constructor() {
    super('Rhino')
  }
}

class Employee {
  private name: string
  constructor(theName: string) {
    this.name = theName
  }
}

let animal = new Animal('Goat')
let rhino = new Rhino()
let employee = new Employee('Bob')

animal = rhino
animal = employee // 错误: Animal 与 Employee 不兼容.
```

这个例子中有 Animal 和 Rhino 两个类，Rhino 是 Animal 类的子类。 还有一个 Employee 类，其类型看上去与 Animal 是相同的。 我们创建了几个这些类的实例，并相互赋值来看看会发生什么。 因为 Animal 和 Rhino 共享了来自 Animal 里的私有成员定义 private name: string，因此它们是兼容的。 然而 Employee 却不是这样。当把 Employee 赋值给 Animal 的时候，得到一个错误，说它们的类型不兼容。 尽管 Employee 里也有一个私有成员 name，但它明显不是 Animal 里面定义的那个。

- protected

> protected 修饰符与 private 修饰符的行为很相似，但有一点不同，protected 成员在派生类中仍然可以访问。例如：

```typescript
class Person {
  protected name: string
  constructor(name: string) {
    this.name = name
  }
}

class Employee extends Person {
  private department: string

  constructor(name: string, department: string) {
    super(name)
    this.department = department
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`
  }
}

let howard = new Employee('Howard', 'Sales')
console.log(howard.getElevatorPitch())
console.log(howard.name) // 错误
```

注意，我们不能在 Person 类外使用 name，但是我们仍然可以通过 Employee 类的实例方法访问，因为 Employee 是由 Person 派生而来的。

> 构造函数也可以被标记成 protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。比如，

```typescript
class Person {
  protected name: string
  protected constructor(theName: string) {
    this.name = theName
  }
}

// Employee 能够继承 Person
class Employee extends Person {
  private department: string

  constructor(name: string, department: string) {
    super(name)
    this.department = department
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`
  }
}

let howard = new Employee('Howard', 'Sales')
let john = new Person('John') // 错误: 'Person' 的构造函数是被保护的.
```

- 静态属性 static
  到目前为止，我们只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性。 我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。 在这个例子里，我们使用 static 定义 origin，因为它是所有网格都会用到的属性。 每个实例想要访问这个属性的时候，都要在 origin 前面加上类名。 如同在实例属性上使用 this.前缀来访问属性一样，这里我们使用 Grid.来访问静态属性。

```typescript
class Grid {
  static origin = { x: 0, y: 0 }
  calculateDistanceFromOrigin(point: { x: number; y: number }) {
    let xDist = point.x - Grid.origin.x
    let yDist = point.y - Grid.origin.y
    return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale
  }
  constructor(public scale: number) {}
}

let grid1 = new Grid(1.0) // 1x scale
let grid2 = new Grid(5.0) // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }))
console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }))
```

- 抽象类

> 抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 abstract 关键字是用于定义抽象类和在抽象类内部定义抽象方法。

```typescript
abstract class Animal {
  abstract makeSound(): void
  move(): void {
    console.log('roaming the earch...')
  }
}
```

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 abstract 关键字并且可以包含访问修饰符。

```typescript
abstract class Department {
  constructor(public name: string) {}

  printName(): void {
    console.log('Department name: ' + this.name)
  }

  abstract printMeeting(): void // 必须在派生类中实现
}

class AccountingDepartment extends Department {
  constructor() {
    super('Accounting and Auditing') // 在派生类的构造函数中必须调用 super()
  }

  printMeeting(): void {
    console.log('The Accounting Department meets each Monday at 10am.')
  }

  generateReports(): void {
    console.log('Generating accounting reports...')
  }
}

let department: Department // 允许创建一个对抽象类型的引用
department = new Department() // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment() // 允许对一个抽象子类进行实例化和赋值
department.printName()
department.printMeeting()
department.generateReports() // 错误: 方法在声明的抽象类中不存在
```

### 5.函数

- 可选参数和默认参数

> TypeScript 里我们可以在参数名旁使用?实现可选参数的功能。
> 可选参数必须跟在必须参数后面。

```typescript
function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + ' ' + lastName
  else return firstName
}

let result1 = buildName('Bob') // works correctly now
let result2 = buildName('Bob', 'Adams', 'Sr.') // error, too many parameters
let result3 = buildName('Bob', 'Adams') // ah, just right
```

- this

  2.1 this 和箭头函数

> JavaScript 里，this 的值在函数被调用的时候才会指定。

```typescript
let deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function () {
    return function () {
      let pickedCard = Math.floor(Math.random() * 52)
      let pickedSuit = Math.floor(pickedCard / 13)

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }
    }
  }
}

let cardPicker = deck.createCardPicker()
let pickedCard = cardPicker()

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit)
```

可以看到 createCardPicker 是个函数，并且它又返回了一个函数。 如果我们尝试运行这个程序，会发现它并没有弹出对话框而是报错了。 因为 createCardPicker 返回的函数里的 this 被设置成了 window 而不是 deck 对象。 因为我们只是独立的调用了 cardPicker()。 顶级的非方法式调用会将 this 视为 window。 （注意：在严格模式下，this 为 undefined 而不是 window）。

为了解决这个问题，我们可以在函数被返回时就绑好正确的 this。 这样的话，无论之后怎么使用它，都会引用绑定的‘deck’对象。 我们需要改变函数表达式来使用 ECMAScript 6 箭头语法。 箭头函数能保存函数创建时的 this 值，而不是调用时的值：

```typescript
let deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function () {
    // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
    return () => {
      let pickedCard = Math.floor(Math.random() * 52)
      let pickedSuit = Math.floor(pickedCard / 13)

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }
    }
  }
}

let cardPicker = deck.createCardPicker()
let pickedCard = cardPicker()

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit)
```

### 6. 泛型

### 7. 枚举

### 8. 类型推论

### 9. 类型兼容性

### 10. 高级类型

### 11. Symbols

自 ECMAScript 2015 起，symbol 成为了一种新的原生类型，就像 number 和 string 一样。
symbol 类型的值是通过 Symbol 构造函数创建的。

```typescript
let sym1 = Symbol()
let sym2 = Symbol('key') // 可选的字符串key
```

Symbols 是不可改变且唯一的。

```typescript
let sym2 = Symbol('key')
let sym3 = Symbol('key')
sym2 === sym3 // false, symbols是唯一的
```

像字符串一样，symbols 也可以被用做对象属性的键。

```typescript
let sym = Symbol()

let obj = {
  [sym]: 'value'
}

console.log(obj[sym]) // "value"
```

Symbols 也可以与计算出的属性名声明相结合来声明对象的属性和类成员。

```typescript
const getClassNameSymbol = Symbol()

class C {
  [getClassNameSymbol]() {
    return 'C'
  }
}

let c = new C()
let className = c[getClassNameSymbol]() // "C"
```

众所周知的 Symbols
除了用户定义的 symbols，还有一些已经众所周知的内置 symbols。 内置 symbols 用来表示语言内部的行为。

以下为这些 symbols 的列表：

```typescript
Symbol.hasInstance
方法，会被instanceof运算符调用。构造器对象用来识别一个对象是否是其实例。

Symbol.isConcatSpreadable
布尔值，表示当在一个对象上调用Array.prototype.concat时，这个对象的数组元素是否可展开。

Symbol.iterator
方法，被for-of语句调用。返回对象的默认迭代器。

Symbol.match
方法，被String.prototype.match调用。正则表达式用来匹配字符串。

Symbol.replace
方法，被String.prototype.replace调用。正则表达式用来替换字符串中匹配的子串。

Symbol.search
方法，被String.prototype.search调用。正则表达式返回被匹配部分在字符串中的索引。

Symbol.species
函数值，为一个构造函数。用来创建派生对象。

Symbol.split
方法，被String.prototype.split调用。正则表达式来用分割字符串。

Symbol.toPrimitive
方法，被ToPrimitive抽象操作调用。把对象转换为相应的原始值。

Symbol.toStringTag
方法，被内置方法Object.prototype.toString调用。返回创建对象时默认的字符串描述。

Symbol.unscopables
对象，它自己拥有的属性会被with作用域排除在外。
```
