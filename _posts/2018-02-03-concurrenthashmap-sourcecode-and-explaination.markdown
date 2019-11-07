本文整理自漫画：[什么是 ConcurrentHashMap？ - 小灰的文章 - 知乎](http://zhuanlan.zhihu.com/p/31614308) 。已获得作者授权。

---

HashMap 在高并发下会出现链表环，从而导致程序出现死循环。高并发下避免 HashMap 出问题的方法有两种。一是使用 HashTable，二是使用 Collections.syncronizedMap

但是这两种方法的性能都能差。因为这两个在执行读写操作时都是将整个集合加锁，导致多个线程无法同时读写集合。高并发下的 HashMap 出现的问题就需要 ConcurrentHashMap 来解决了。

### 什么是 ConcurrentHashMap？

ConcurrentHashMap 中有一个 Segment 的概念。

> Segment 是什么呢？Segment 本身就相当于一个 HashMap 对象。
>
> 同 HashMap 一样，Segment 包含一个 HashEntry 数组，数组中的每一个 HashEntry 既是一个键值对，也是一个链表的头节点。

单一的 Segment 结构如下：
![](http://upload-images.jianshu.io/upload_images/129905-a895ae92f7320615.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

像这样的 Segment 对象，在 ConcurrentHashMap 集合中有多少个呢？有 2 的 N 次方个，共同保存在一个名为 segments 的数组当中。

因此整个 ConcurrentHashMap 的结构如下：
![](http://upload-images.jianshu.io/upload_images/129905-8cd1a52a31f8bd31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以说，ConcurrentHashMap 是一个二级哈希表。在一个总的哈希表下面，有若干个子哈希表。

这样的二级结构，和数据库的水平拆分有些相似。

### ConcurrentHashMap 的优势

ConcurrentHashMap 的优势就是采取了**锁分段技术**，每一个 Segment 的读写操作高度自治，Segment 之间互不影响。

**Case1**：不同 Segment 的并发写入

![image.png](http://upload-images.jianshu.io/upload_images/129905-b434c48337e9e79d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不同 Segment 的写入是可以并发执行的。

**Case2：同一 Segment 的一写一读**

![](http://upload-images.jianshu.io/upload_images/129905-0c9fbc6a6a906eae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同一 Segment 的写和读是可以并发执行的。

**Case3：同一 Segment 的并发写入**

![](http://upload-images.jianshu.io/upload_images/129905-a84149f6e60fcb25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Segment 的写入是需要上锁的，因此对同一 Segment 的并发写入会被阻塞。

由此可见，ConcurrentHashMap 当中每个 Segment 各自持有一把锁。在保证线程安全的同时降低了锁的粒度，让并发操作效率更高。

---

### Concurrent 的读写过程

**Get 方法：**

> 1. 为输入的 Key 做 Hash 运算，得到 hash 值。
> 2. 通过 hash 值，定位到对应的 Segment 对象
> 3. 再次通过 hash 值，定位到 Segment 当中数组的具体位置。

**Put 方法：**

> 1. 为输入的 Key 做 Hash 运算，得到 hash 值。
> 2. 通过 hash 值，定位到对应的 Segment 对象
> 3. **获取可重入锁**
> 4. 再次通过 hash 值，定位到 Segment 当中数组的具体位置。
> 5. 插入或覆盖 HashEntry 对象。
> 6. **释放锁**。

读写操作均需要二次定位到 Segment。

---

### ConcurrentHashMap 在调用 Size 方法时如何解决一致性问题？

Size 方法的目的是统计 ConcurrentHashMap 的总元素数量， 自然需要把各个 Segment 内部的元素数量汇总起来。

但是，如果在统计 Segment 元素数量的过程中，已统计过的 Segment 瞬间插入新的元素，这时候该怎么办呢？

ConcurrentHashMap 的 Size 方法是一个嵌套循环，大体逻辑如下：

> 1. 遍历所有的 Segment。
> 2. 把 Segment 的元素数量累加起来。
> 3. 把 Segment 的修改次数累加起来。
> 4. 判断所有 Segment 的总修改次数是否大于上一次的总修改次数。如果大于，说明统计过程中有修改，重新统计，尝试次数+1；如果不是。说明没有修改，统计结束。
> 5. 如果尝试次数超过阈值，则对每一个 Segment 加锁，再重新统计。
> 6. 再次判断所有 Segment 的总修改次数是否大于上一次的总修改次数。由于已经加锁，次数一定和上次相等。
> 7. 释放锁，统计结束。

```java
public int size() {
   // Try a few times to get accurate count. On failure due to
   // continuous async changes in table, resort to locking.
   final Segment<K,V>[] segments = this.segments;
    int size;
    boolean overflow; // true if size overflows 32 bits
    long sum;         // sum of modCounts
    long last = 0L;   // previous sum
    int retries = -1; // first iteration isn't retry
    try {
        for (;;) {
            if (retries++ == RETRIES_BEFORE_LOCK) {
                for (int j = 0; j < segments.length; ++j)
                    ensureSegment(j).lock(); // force creation
            }
            sum = 0L;
            size = 0;
            overflow = false;
            for (int j = 0; j < segments.length; ++j) {
                Segment<K,V> seg = segmentAt(segments, j);
                if (seg != null) {
                    sum += seg.modCount;
                    int c = seg.count;
                    if (c < 0 || (size += c) < 0)
                        overflow = true;
                }
            }
            if (sum == last)
                break;
            last = sum;
        }
    } finally {
        if (retries > RETRIES_BEFORE_LOCK) {
            for (int j = 0; j < segments.length; ++j)
                segmentAt(segments, j).unlock();
        }
    }
    return overflow ? Integer.MAX_VALUE : size;
}
```

为什么这样设计呢？这种思想和乐观锁悲观锁的思想如出一辙。

为了尽量不锁住所有 Segment，首先乐观地假设 Size 过程中不会有修改。当尝试一定次数，才无奈转为悲观锁，锁住所有 Segment 保证强一致性。

---

想看原作者的文章可以看看这个公众号。
![image.png](http://upload-images.jianshu.io/upload_images/129905-71a363c788aa04ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
