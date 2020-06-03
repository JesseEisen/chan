---
gitlayout: post
title: "优秀第三方工具使用"
author: "Chan"
date: "2019-10-21"
category: "note"
---

## 引

每个 Linux 的发行版都会带有一批趁手的功能或者脚本语言的支持。诸如：awk，sed 之流，在某些程度上能够让生产力提升几个等级。不过这些工具也有一定的局限性，同时随着时间的发展和需求的涌现，一些生产力的工具是必须产生的。有些是为了提升原有老的工具的速度的，快在执行命令的诉求上可以说是一个指标。有些则是应对新需求儿产生的，这篇文章主要是介绍一些好的工具常用的使用姿势，以期在关键时候派上用场。

---

## jq

故名思义，一款命令行处理 Json 的工具。Json 作为一个广泛使用的数据交互格式拥有很多实现。而很到时候我们并不想从零开始去读文件，解析 Json 内容，然后在输出或者重组我们的需要。所以命令行工具在此时就显得很有必要，所以 jq 作为这么一款工具很合适。jq 的功能基本上算是很丰富了，可以满足日常的使用。

+ 使用 shell 脚本中的参数

这个诉求应该是很常见的，但是需要掌握一定的使用姿势。首先看下官方文档里面的介绍：

> `--arg name value`:
>
> This option passes a value to the jq program as a predefined variable. If you run jq with `--arg foo bar`, then `$foo` is available in the program and has the value `"bar"`. Note that `value` will be treated as a string, so `--arg foo 123` will bind `$foo` to `"123"`.

所以我们可以使用 `--arg`  将 shell 的变量设置到一个 jq 的变量，这样 jq 在内部就可以使用了。思路很清晰，但是在 jq 内部使用的使用还是要注意的，如果作为 jq 的 filter 来使用的话，需要加上 `[]` 。简单的一个例子：

```shell
#a.json:
# {"1": {"id": 1, "name": "index"}}
$ jq -r --arg i "$i" '.[$i].name' a.json
index
$ i=2
$ jq -r --arg i "$i" '$i' a.json
2
```

如果是直接使用，可以直接通过 $i 进行使用的。

---

+ 数组操作

使用 `.arrname[]`  就可以输出该数组中的所有的内容。数组可以直接使用 `+` 进行连接。