---
gitlayout: post
title: "postgreSQL 入门到……"
author: "Chan"
date: "2020-04-17"
category: "note"
---

### 创建用户

postgreSQL 安装后会有一个 `postgres` 用户，一般我们需要切换到这个用户，然后进行用户的创建。操作如下：

```bash
$ su - postgres
-bash-4.2$ psql
postgre=# create user username with password 'xxxxx';
postgre=# create database databaseName owner username;
postgre=# grant all privileges on database databaseName to username;
```

---

### 登录

采用用户名密码登陆 postgres 客户端：

```bash
$ psql -h hosturl -U username -d databasename -p port
```

输入对应的密码就可以登录进入指定的 database 中。

> 在一些编程库中会遇到一个错误： sslmode required not support 。 通过加入  sslmode=disable 解决

---

postgresql 支持将指定的数据库导出到一个 sql 文件中，同时在另外的数据库中进行导入。操作命令如下：

```bash
$ pg_dump -h hosturl -U username databaseName>/path/to/save/dum.sql
$ psql -h hosturl -d databaseName -U username -f dum.sql
```

