---
layout: post
title: "命令行不常用选项记录"
author: "chan"
date: "2020-07-01"
category: "note"
---

### W

Linux 命令经历了这么些年的迭代，几乎能够满足大部分的场景需求。很多情况下，通过设置某些选项即可解决问题。毕竟我所遇到的问题，前人肯定都是遇到过的。这篇笔记主要是用来记录这些不太常用的命令的选项的。

---

### tar

<details>
  <summary>忽略目录前缀</summary>
  问题：当指定了要打包的目录的完整路径时，在打包之后这个路径的目录结构会被保留下来。如果想只保留最后一级目录，可以通过 -C 选项。

```shell
  $ tar -czf target.tar.gz -C /xxx/xxx target
```

`-C` 指定目录前缀

</details>



