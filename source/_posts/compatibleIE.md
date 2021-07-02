---
title: 兼容IE
date: 2019-11-20 15:55:22
categories:
  - 兼容IE
tags:
  - 兼容IE
  - 面试
---

#### 1.IE 中安装 chrome 插件

#### 2.使用 IE 中 window.ActiveXObject 的属性

```
var objShell=new ActiveXObject("WScript.Shell");
    var reject=false;
    try{
        var path=objShell.RegRead("HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\App Paths\\chrome.exe\\");
        path=path.replace(/\ /g,'%20');//空格替换为%20
        //path=path.replace(/\\/g,'\\\\');//单斜杠替换为双斜杠
        var url=window.location.href.replace('home.html', urlQuery);;
        objShell.Run("file:///"+path+" "+url,0,true);
        window.opener=null;
        window.open('', '_self', '');
        window.close();
    }
    catch(err){
        reject=true
    }
```
