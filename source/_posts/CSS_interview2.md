---
title: CSS 二
date: 2019-11-02 17:35:22
categories:
  - 面试
tags:
  - Css
  - 面试
---

**CSS 面试总结分为三部分，本节为第二部分：**

### 9.css 画一个三角形

- 利用 border

```css
/** 正三角 */
.triangle {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 25px 40px 25px;
  border-color: transparent transparent rgb(245, 129, 127) transparent;
}

/** 倒三角 */
.triangle {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 40px 25px 0 25px;
  border-color: rgb(245, 129, 127) transparent transparent transparent;
}
```

<!--more-->

### 10.手动实现一个左右固定 100px，中间自适应的三列布局

#### 10.1 双飞翼布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>3-items双飞翼布局</title>
    <style>
      html,
      body {
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
      }
      body {
        overflow: hidden;
      }
      .main {
        width: 100%;
        height: 100%;
        float: left;
      }
      .middle {
        margin: 0 100px;
        height: 100%;
        background: blueviolet;
      }
      .left {
        width: 100px;
        height: 100%;
        background: red;
        float: left;
        margin-left: -100%;
      }
      .right {
        width: 100px;
        height: 100%;
        background: red;
        float: left;
        margin-left: -100px;
      }
    </style>
  </head>
  <body>
    <div class="main">
      <div class="middle"></div>
    </div>
    <div class="left"></div>
    <div class="right"></div>
  </body>
</html>
```

#### 10.2 圣杯布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>3-items圣杯布局</title>
    <style>
      html,
      body {
        height: 100%;
        margin: 0;
        padding: 0;
      }

      body {
        overflow: hidden;
        padding: 0 100px;
      }
      .main {
        width: 100%;
        height: 100%;
        background: blue;
        float: left;
      }
      .left {
        width: 100px;
        height: 100%;
        background: red;
        float: left;
        margin-left: -100%;
        position: relative;
        left: -100px;
      }
      .right {
        width: 100px;
        height: 100%;
        background: red;
        float: left;
        margin-left: -100px;
        position: relative;
        right: -100px;
      }
    </style>
  </head>

  <body>
    <div class="main">
      mainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmainmain
    </div>
    <div class="left"></div>
    <div class="right"></div>
  </body>
</html>
```

#### 10.3 浮动布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>浮动布局</title>
    <style>
      html,
      body {
        width: 100%;
        height: 100%;
        margin: 0;
        padding: 0;
      }

      .main {
        height: 100%;
        margin: 0 100px;
        background: blue;
      }

      .left {
        float: left;
        width: 100px;
        height: 100%;
        background: red;
      }

      .right {
        width: 100px;
        height: 100%;
        background: red;
        float: right;
      }
    </style>
  </head>

  <body>
    <div class="left"></div>
    <div class="right"></div>
    <div class="main"></div>
  </body>
</html>
```

#### 10.4 position 布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>position</title>
    <style>
      html,
      body {
        width: 100%;
        height: 100%;
        margin: 0;
        padding: 0;
        position: relative;
      }
      .left {
        width: 100px;
        height: 100%;
        background-color: red;
        position: absolute;
        left: 0;
        top: 0;
      }
      .right {
        width: 100px;
        height: 100%;
        background-color: red;
        position: absolute;
        top: 0;
        right: 0;
      }
      .main {
        height: 100%;
        background-color: blue;
        margin: 0 100px;
      }
    </style>
  </head>
  <body>
    <div class="left"></div>
    <div class="right"></div>
    <div class="main"></div>
  </body>
</html>
```

#### 10.5 flex 布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>flex</title>
    <style>
      html,
      body {
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        background-color: blue;
        display: flex;
        flex-direction: row;
      }
      .left {
        width: 100px;
        height: 100%;
        background-color: brown;
      }
      .main {
        flex: 1;
        height: 100%;
        background-color: blue;
      }
      .right {
        width: 100px;
        height: 100%;
        background-color: brown;
      }
    </style>
  </head>

  <body>
    <div class="left"></div>
    <div class="main">main</div>
    <div class="right"></div>
  </body>
</html>
```

#### 10.6 calc

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>calc</title>
    <style>
      html,
      body {
        width: 100%;
        height: 100%;
        margin: 0;
        padding: 0;
      }

      .left {
        width: 100px;
        height: 100%;
        background-color: red;
        float: left;
      }

      .right {
        width: 100px;
        height: 100%;
        background-color: red;
        float: right;
      }
      .main {
        width: calc(100% - 200px);
        float: left;
        height: 100%;
        background-color: blue;
      }
    </style>
  </head>

  <body>
    <div class="left"></div>
    <div class="right"></div>
    <div class="main"></div>
  </body>
</html>
```
