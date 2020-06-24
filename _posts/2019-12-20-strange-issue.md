---
gitlayout: post
title: "奇怪的问题"
author: "Chan"
date: "2019-12-20"
category: "note"
---

## 由

在日常工作中，会很容易遇到一些奇葩的问题，微小，但却令人不适。往往这些只需要简单的方式就能解决。但是由于不常见，所以需要在此记录。



### <U+FEFF>  

这个 unicode 字符一般会出现在文件的开头，正常的编辑器中是看不到这个的，这是一个 BOM ，在 UTF-16/32 中是必需的一个标志，用来标记编码的排序方式。但是在 UTF-8 中则是可选的。正常情况下想去掉这个字符可以用 dos2unix 命令。或者粗暴一点的用

```
sed -i 's/^\xef\xbb\xbf//g' filename
```

---

### 删除含有特殊符号的目录

经常在 Linux 中会遇到一些目录的名称是带有空格的，这种名称对 Linux 而言是不太友好的。如果不注意会导致严重的错误，尤其是在脚本中，如果对有空格的文件名而言，可能会只处理空格前半部的内容，如果前半部分恰好有对应的目录存在，那这个操作就很危险了。**所以脚本中的变量尽量都用引号扩上**

一般我们可以有如下的方式规避：

+ 通过引号

```bash
$ mkdir " good"
$ rm -rf " good"
```

+ 通过转义

```bash
$ ls hello\ world
```

+ 通配符

```bash
$ rm -i -- *\ *
```

一个小技巧，当文件的开头是 - 的时候，大部分的命令都会将其处理成 flag。只需要在文件前面加上 -- 用于告知命令选项到此结束。这对于大部分的命令而言都是适用的。