---
layout: post
title: "js-cheatsheets"
author: "chan"
date: "2020-06-29"
category: "note"
---

### BaseName

JS 中没有直接可用的 basename 方法，需要手动的实现一个。最简单的就是通过 split 实现：

```javascript
> baseName = path => path.split(/[\\/]/).pop()
[Function: basename]
> basename("/hello/world")
'world'
```

同样可以在原型链中实现，简单的实现如下：

```javascript
String.prototype.basename = function(sep) {
  sep = sep || '\\/';
  return this.split(new RegExp("["+sep+"]")).pop();
}

// use
> str="https://ww.google.com/blablabla"
> str.basename()
'blablabla'
```

