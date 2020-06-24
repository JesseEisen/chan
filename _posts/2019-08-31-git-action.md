---
gitlayout: post
title: "Git cheatsheet"
author: "Chan"
date: "2019-08-30"
category: "note"
---

---

本文是 Git 操作的笔记文档，梳理了一下不同的场景下命令行的操作。诚然在很多情况下我们可以使用 UI 界面的工具可以更加方便的操作和对比。但是命令行在某些方面还是必不可少的。

### 基本配置

在使用 Git 前需要做一些简短的配置。最基本的就是用户名和电子邮件这两个。这两个信息是在提交的代码时用到的。

```bash
git config --global user.name "your name"
git config --global user.email "your email"
```

使用 `--global` 后，设置会全局应用。所设置的内容会在 `~/.gitconfig`  文件中。如果只是想在当前的项目中使用该配置，可以去掉该配置即可。一个简单的命令用来查看当前系统上所有的全局配置，可以使用：

```bash
git config --list
```

如果忘记了某个命令的操作，或者想知道这个命令有哪些选项时，可以使用如下的几个命令来查看文档：

```bash
git help <verb>
git <verb> --help
man git-<verb>
```

---

### 修改比较

Git 有别有其他的版本控制软件，有 **工作区**，**暂存区**，**版本库** 这三个概念。我们一般是在工作区开发，然后将内容提交到暂存区，最后确认无误后，将其提交到版本库。对此我们可以通过 `diff` 命令来比对我们的修改。

```bash
git diff  # work -> staged
git diff -cached  # staged -> head
git diff HEAD # work -> head
```

除此之外，还可以比较当前的工作区与其他不同的版本之间的不同。或者是两个不同的版本之间的不同。

```bash
git diff commit-id filepath
git diff -cached commit-id filepath
git diff commit-id commit-id
```

我们可以将这些不同输出到一个文件中，这个文件一般可以作为补丁使用，这块在团队合作中使用的比较多一些。但是我们一般都是用分支来进行这些操作，因为分支在 git 中真的是很轻量了。

---

### 查看历史

版本控制的一个主要功能就是提供历史查询功能，并能够回到指定的版本中。



---

### 分支检出

在 Git 中可以检出分支，可以检出 Tag，可以检出指定的版本和文件。

+ 检出 tag

```
git checkout tag
```

+ 检出分支

```
git checkout branch-name
```

+ 检出文件

```
git checkout [commit-id] -- filename
```





---

### Tag 标签

对某个提交打上对应的 tag 分支。

+ 列出 tag

```
git tag
git tag -l "v1.*"   // filter 
```

+ 创建标签(附加信息)

```
git tag -a v1.0 -m "version v1.4"
```

+ 创建标签(轻量)

```
git tag v1.1
```

+ 后期增加标签

```
git tag -a v1.2 commitid
```

+ 推送标签到远程

```
git push origin --tags // 推送所有
git push origin v1.1   // 推送 v1.1 这个tag
```

+ 删除标签

```
git tag -d v1.1   //本地删除
git push origin :refs/tags/v1.1  //删除远程
```

+ 检出指定的 tag

```
git checkout 1.1
```

---

### Log 查看

查看 log 除了简单的查看版本号和提交信息之外，我们很多时候还想看到对应的提交里面包含哪些文件，这次提交都修改了什么等等。这些通过工具可以轻松的看到，但是命令行同样可以轻松的完成，只不过需要记住一些命令。

+ 列出对应版本中提交的文件

```
git log --stat
```

