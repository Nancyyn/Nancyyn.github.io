---
title: JS 二
date: 2019-11-03 14:50:50
categories:
  - 面试
tags:
  - Js
  - 面试
---

**JS 面试总结分为五部分，本节为第二部分：**

### 3.call() 与 apply()

#### 3.1 call()

- **方法重用**
  call() 方法可以用来调用所有者对象作为参数的方法。

通过 call()，您能够使用属于另一个对象的方法。

```javascript
本例调用 person 的 fullName 方法，并用于 person1：

实例
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
var person1 = {
    firstName:"Bill",
    lastName: "Gates",
}j
var person2 = {
    firstName:"Steve",
    lastName: "Jobs",
}
person.fullName.call(person1);  // 将返回 "Bill Gates"
```

<!--more-->

#### 3.2.apply()

- 方法重用
  通过 apply() 方法，您能够编写用于不同对象的方法。

apply() 方法与 call() 方法非常相似：

```javascript
在本例中，person 的 fullName 方法被应用到 person1：

实例
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
var person1 = {
    firstName: "Bill",
    lastName: "Gates",
}
person.fullName.apply(person1);  // 将返回 "Bill Gates"
```

- call() 和 apply() 之间的区别
  不同之处是：

call() 方法分别接受参数。

apply() 方法接受数组形式的参数。

带参数的 apply() 方法

```javascript
apply() 方法接受数组中的参数：

实例
var person = {
  fullName: function(city, country) {
    return this.firstName + " " + this.lastName + "," + city + "," + country;
  }
}
var person1 = {
  firstName:"John",
  lastName: "Doe"
}
person.fullName.apply(person1, ["Oslo", "Norway"]);
```

与 call() 方法对比：

```javascript
实例
var person = {
  fullName: function (city, country) {
    return this.firstName + ' ' + this.lastName + ',' + city + ',' + country
  }
}
var person1 = {
  firstName: 'John',
  lastName: 'Doe'
}
person.fullName.call(person1, 'Oslo', 'Norway')
```

### 4. typeof 与 Number

```javascript
typeof null = object
typeof undefined = undefined
Number(null) = 0
Number(undefined) = NaN
```

### 5.正则

#### 5.1 test()

- test() 是一个正则表达式方法。

  它通过模式来搜索字符串，然后根据结果返回 true 或 false。

  下面的例子搜索字符串中的字符 "e"：

  ```javascript
  var patt = /e/
  patt.test('The best things in life are free!') // true
  ```

#### 5.2 exec()

- exec() 方法是一个正则表达式方法。

  它通过指定的模式（pattern）搜索字符串，并返回已找到的文本。

  如果未找到匹配，则返回 null。

  下面的例子搜索字符串中的字符 "e"：

  ```javascript
  var obj = /e/.exec('The best things in life are free!')
  console.log(obj) // obj见下图
  console.log(
    'Found ' +
      obj[0] +
      ' in position ' +
      obj.index +
      ' in the text: ' +
      obj.input
  )
  // Found e in position 2 in the text: The best things in life are free!
  ```

![exec](exec.png)

#### 5.3 RegExp 对象

- RegExp 对象表示正则表达式，它是对字符串执行模式匹配的强大工具。

- 直接量语法

  ```javascript
  /pattern/attributes
  ```

- 创建 RegExp 对象的语法：

  ```javascript
  new RegExp(pattern, attributes)
  ```

- 参数

  参数 **pattern** 是一个字符串，指定了正则表达式的模式或其他正则表达式。

  参数 **attributes** 是一个可选的字符串，包含属性 "g"、"i" 和 "m"，分别用于指定全局匹配、区分大小写的匹配和多行匹配。ECMAScript 标准化之前，不支持 m 属性。如果 _pattern_ 是正则表达式，而不是字符串，则必须省略该参数。

##### 5.3.1 修饰符

