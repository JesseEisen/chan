---
gitlayout: post
title: "工具操作小记"
author: "Chan"
date: "2019-12-12"
category: "note"
---

### 引 

总结一些工具不太方便的地方，但是又不太适合自动化处理的地方。方便后续查阅和使用。

---

## Gitlab Release

gitlab 的发布操作还是比较原始的，还得基于 API 的操作才行。总结下面的几个步骤：

**Step1:创建一个 PRIVATE-TOKEN**

> 在用户设置页面找到 Access Token， 创建一个 token， 创建之后需要注意及时保存这个 token，因为页面刷新后就找不到这个 token 了。（这一点很蛋疼）

**Step2:上传要发布的内容**

> 正常的发布都会带上编译好的库或者可执行文件，所以这个操作很有必要。一般请求是：
>
> ```shell
> curl --request POST --header "PRIVATE-TOKEN: :token" --form "file=@:file" https://gitlab.deepglint.com/api/v4/projects/:id/uploads
> ```
>
> 只需要修改 `:token`, `:file`, `:id`  为你需要上传的内容即可。正常情况下会返回一个相对于你要发布的项目的一个相对地址。

**Step3:创建一个release**

> 在发布前你可能已经对要发布的版本打上了 tag ，此时就可以直接通过下面的请求进行发布：
>
> ```shell
> curl --header 'Content-Type: application/json' --header "PRIVATE-TOKEN: :token" \
>      --data '{ "name": ":releaseName", "tag_name": ":tagName", "description": ":desc", "assets": { "links": [{ "name": ":fielname", "url": ":url }] } }' \
>      --request POST https://gitlab.deepglint.com/api/v4/projects/:id/releases
> 
> ```
>
> 如你所见，需要填上对应的内容之后就可以发布了。注意这边的 `:url` 是基于上一步操作后的 url 加上 repo 的地址。否则会导致 Release 页面的下载出错。

​           

至此一个简单的发布就完成了。但是如果此时发布错了，也是可以删除的。通过下面这个请求直接删除，但是有时候会 403 forbidden。还没有能搞清楚是为啥。

```shell
curl --request DELETE --header "PRIVATE-TOKEN: :token" "https://gitlab.deepglint.com/api/v4/projects/:id/releases/:tag"
```



一些其他的操作可以参考：

+ [上传文件](https://docs.gitlab.com/ee/api/projects.html#upload-a-file)
+ [Release API 详情](https://docs.gitlab.com/ee/api/releases/index.html)