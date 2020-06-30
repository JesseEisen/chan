---
layout: post
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

---

### 处理 Pipe 的输入

当需要我们所编写的脚本可以接收 pipe 的输入时。可以使用如下的技巧：

```shell
#!/bin/bash
[ $# -ge 1 -a -f "$1" ] && input="$1" || input="-"
cat $input
```

注意，cat 可以接收 `-`  作为 stdin 的。如果有其他的命令也可以处理 `-` 。则也可以处理上述命令中的 `$input`。 简单解释一下：

`[ $# -ge 1 -a -f "\$1"]`  这个是用来判断脚本的参数是不是只有一个且参数是一个文件， 如果是，则把 "$1" 赋值给 input。 再根据短路判断规则，如果 `||` 前面的判断成功的话就直接结束。如果失败的话，则把 `-` 赋值给 input。<mark> `-` 一般表示的是标准输入。</mark>

---

### Sed 和 Awk 使用 shell 变量

日常脚本编写中我们常会在 sed 或者 awk 中使用 shell 的变量。我们需要做如下的处理：

+ sed

将原有的单引号改成双引号即可

```shell
$ name="stephen"
$ sed -n "s/$name/curry/g" filename
```

+ awk

使用双引号嵌套单引号的方式，或者使用 `-v` 选项传入

```shell 
$ name="stephen"
$ awk 'BEGIN {print("'$name'")}'
$ awk -v awk_var="$name" 'BEGIN {print awk_var}'
```

---

### Bash 命令行参数处理

如果参数比较简单，可以直接使用  `case`  进行。如果稍微复杂一些的，可以使用  `while` 来做。当然还是可以使用 `getopt` 进行。

+ while 

```shell
declare -a paths=()
while [ $# -gt 0 ]; do 
   case $1 in
   	 --A|--longA)
     	   shift
   	 		optA="$1"
   	 		;;
   	 --*)
   	    usage
   	    exit 1
   	    ;;
   	  *)
   	   paths += ( "$1" )
   	   ;;
   esac
   
   shift
 done
```

上述的程序能够处理： `./myscript -A a  b c d`  这样的命令，其中 aptA 的值是 a。paths 的值是 (b c d)。当我们还想以另外的一种形式进行处理时，可以采取如下方式：

```shell
for i in "$@"; do
case $i in
		-e=*|--extension=*)
		extension="${i#*=}"
		shift
		;;
-s=*|--static=*)
		static="${i#*=}"
		shift
		;;
--default)
		default=yes
		shift
		;;
*)
		// do nothing
		;;
esac
done
	   		
```

上述程序可以处理这样的形式：`./myscript -e=exe -s=yes --defalut`  。 注意如果需要处理额外的参数，可以在 `*)`  分支上里面处理。

+ getopt

参考[这篇文章](https://www.aplawrence.com/Unix/getopts.html)