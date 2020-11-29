---
layout: post
title: "HTML Issues"
author: "chan"
date: "2020-06-19"
category: "note"
---

### 换行符转换

**问题：**要展示的内容里面有 `\n`  这样的标记时，如果直接展示，则这个 `\n` 并不会被解析。

**解决方案：**

+ 添加样式  `white-space: pre-wrap`  

+ 或者使用 `<pre>` 标记进行包裹

---

### 通过 button 下载文件

**问题**：点击按钮之后，后台返回文件的 url，需要能够自动触发下载操作

**解决方案**：简单的做法如下：

```js
let a = document.createElement("a");
a.download = fileName;
a.style.display = "none";
a.target="_blank";   // open in new tag page
a.href = fullurl;    
document.body.appendChild(a);
a.click();
URL.revokeObjectURL(a.href);    
document.body.removeChild(a); 


```

