---
layout: post
title: "概念的个人理解"
author: "chan"
date: "2021-01-12"
category: "note"
---

本文主要是记录一些编程相关的概念的理解。这些有已经存在很多年的，有的是新出现的。只看字面很难理解到底在说什么，或者看了字面也无法想到是哪方面的内容。

---

### 声明式编程

Declarative programming：这是一种编程范式，它只描述目标的性质，让计算机明白目标，而不是具体的流程。白话一些就是告诉计算机我要什么，具体怎么能实现目标就不关心了，可以说是没有任何**副作用**的。而相对的是命令式编程，命令式编程简单的说就是：你该如何一步步的进行才能最终实现目标。声明式编程这是一个比较大的概念，从属于它的一些具体的子编程范式有：

- 约束式编程范式
- 领域专属语言(DSL)： 形如正则表达式，标记语言等等
- 函数式编程：比如 Haskell，Ocaml 等一系列的函数式编程语言，尝试最小化状态带来的副作用
- 逻辑式编程：类似于 Prolog 声明关系并对关系进行提问的这种逻辑型形式。

---

### 副作用

上面提到的副作用指的是：当调用函数时，除了返回函数值之外，还会对主调用函数产生附加的影响。比如修改全局变量，修改参数或者改变外部存储等等。

具体参考 [Wikipedia 副作用](https://zh.wikipedia.org/wiki/%E5%89%AF%E4%BD%9C%E7%94%A8_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6))

---

### 最小表达力原则(PLE)

Principle of Least Expressiveness 的一个解释是：

> When programming a component, the right computation model for the component is the least expressive model that results in a natural program.
>
> The least expressive model means that if you can express your idea with a constant, use that, and similarly for lookup tables, state machines, and so on. You should only use a Turing-complete language when you cannot use something simpler—with the caveat not to contort the code.

可以阅读 [《Principle of Least Expressiveness》](https://www.info.ucl.ac.be/~pvr/PrincipleOfLeastExpressiveness.pdf) 获取更深的理解。

---

###  DDD领域驱动设计

简单说是：软件代码的结构及语言（类别名称、类方法、类变量）需符合业务领域中的习惯用法。

