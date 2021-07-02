---
title: npm发布到私服
date: 2019-12-10 15:55:22
categories:
  - npm发布到私服
tags:
  - npm发布到私服
  - 技术总结
---

### 1. 从本地发布到私服

#### 1.1 指定 registry

可以在命令行中
`npm publish --registry=http://192.168.0.191:8888/repository/rdmu-npm/`
**或者**
在`package.json`文件下添加`publishConfig`属性

![registry](registry.png)

#### 1.2 使用 npm login 进行登录

- 指定 registry

`npm login --registry=http://192.168.0.191:8888/repository/rdmu-npm/`

- 在输入 username、password、email 后，会报一个错：

![error1](error1.png)

是因为本地![npmrc](npmrc.png)文件中有之前 npm registry 的记录，打开文件删掉内容即可。

如果登录后还不能发，检查 npm nexus 的 Realms 设置，把 npm Bearer Token Reaim 放入 Active 中，并保存：

![realms](realms.png)

#### 1.3 npm publish 发布即可

### 2. 从私服上下载 npm 包

#### 2.1 设置从哪个源上下载资源

`npm config set @p8-ui:registry=http://192.168.0.191:8888/repository/rdmu-npm/`

#### 2.2 从私服上下载资源

![error2](error2.png)

发现私服`rdmu-npm`上没有 p8-ui 包本身的依赖，后来配置了`npm-taobao`代理，组合成为`npm-group`，将源地址设置为`npm config set registry http://192.168.0.191:8888/repository/npm-group/`，才顺利下载了依赖。
