---
layout: post
title: "openssl 和 libssl 库的蹚水"
author: "chan"
date: "2020-06-03"
category: "tech"
---

### W

之前的经验都是使用 libcurl 时需要附带上 openssl 的支持。并未直接上手使用 libssl 库的操作。最近得幸体验了一下 openssl ，浅浅的了解了一些加密算法。文章内容是基于 `openssl 1.1.1g` 版本。在此之前可以阅读 [halfrost 有关加密的一系列文章](https://halfrost.com/tag/cryptography/)。 本篇文章不会过多的描述算法的原理，会比较侧重于实际使用。命令行和加密算法的接口使用。

我们还需要明确两个概念：摘要、加解密。 摘要一般是用于数字签名和消息认证，比如常用的 MD5/SHA 等，特点就是对于同一消息内容，使用同一个摘要算法得到的结果总是一样的，而当修改原消息其中的任意一个字符，都会引起最终摘要的结果的不同。摘要不会对消息体本身有任何的修改。而加密算法则是对消息本身进行混淆，使得呈现出来的密文无法直接阅读理解其中的内容，同时还需要保证在拥有解密方法的一方可以正确的还原出明文信息。

正常加密后的密文所组成的内容都是杂乱无规律的，所以大多数情况我们会通过 base64 的二进制表现方法输出内容。 base64 只是一种编码模式，其规则是固定的，所以任何人都可以可以正向和逆向的解析。64 指的是 [a-zA-z0-9] 这 62 个字符，同时另加两个可打印字符，一般为 `/` 和 `+` 这两个字符。 我们还会看到 base64 编码后的内容中还有 `=`  这个字符一般用来作为后缀使用。 

---

### 命令行

一般情况下，我们优先使用命令行来做验证，如果命令行可以执行通过，那么后面的接口调用就是一个水到渠成的过程了。openssl 提供了目前主流的摘要/加解密算法，功能很全面。

###  摘要

在 Linux 中，计算摘要的命令也有很多，比如：`md5sum   sha1sum   sha224sum   sha256sum   sha384sum   sha512sum`  等。我们同样可以用他们来计算摘要。使用 openssl 计算摘要也是很简单的。

```bash
$ openssl md5  filename
$ openssl sha  filename
$ openssl sha256 filename
$ ....
```

你会发现，这个和直接使用 Linux 命令差别不大。但是我们常用的方式是：对明文计算出摘要，然后会把摘要加密保存。这样在解开密文后，对解密后的内容做一次摘要，然后和解密后的摘要进行比对，如果一致，则表明明文没有被篡改。我们可以使用 openssl 模拟这个过程。

```shell
# 使用 rsa 私玥对摘要加密
$ openssl dgst -sha256 -sign rsaPrivateKey.pem -out data.sig rawfile
$ ls -lh data.sig
Permissions Size User  Date Modified Name
.rw-r--r--   256 jesse  6 6  9:55    data.sig

# 验证
$ openssl dgst -sha256 -verify rsaPubkey.pem -signature data.sig rawfile
Verified OK

## 我们在 rawfile 最后添加一个字符， 再次验证一下
$ echo "=" >> rawfile
$ openssl dgst -sha256 -verify rsaPubkey.pem -signature data.sig rawfile
Verification Failure
```

可以看到只有是同一份文件，其计算出来的摘要才是一致的。这个是一个基本的数字签名的形式。上面加密的过程中使用到了 rsa 的公私钥文件。关于 rsa 的内容会在下面说到。

上述命令我们是将摘要加密后保存到文件中，我们可以看下这个文件的内容，并尝试将其输出 base64 的编码格式。

```bash
$ cat data.sig
wd�ʟ���ї���)jHs�8��L]X�.�2�����7Ψ`���L�4�z�:j;jAÖ��
+��h[��\��_)���ީ����i�C��֟l�aZ.���+��������%b�ǧ�&x�.�R�T��=5�̒4��O��V+g+���*�y������J�9|]���os��o
                                      w��;"y�I��V�4�ȼ
                                                     x�PӻQb�TZ�
$ cat data.sig | openssl base64 
E5Ybyw0ftJed78n0/9GXkZx9ie8wLM0vuFVJ/wM66RlYDkR6Ql+Lh+s3zqhg1dsV
qB1M8zTxiHqvOmoEO2pBw5a7sg13ZInKn7sJpq6zKWpIc8U49txMXVjeLpYyoKHg
ywvxGucumk1ReMaCtJOS5yViqcen4SZ41S6qUuJUEeGe6T0SNefMkjSby0/m1FYr
ZyvFEwQEmuQquXmfF4Kp5db7SpsfOXxdkxep029zvY9vDSvf/xNoW7nHXK2eXymM
jfqiDt6ptu/v2mnfQ8DQ1p9sxWFaLsh/ke8rxOvgAsUMd4mGOyJ5+EkAjMFWkTSW
yLwLeN1Q07tRYgGqVFoarw==
```

可以看到原始的内容就是一堆二进制的数，在终端上直接显示大多是乱码，而编码成 base64 格式后，输出的内容比较可读。 

---

除此之外还有一个 **HMAC**（Hash-based message authentication code ）算法，原本的名称 Keyed-hash message authentication code。 注意其中的 keyed-hash 。也就是说在生成摘要的时候需要基于一个 key。 正常的 MAC 算法是可以通过碰撞来破解的。但是如果在 hash 的过程中加入了 key，也可以称之为 salt 的话，这就会加大碰撞的难度。我们一般会使用如下的命令来生成 HMAC 的摘要。

```bash
$ echo "Hello" | openssl sha256 -hmac "world"
(stdin)= f4e006393210bb62c76d1ae02fa54a5b1d3bc0eb8a79ef8df1a6268eb028e63e

```

而当没有 `world` 的话，一般无法计算生成出相同的散列值的。如果你熟悉一些云存储服务器的 SDK。 一般会分配一个 secretKey ，然后生成在请求头中加入中的 verify 字段中使用 HMAC-SHA1 的散列。这个 secretKey 客户端和服务端都有的。因此可以作为一个校验。

**有关摘要可以通过 `openssl dgst -help`  查看选项，虽然没有 -help 这个选项，但是会触发打印用法。**

---

### 加密

我们为了让信息完整，正确，可信任的从一方传输到另一方，可以说是费劲心思。想兼容用户体验好和不可破解这两个目的方式可以说是非常的难。目前以软件做的加解密都有一定的机会被破解掉。下面主要是从几种常见的加密算法说起，看看 openssl 是如何来完成这些的。这个过程是为了我们在使用 API 的时候打一下一些基础。

**对称加密**

对称加密就是通过同一个密钥能够实现加密和解密。只要第三方不知道这个密钥，就很难破解传输的密文。使用的比较多的是 AES 算法。这种加密方式需要通信双方已经协商好了密钥，而这个密钥不会在公共网络上进行传输。基于此场景下。则安全性就能得到一定的保证。但是如果密钥泄漏，所有的一切隐藏就无济于事了。

对称加密的原理也比较好理解，就是一个异或计算。 `a^b = c;  c^b = a;`  b 就是那个密钥。

