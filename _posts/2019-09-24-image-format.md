---
layout: post
title: "YUV 图片格式介绍"
author: "Chan"
date: "2019-09-24"
category: "tech"
---

---

本文主要关注 YUV 和 JPG 这两种格式，网上少有一些图片格式之间的转换，偏冷门一点的格式转换基本上看不到解决方案。所以在理解了这些格式的构成之后，就能自己手动写一个转格式的程序。

### YUV 

YUV 通常我们需要拆开看，其中：

+ Y  表示的是亮度，或者常说的灰度。Luminance
+ UV 表示的是色度，描述色彩和饱和度。Chrominance

这样的格式，在只有 Y 分量的情况下，同样可以描述一幅图片，只不过是一张黑白色的图片。除此之外，我们还要知道存储的格式，一般分两种：

+ Planar 即先存一组 Y 分量，接着一组 U 分量，继续 V 分量。
+ Packet 即 YUV 分量是连续交叉的存放的。

**格式**

通常 YUV 有 444， 422， 420 这三种表达方式。简单的叙述如下：

+ `4:4:4` 采样，表示的是**每**个 Y 分量对应着一个 UV 分量
+ `4:2:2` 采样，表示的是**两**个 Y 分量对应着一个 UV 分量
+ `4:2:0` 采样，表示的是**四**个 Y 分量对应着一个 UV 分量

一个经典的图表示如下：

![img](https://static.oschina.net/uploads/img/201412/08141454_ACE9.jpg)

其中黑色的点表示的是 Y 分量，空心点表示的 UV 分量组。 

---

### 存储

说完了格式，我们需要了解在实际使用过程中，YUV 格式的文件是如何存储的。在最开始我们提到了 Packed 和 Planar 这两种格式，下面分别说一下如何这两种格式是如何存储的。

+  Packed

在内存的体现中，packed 是按照 YUV 这一组进行的。对于 `4:2:2`  这样的布局，一般有 YUY2， UYVY。 简单的布局如下：

1. YUY2

Y0|U0|Y1|V0  Y2|U1|Y3|V1  Y4|U2|Y5|V2

2. UYVY

U0|Y0|V0|Y1 U1|Y2|V1|Y3  U2|Y4|V2|Y5

+ Planar

planar 的格式在内存操作上可能会比较的好理解一些，基本的原则就是三个分量各自按照一组排列，只是在数量上按照一定的比例进行。一般我们会分为 YUV420P， YUV420SP。实际上我们还会更加细分。如下：

> I420 -> YUV420P <- YV12
>
> NV12 -> YUV420SP <-NV21

内存的布局就很简单了，对于 YUV420P 而言，上面两种的布局如下：

YYYYYYYY UU VV  I420

YYYYYYYY VV UU  YV12

YYYYYYYY UVUV   NV12

YYYYYYYY VUVU  NV21

---

### 转换

在了解了内存的布局之后，在 YUV 格式内部的转换就是内存的组合，还是比较简单的。比如从 YUV420P 转换到 YUV420SP 可以写成如下的方式：

```c
/* Y--------------------------------------------------*/
    pDst = pLuma;
    for (u32Row = 0; u32Row < u32LumaHeight; ++u32Row)
    {
        fread(pDst, u32Width, 1, pFile);
        pDst += u32LumaStride;
    }

    if (PIXEL_FORMAT_YUV_400 == pstFrame->enPixelFormat)
    {
        return HI_SUCCESS;
    }
    else if (PIXEL_FORMAT_YVU_SEMIPLANAR_420 == pstFrame->enPixelFormat)
    {
        u32ChromaHeight = u32ChromaHeight >> 1;
    }

    pTemp = (HI_U8*)malloc(u32ChromaStride);
    if (HI_NULL == pTemp)
    {
        SAMPLE_PRT("vgs malloc failed!.\n");
        return HI_FAILURE;
    }
    memset(pTemp, 0, u32ChromaStride);

    /* U--------------------------------------------------*/
    pDst = pChroma + 1;
    for (u32Row = 0; u32Row < u32ChromaHeight; ++u32Row)
    {
        fread(pTemp, u32Width/2, 1, pFile);
        for (u32List = 0; u32List < u32ChromaStride; ++u32List)
        {
            *pDst = *(pTemp + u32List);
            pDst += 2;
        }
        pDst = pChroma + 1;
        pDst += (u32Row + 1) * u32LumaStride;
    }

    /* V--------------------------------------------------*/
    pDst = pChroma;
    for (u32Row = 0; u32Row < u32ChromaHeight; ++u32Row)
    {
        fread(pTemp, u32Width/2, 1, pFile);
        for(u32List = 0; u32List < u32ChromaStride; ++u32List)
        {
            *pDst = *(pTemp + u32List);
            pDst += 2;
        }
        pDst = pChroma;
        pDst += (u32Row + 1) * u32LumaStride;
    }

```

这一段是读取一个 YUV420P 格式的文件并将去存成 NV21 格式。 