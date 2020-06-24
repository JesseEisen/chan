---
gitlayout: post
title: "shell 脚本实践"
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

---

### 命令查找

`command` 本身是用来寻找命令是否存在。不过查找的范围是在 「内置命令和系统 PATH 」中寻找。常用来做命令检查，如果存在则继续后续的命令操作。惯用法如下：

```shell
$ command -v echo > /dev/null &&  echo "hello"
hello
$ command -v xxx > /dev/null && {  xxx; xxxx }
...
...
```

还有一个点值得注意的是：当在 `.bashrc` 中定义了一个和命令同名的函数，那么直接运行这个命令的时候会优先执行 `.bashrc` 中的函数。For intance：

```shell
$ cat ~/.bashrc
...
function hello() {
	 echo "hello from bashrc"
}
....

$ hello  # apt install hello
hello from bashrc

$ command hello
hello world!
```

可以看到，使用了 command 之后会跳过 .bashrc 中的函数，直接执行命令。 使用 `help command`  可以查看手册。更多讨论参看：[command](https://askubuntu.com/questions/512770/what-is-use-of-command-command) 。
