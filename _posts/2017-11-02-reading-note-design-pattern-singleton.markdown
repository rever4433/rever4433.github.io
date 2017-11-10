---
layout:     post
title:      "[读书笔记]《设计模式之禅》(一)之单例模式"
date:       2017-11-02 10:57:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

单例模式是一个比较简单的模式，定义如下：
Ensure a class has only one instance, and provide a global point of access to it.(确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例)
##1.饿汉式
``` java
/**
 * 
 * @Title: Singleton
 * @description: 单例模式-饿汉式
 * @author: gaoyakang
 * @date: 2017年11月2日 下午10:25:42
 *
 */
public class Singleton {
	private static final Singleton singleton=new Singleton();
	private Singleton() {
		//确保不产生多个对象
	}
	//通过getInstance方法获得实例对象
	public static Singleton getInstance() {
		return singleton;
	}
	//类中的方法尽量是static
	public static void doSomething() {
		System.out.println("This is a singleton.");
	}
}
```
##2.懒汉式-线程不安全
```java
/**
 * @Title: Singleton
 * @description: 单例模式-懒汉式
 * @author: gaoyakang
 * @date: 2017年11月2日 下午10:28:48
 * 
 */
public class Singleton {
	public static Singleton singleton;
	public Singleton() {
		
	}
	public static Singleton getSingleton() {
	if(singleton==null) {
		singleton=new Singleton();
	}
	return singleton;
	}
}

```
##3.饿汉式——线程安全
线程不安全的单例模式在系统压力增大时内存中可能出现多个单例
```java
/**
 * @Title: Singleton
 * @description: 单例模式-线程安全懒汉式
 * @author: gaoyakang
 * @date: 2017年11月2日 下午10:32:21
 * 
 */
public class Singleton {
	public static  Singleton singleton;
	public Singleton() {
		
	}
	public static synchronized Singleton getSingleton() {
		if(singleton==null) {
			singleton=new Singleton();
		}
		return singleton;
	}
}

```
##4.静态内部类
```java
/**
 * @Title: Singleton
 * @description:
 * @author: gaoyakang
 * @date: 2017年11月4日 上午12:51:10
 * 
 */
public class Singleton {
	private static class SingletonHolder {
		private static final Singleton INSTANCE = new Singleton();
	}
	private Singleton() {

	}
	public static final Singleton getInstance() {
		return SingletonHolder.INSTANCE;
	}
}
```

##5.单例模式扩展——多例模式
```java
import java.util.ArrayList;
import java.util.Random;

/**
 * @Title: Singleton
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月4日 上午1:01:57
 * 
 */
public class Singleton {
	private static int maxNumOfSingleton=2;
	private static ArrayList<Singleton> singletonList=new ArrayList<Singleton>();
	private static ArrayList<String> nameList=new ArrayList<String>();
	private static int countNumOfSingleton=0;
	static {
		for(int i=0;i<maxNumOfSingleton;i++) {
			singletonList.add(new Singleton("皇"+(i+1)+"帝"));
		}
	}
	private Singleton() {
		
	}
	private Singleton(String name) {
		nameList.add(name);
	}
	public static Singleton getInstance() {
		Random random=new Random();
		countNumOfSingleton=random.nextInt(maxNumOfSingleton);
		return singletonList.get(countNumOfSingleton);
	}
	public void foo() {
		System.out.println(nameList.get(countNumOfSingleton));
	}
}

```
臣子参拜的过程:
```java
/**
 * @Title: Minister
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月4日 上午1:08:45
 * 
 */
public class Minister {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
			int ministerNum=5;
			for(int i=0;i<ministerNum;i++) {
				Singleton singleton=Singleton.getInstance();
				System.out.print("第"+(i+1)+"个大臣参拜的是：");
				singleton.foo();
			}
	}

}

```
###多例模式：
> 作为单例模式的拓展，我们可以在设计时决定在内存中有多少实例，方便系统进行拓展，修正单例模式可能存在的性能问题，提高系统的响应速度。例如读取文件，我们可以在系统启动时完成初始化工作，在内存中启动固定数量的reader实例，然后在需要读取文件时就可以快速响应。
---

##单例模式的优点：
- 单例模式在内存中只有一个实例，减少了内存的开支，尤其在一个对象需要频繁创建销毁时，其优势十分明显
- 单例模式可以避免对资源的多重占用，如写文件操作
- 单例模式可以在系统设置全局的访问点，优化和共享资源访问

##单例模式的缺点：
- 单例模式扩展很困难，若要扩展，除了修改代码基本上没有第二种途径可以实现。
- 单例模式对测试不利。
- 单例模式与单一职责原则有冲突。
##单例模式的使用场景:
- 要求生成唯一序列号的环境
- 整个项目中需要一个共享访问点或共享数据，如Web页面上的计数器
- 创建一个对象需要消耗的资源过多，如访问IO和数据库等资源
- 需要定义大量的静态常量和静态方法的环境