| 修饰符                                                    | 描述                                                     |
| :-------------------------------------------------------- | :------------------------------------------------------- |
| [i](https://www.w3school.com.cn/jsref/jsref_regexp_i.asp) | 执行对大小写不敏感的匹配。                               |
| [g](https://www.w3school.com.cn/jsref/jsref_regexp_g.asp) | 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。 |
| m                                                         | 执行多行匹配。                                           |

##### 5.3.2 方括号

方括号用于查找某个范围内的字符：

| 表达式                                                                     | 描述                               |
| :------------------------------------------------------------------------- | :--------------------------------- |
| [\[abc\]](https://www.w3school.com.cn/jsref/jsref_regexp_charset.asp)      | 查找方括号之间的任何字符。         |
| [\[^abc\]](https://www.w3school.com.cn/jsref/jsref_regexp_charset_not.asp) | 查找任何不在方括号之间的字符。     |
| [0-9]                                                                      | 查找任何从 0 至 9 的数字。         |
| [a-z]                                                                      | 查找任何从小写 a 到小写 z 的字符。 |
| [A-Z]                                                                      | 查找任何从大写 A 到大写 Z 的字符。 |
| [A-z]                                                                      | 查找任何从大写 A 到小写 z 的字符。 |
| [adgk]                                                                     | 查找给定集合内的任何字符。         |
| [^adgk]                                                                    | 查找给定集合外的任何字符。         |
| (red\|blue\|green)                                                         | 查找任何指定的选项。               |

##### 5.3.3 元字符

元字符（Metacharacter）是拥有特殊含义的字符：

| 元字符                                                                   | 描述                                        |
| :----------------------------------------------------------------------- | :------------------------------------------ |
| [.](https://www.w3school.com.cn/jsref/jsref_regexp_dot.asp)              | 查找单个字符，除了换行和行结束符。          |
| [\w](https://www.w3school.com.cn/jsref/jsref_regexp_wordchar.asp)        | 查找单词字符。                              |
| [\W](https://www.w3school.com.cn/jsref/jsref_regexp_wordchar_non.asp)    | 查找非单词字符。                            |
| [\d](https://www.w3school.com.cn/jsref/jsref_regexp_digit.asp)           | 查找数字。                                  |
| [\D](https://www.w3school.com.cn/jsref/jsref_regexp_digit_non.asp)       | 查找非数字字符。                            |
| [\s](https://www.w3school.com.cn/jsref/jsref_regexp_whitespace.asp)      | 查找空白字符。                              |
| [\S](https://www.w3school.com.cn/jsref/jsref_regexp_whitespace_non.asp)  | 查找非空白字符。                            |
| [\b](https://www.w3school.com.cn/jsref/jsref_regexp_begin.asp)           | 匹配单词边界。                              |
| [\B](https://www.w3school.com.cn/jsref/jsref_regexp_begin_not.asp)       | 匹配非单词边界。                            |
| \0                                                                       | 查找 NUL 字符。                             |
| [\n](https://www.w3school.com.cn/jsref/jsref_regexp_newline.asp)         | 查找换行符。                                |
| \f                                                                       | 查找换页符。                                |
| \r                                                                       | 查找回车符。                                |
| \t                                                                       | 查找制表符。                                |
| \v                                                                       | 查找垂直制表符。                            |
| [\xxx](https://www.w3school.com.cn/jsref/jsref_regexp_octal.asp)         | 查找以八进制数 xxx 规定的字符。             |
| [\xdd](https://www.w3school.com.cn/jsref/jsref_regexp_hex.asp)           | 查找以十六进制数 dd 规定的字符。            |
| [\uxxxx](https://www.w3school.com.cn/jsref/jsref_regexp_unicode_hex.asp) | 查找以十六进制数 xxxx 规定的 Unicode 字符。 |

##### 5.3.4 量词

| 量词                                                                  | 描述                                        |
| :-------------------------------------------------------------------- | :------------------------------------------ |
| [n+](https://www.w3school.com.cn/jsref/jsref_regexp_onemore.asp)      | 匹配任何包含至少一个 n 的字符串。           |
| [n\*](https://www.w3school.com.cn/jsref/jsref_regexp_zeromore.asp)    | 匹配任何包含零个或多个 n 的字符串。         |
| [n?](https://www.w3school.com.cn/jsref/jsref_regexp_zeroone.asp)      | 匹配任何包含零个或一个 n 的字符串。         |
| [n{X}](https://www.w3school.com.cn/jsref/jsref_regexp_nx.asp)         | 匹配包含 X 个 n 的序列的字符串。            |
| [n{X,Y}](https://www.w3school.com.cn/jsref/jsref_regexp_nxy.asp)      | 匹配包含 X 至 Y 个 n 的序列的字符串。       |
| [n{X,}](https://www.w3school.com.cn/jsref/jsref_regexp_nxcomma.asp)   | 匹配包含至少 X 个 n 的序列的字符串。        |
| [n$](https://www.w3school.com.cn/jsref/jsref_regexp_ndollar.asp)      | 匹配任何结尾为 n 的字符串。                 |
| [^n](https://www.w3school.com.cn/jsref/jsref_regexp_ncaret.asp)       | 匹配任何开头为 n 的字符串。                 |
| [?=n](https://www.w3school.com.cn/jsref/jsref_regexp_nfollow.asp)     | 匹配任何其后紧接指定字符串 n 的字符串。     |
| [?!n](https://www.w3school.com.cn/jsref/jsref_regexp_nfollow_not.asp) | 匹配任何其后没有紧接指定字符串 n 的字符串。 |

##### 5.3.5 RegExp 对象属性

| 属性                                                                        | 描述                                     | FF  | IE  |
| :-------------------------------------------------------------------------- | :--------------------------------------- | :-- | :-- |
| [global](https://www.w3school.com.cn/jsref/jsref_regexp_global.asp)         | RegExp 对象是否具有标志 g。              | 1   | 4   |
| [ignoreCase](https://www.w3school.com.cn/jsref/jsref_regexp_ignorecase.asp) | RegExp 对象是否具有标志 i。              | 1   | 4   |
| [lastIndex](https://www.w3school.com.cn/jsref/jsref_lastindex_regexp.asp)   | 一个整数，标示开始下一次匹配的字符位置。 | 1   | 4   |
| [multiline](https://www.w3school.com.cn/jsref/jsref_multiline_regexp.asp)   | RegExp 对象是否具有标志 m。              | 1   | 4   |
| [source](https://www.w3school.com.cn/jsref/jsref_source_regexp.asp)         | 正则表达式的源文本。                     | 1   | 4   |

##### 5.3.6 RegExp 对象方法

| 方法                                                                  | 描述                                               | FF  | IE  |
| :-------------------------------------------------------------------- | :------------------------------------------------- | :-- | :-- |
| [compile](https://www.w3school.com.cn/jsref/jsref_regexp_compile.asp) | 编译正则表达式。                                   | 1   | 4   |
| [exec](https://www.w3school.com.cn/jsref/jsref_exec_regexp.asp)       | 检索字符串中指定的值。返回找到的值，并确定其位置。 | 1   | 4   |
| [test](https://www.w3school.com.cn/jsref/jsref_test_regexp.asp)       | 检索字符串中指定的值。返回 true 或 false。         | 1   | 4   |

##### 5.3.7 支持正则表达式的 String 对象的方法

| 方法                                                           | 描述                             | FF  | IE  |
| :------------------------------------------------------------- | :------------------------------- | :-- | :-- |
| [search](https://www.w3school.com.cn/jsref/jsref_search.asp)   | 检索与正则表达式相匹配的值。     | 1   | 4   |
| [match](https://www.w3school.com.cn/jsref/jsref_match.asp)     | 找到一个或多个正则表达式的匹配。 | 1   | 4   |
| [replace](https://www.w3school.com.cn/jsref/jsref_replace.asp) | 替换与正则表达式匹配的子串。     | 1   | 4   |
| [split](https://www.w3school.com.cn/jsref/jsref_split.asp)     | 把字符串分割为字符串数组。       | 1   | 4   |
