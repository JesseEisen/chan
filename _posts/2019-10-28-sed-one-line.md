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