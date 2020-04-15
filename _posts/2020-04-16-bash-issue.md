---
gitlayout: post
title: "shell 脚本操作"
author: "Chan"
date: "2020-04-16"
category: "note"
---

## W

这篇笔记主要关于 shell 脚本完成一些常见任务的最佳实践。但限于知识的边界，下面的实现方式并不一定是最佳的。仅供参考，具体问题具体分析。

---

### 合并文件

对于不同场景下的合并文件所使用的方法也不尽相同，下面列举一些不同场景下最直接的解决方案。这类操作使用 awk 相对而言比较轻松一些。

+ 两个文件行数相同，单纯的在每一行后面追加

```bash
$ paste -d "\t" file1 file2
---
$ awk 'NR==FNR {a[NR]=$0;nr=NR;} NR>FNR {print a[NR-nr]"\t"$0}' file1 file2
$ awk '{getline f2 < "file2"; print $0"\t"f2}' file1  # 使用getline 读取文件
```

