---
layout:     post
title:      "「源码分析」深入解析Java AtomicInteger 原子类型"
date:       2018-01-11 11:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

## 深入解析Java AtomicInteger原子类型

在进行并发编程的时候我们需要确保程序在被多个线程并发访问时可以得到正确的结果，也就是实现线程安全。线程安全的定义如下：

> 当多个线程访问某个类时，不管运行时环境采用何种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的同步或协同，这个类都能表现出正确的行为，那么这个类就是线程安全的。

举个线程不安全的例子。假如我们想实现一个功能来统计网页访问量，你可能想到用```count++``` 来统计访问量，但是这个自增操作不是线程安全的。```count++``` 可以分成三个操作：

1. 获取变量当前值
2. 给获取的当前变量值+1
3. 写回新的值到变量

假设count的初始值为10，当进行并发操作的时候，可能出现线程A和线程B都进行到了1操作，之后又同时进行2操作。A先进行到3操作+1，现在值为11；注意刚才AB获取到的当前值都是10，所以B执行3操作后，count的值依然是11。这个结果显然不符合我们的要求。

所以我们需要用本篇的主角—— AtomicInteger 来保证线程安全。

AtomicInteger 的源码如下：

```java
package java.util.concurrent.atomic;
import sun.misc.Unsafe;

public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;

    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
      try {
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
      } catch (Exception ex) { throw new Error(ex); }
    }

    private volatile int value;

    public AtomicInteger(int initialValue) {
        value = initialValue;
    }

    public AtomicInteger() {
    }

    public final int get() {
        return value;
    }

    public final void set(int newValue) {
        value = newValue;
    }

    public final void lazySet(int newValue) {
        unsafe.putOrderedInt(this, valueOffset, newValue);
    }

    public final int getAndSet(int newValue) {
        for (;;) {
            int current = get();
            if (compareAndSet(current, newValue))
                return current;
        }
    }

    public final boolean compareAndSet(int expect, int update) {
	return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }

    public final boolean weakCompareAndSet(int expect, int update) {
	return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }

    public final int getAndIncrement() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return current;
        }
    }

    public final int getAndDecrement() {
        for (;;) {
            int current = get();
            int next = current - 1;
            if (compareAndSet(current, next))
                return current;
        }
    }

    public final int getAndAdd(int delta) {
        for (;;) {
            int current = get();
            int next = current + delta;
            if (compareAndSet(current, next))
                return current;
        }
    }

    public final int incrementAndGet() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return next;
        }
    }

    public final int decrementAndGet() {
        for (;;) {
            int current = get();
            int next = current - 1;
            if (compareAndSet(current, next))
                return next;
        }
    }
  
    public final int addAndGet(int delta) {
        for (;;) {
            int current = get();
            int next = current + delta;
            if (compareAndSet(current, next))
                return next;
        }
    }

    public String toString() {
        return Integer.toString(get());
    }


    public int intValue() {
	return get();
    }

    public long longValue() {
	return (long)get();
    }

    public float floatValue() {
	return (float)get();
    }

    public double doubleValue() {
	return (double)get();
    }

}
```



---

我们先看原子整型类中定义的属性

```java
   // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
      try {
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
      } catch (Exception ex) { throw new Error(ex); }
    }
```



Unsafe是JDK内部的工具类，主要实现了平台相关的操作。下面内容引自

> [sun.misc.Unsafe](http://hg.openjdk.java.net/jdk7u/jdk7u/jdk/file/tip/src/share/classes/sun/misc/Unsafe.java)是JDK内部用的工具类。它通过暴露一些Java意义上说“不安全”的功能给Java层代码，来让JDK能够更多的使用Java代码来实现一些原本是平台相关的、需要使用native语言（例如C或C++）才可以实现的功能。该类不应该在JDK核心类库之外使用。

Unsafe的具体实现跟本篇的目标关联不大，你只要知道这段代码是为了获取value在堆内存中的偏移量就够了。偏移量在AtomicInteger中很重要，原子操作都靠它来实现。

### Value的定义和volatile

AtomicInteger 本身是个整型，所以最重要的属性就是value，我们看看它是如何声明value的

```java
 private volatile int value;
```

我们看到value使用了```volatile ```修饰符，那么什么是```volatile```呢？

volatile相当于```synchronized```的弱实现，也就是说```volatile```实现了类似```synchronized```的语义，却又没有锁机制。它确保对```volatile```字段的更新以可预见的方式告知其他的线程。

```volatile```包含以下语义：

> 1. Java 存储模型不会对valatile指令的操作进行重排序：这个保证对volatile变量的操作时按照指令的出现顺序执行的。
> 2. volatile变量不会被缓存在寄存器中（只有拥有线程可见）或者其他对CPU不可见的地方，每次总是从主存中读取volatile变量的结果。也就是说对于volatile变量的修改，其它线程总是可见的，并且不是使用自己线程栈内部的变量。也就是在happens-before法则中，对一个valatile变量的写操作后，其后的任何读操作理解可见此写操作的结果。

简而言之volatile 的作用是当一个线程修改了共享变量时，另一个线程可以读取到这个修改后的值。在分析AtomicInteger 源码时，我们了解到这里就足够了。

### 用CAS操作实现安全的自增

AtomicInteger中有很多方法，例如```incrementAndGet()``` 相当于```i++``` 和```getAndAdd()``` 相当于```i+=n```  。从源码中我们可以看出这几种方法的实现很相似，所以我们主要分析```incrementAndGet()``` 方法的源码。

源码如下：

```java
 public final int incrementAndGet() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return next;
        }
    }

 public final boolean compareAndSet(int expect, int update) {
	return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
```

```incrementAndGet()``` 方法实现了自增的操作。核心实现是先获取当前值和目标值（也就是value+1），如果```compareAndSet(current, next)``` 返回成功则该方法返回目标值。那么compareAndSet是做什么的呢？理解这个方法我们需要引入CAS操作。

在大学操作系统课程中我们学过独占锁和乐观锁的概念。独占锁就是线程获取锁后其他的线程都需要挂起，直到持有独占锁的线程释放锁；乐观锁是先假定没有冲突直接进行操作，如果因为有冲突而失败就重试，直到操作成功。其中乐观锁用到的机制就是CAS，Compare and Swap。

AtomicInteger 中的CAS操作就是```compareAndSet()```，其作用是每次从**内存中根据内存偏移量（```valueOffset```）取出数据**，将取出的值跟expect 比较，如果数据一致就把内存中的值改为update。

这样使用CAS就保证了原子操作。其余几个方法的原理跟这个相同，在此不再过多的解释。

没看AtomicInteger  源码之前，我认为其内部是用```synchronized``` 来实现的原子操作。查阅资料后发现```synchronized``` 会影响性能，因为Java中的```synchronized``` 锁是独占锁，虽然可以实现原子操作，但是这种实现方式的并发性能很差。

### 总结

总结一下，AtomicInteger 中主要实现了整型的原子操作，防止并发情况下出现异常结果，其内部主要依靠JDK 中的unsafe 类操作内存中的数据来实现的。volatile 修饰符保证了value在内存中其他线程可以看到其值得改变。CAS操作保证了AtomicInteger 可以安全的修改value 的值。