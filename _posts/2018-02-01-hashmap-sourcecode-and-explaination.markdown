---
layout: post
title: "浅谈HashMap 的底层原理"
date: 2018-02-01 16:01:34
author: "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

本文整理自漫画：[什么是 HashMap? -小灰的文章](http://zhuanlan.zhihu.com/p/31610616) 。已获得作者授权。

---

HashMap 是一个用于存储 Key-Value 键值对的集合，每一个键值对也叫做**Entry**。这些个 Entry 分散存储在一个数组当中，这个数组就是 HashMap 的主干。
**HashMap 数组每一个元素的初始值都是 Null**。
![](http://upload-images.jianshu.io/upload_images/129905-cfe801221d06697b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###1. Put 方法的原理
调用 Put 方法的时候发生了什么呢？
比如调用 hashMap.put("apple", 0) ，插入一个 Key 为“apple"的元素。这时候我们需要利用一个哈希函数来确定 Entry 的插入位置（index）：
`index = Hash（"apple"）`
假定最后计算出的 index 是 2，那么结果如下：
![](http://upload-images.jianshu.io/upload_images/129905-9bc111821efffb3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是，因为 HashMap 的长度是有限的，当插入的 Entry 越来越多时，再完美的 Hash 函数也难免会出现 index 冲突的情况。比如下面这样：
![](http://upload-images.jianshu.io/upload_images/129905-4f706012834dc07d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时候该怎么办呢？我们可以利用链表来解决。
HashMap 数组的每一个元素不止是一个 Entry 对象，也是一个链表的头节点。每一个 Entry 对象通过 Next 指针指向它的下一个 Entry 节点。当新来的 Entry 映射到冲突的数组位置时，只需要插入到对应的链表即可：
![](http://upload-images.jianshu.io/upload_images/129905-ee2b61ef6e511ef8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新来的 Entry 节点插入链表时，使用的是“头插法。

###2. Get 方法的原理
使用 Get 方法根据 Key 来查找 Value 的时候，发生了什么呢？
首先会把输入的 Key 做一次 Hash 映射，得到对应的 index：
index = Hash（“apple”）
由于刚才所说的 Hash 冲突，同一个位置有可能匹配到多个 Entry，这时候就需要顺着对应链表的头节点，一个一个向下来查找。假设我们要查找的 Key 是“apple”：
![](http://upload-images.jianshu.io/upload_images/129905-379fc475bb897219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一步，我们查看的是头节点 Entry6，Entry6 的 Key 是 banana，显然不是我们要找的结果。
第二步，我们查看的是 Next 节点 Entry1，Entry1 的 Key 是 apple，正是我们要找的结果。
之所以把 Entry6 放在头节点，是因为 HashMap 的发明者认为，后插入的 Entry 被查找的可能性更大。

###3. HashMap 的初始长度

初始长度为 16，且每次自动扩容或者手动初始化的时候必须是 2 的幂。
如何进行位运算呢？有如下的公式（Length 是 HashMap 的长度）：
之前说过，从 Key 映射到 HashMap 数组的对应位置，会用到一个 Hash 函数：
`index = Hash（“apple”）`
如何实现一个尽量均匀分布的 Hash 函数呢？我们通过利用 Key 的 HashCode 值来做某种运算。
`index = HashCode（Key） & （Length - 1）`
下面我们以值为“book”的 Key 来演示整个过程：

1. 计算 book 的 hashcode，结果为十进制的 3029737，二进制的`101110001110101110 1001`。
2. 假定 HashMap 长度是默认的 16，计算 Length-1 的结果为十进制的 15，二进制的 1111。
3. 把以上两个结果做与运算，`101110001110101110 1001 & 1111 = 1001`，十进制是 9，所以 index=9。
   可以说，Hash 算法最终得到的 index 结果，完全取决于 Key 的 Hashcode 值的最后几位。这里的位运算其实是一种快速取模算法。

**HashMap 的 size 为什么必须是 2 的幂?**。这是因为 2 的幂用二进制表示时所有位都为 1，例如 16-1=15 的二进制就是 1111B。我们说了 Hash 算法是为了让 hash 的分布变得均匀。其实我们可以把 1111 看成四个通道，表示跟 1111 做&运算后分布是均匀的。假如默认长度取 10，二进制表示为 1010，这样就相当于有两个通道是关闭的，所以计算出来的索引重复的几率比较大。

想看原作者的文章可以看看这个公众号。
![](http://upload-images.jianshu.io/upload_images/129905-f5ac004374af77ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
