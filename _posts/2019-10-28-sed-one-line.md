---
layout: post
title: "sed cheatsheet"
author: "Chan"
date: "2019-10-28"
category: "note"
---

## 引

`sed`  作为一个流处理器，在命令行里面必须拥有一袭地位。这个古老却很有魅力的工具拥有着巨大的可能，同时拥有正则的加持，更加是如虎添翼。本文整理了一些大部分的 `sed` 的用法。

---

### 重定向

这个重定向和 `shell` 脚本中的不一样，并不是使用 `>>`  标志，而是直接通过 `w`  命令。

```
sed 'w output.txt' source.txt
sed -n '/xxx/,$ w output.txt' source.txt
```

注意一般是使用 `-i` 选项来强制将原文件修改掉，不要使用 `shell` 的重定向来定向到原文件中。

---

### 替换命令

`s`  可以说是最常用的命令了，不过 `s` 可以接很多个 `flag` 来帮助更好的完成任务，其中有一个 `e` 。它的作用是当有替换发生时，会将 `shell` 命令放到 `pattern space` 中，然后会被执行，执行之后的结果会继续被放到  `pattern space`  中。比如:

```shell
$ cat file.txt
a.txt
b.txt
$ sed 's/^/ls -l /e' file.txt
-rw-r--r-- 1 root root 1627 Oct 14 14:30 a.txt
-rw-r--r-- 1 root root 807 Oct 14 14:30 b.txt
```

---

### 转换字符

大多数时候我们可以使用替换命令来实现，但是有 `y` 这个命令我们就可以这样做：

```
$ cat source.txt
It is a Big dog
$ sed 'y/IB/ib/' source.txt
it is a big dog
```

替换对应位置上的字符。

---

### 分组

使用`()` 进行匹配，使用 `\1 \2` 等来引用：

```shell
$ echo "aaa:bbb:ccc" | sed 's/\(.*\):\(.*\)/\2:\1/'
ccc:aaa:bbb
```

默认 sed 是贪婪匹配的，所以会一次性匹配到第二个冒号。这边有两个分组，所以可以用 `\1 \2` 来引用，上面的命令中将其调换了位置。**sed 是不支持非贪婪模式的，如果非要实现非贪婪则需要结合实际情况 trick 一下**。 此外大多时候推荐使用 perl 来做，但是 perl 用的人也不是很多。Google it！

---

### 匹配 HTML 标签

HTML/XML 的标签都有一定的规律，都是包裹在 `<>`  之中。 可以使用如下的方式操作：

```shell
$ sed 's/\(<[^>]*>\).*//' test.html   # 匹配出前面的 html 标签
$ sed -n 's/<[^>]*>//gp' test.html  # 获取最内部的标签的内容
```

