---
layout:     post
title:      "《设计模式之禅》读书笔记(四)之抽象工厂模式"
date:       2018-01-11 10:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---


### 一、抽象工厂模式的定义

>  Provide an Interface for creating families of related or dependent objects without specifying their concrete classes.(为创建一组相关或相互依赖的对象提供一个接口，而且无需指定他们的具体类。)

抽象工厂模式的通用类图如下：

![](http://upload-images.jianshu.io/upload_images/129905-b8c0aaa7d5cea14c.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


抽象工厂模式是工厂方法模式的升级版本，在有多个业务品种、业务分类时，通过抽象工厂模式产生需要的对象时一种非常好的解决方式。 

在抽象工厂模式中，有一个**产品族**的概念：所谓的产品族，是指**位于不同产品等级结构中功能相关联的产品组成的家族**。抽象工厂模式所提供的一系列产品就组成一个产品族；而工厂方法提供的一系列产品称为一个等级结构。例如制造汽车的左侧门和右侧门，这两个的数量应该是相等的——这是两个对象之间的约束，每个型号的车门都是不一样的，这是产品等级结构约束的。源码实现如下：



#### 1. 抽象产品类

#####1.1 产品A的抽象产品类：

```java
package definationAbstractFactoryPattern;

/**
 * @Title: AbstractProductA
 * @description: 抽象产品实现类A
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:52:58
 * 
 */
public abstract class AbstractProductA {
	//每个产品共有的方法
	public void shareMethod() {
	}
	//每个产品相同方法，不同实现
	public abstract void doSomething();
}

```

######1.2 产品B的抽象产品类：

```java
package definationAbstractFactoryPattern;
/**
 * @Title: AbstractProductB
 * @description: 抽象产品实现类B
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:59:08
 * 
 */
public abstract class AbstractProductB {
	//每个产品共有的方法
	public void shareMethod() {
	}
	//每个产品相同方法，不同实现
	public abstract void doSomething();
}

```

#### 2. 产品实现类

```java
package definationAbstractFactoryPattern;

/**
 * @Title: ProductA1
 * @description: 产品A1的实现类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:54:37
 * 
 */
public class ProductA1 extends AbstractProductA{
	@Override
	public void doSomething() {
		System.out.println("产品A1的实现方法");
	}
}

```

```java
package definationAbstractFactoryPattern;
/**
 * @Title: ProductA2
 * @description: 产品A2的实现类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:55:49
 * 
 */
public class ProductA2 extends AbstractProductA{
	@Override
	public void doSomething() {
		// TODO Auto-generated method stub
		System.out.println("产品A2的实现方法");
	}
}

```

```java
package definationAbstractFactoryPattern;
/**
 * @Title: ProductB1
 * @description: 产品B1的实现类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:59:48
 * 
 */
public class ProductB1 extends AbstractProductB{
	@Override
	public void doSomething() {
		// TODO Auto-generated method stub
		System.out.println("产品B1的实现方法");
	}
}

```

```java
package definationAbstractFactoryPattern;
/**
 * @Title: ProductB2
 * @description: 产品B2的实现类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:59:48
 * 
 */
public class ProductB2 extends AbstractProductB{

	/* (non-Javadoc)
	 * @see definationAbstractFactoryPattern.AbstractProductB#doSomething()
	 */
	@Override
	public void doSomething() {
		// TODO Auto-generated method stub
		System.out.println("产品B1的实现方法");
	}

}

```

#### 3. 抽象工厂类

有N个产品族，在抽象工厂类中就应该有N个创建方法

```java
package definationAbstractFactoryPattern;
/**
 * @Title: AbstractCreator
 * @description: 抽象工厂类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午8:57:12
 * 
 */
public abstract class AbstractCreator {
	//创建A产品家族
	public abstract AbstractProductA createProductA();
	//创建B产品家族
	public abstract AbstractProductB createProductB();
}

```

#### 4. 产品的实现类

创建产品是由具体的实现类来完成的
#####4.1 产品等级1的实现类

```java
package definationAbstractFactoryPattern;
/**
 * @Title: Creator1
 * @description: 生产产品等级1的产品
 * @author: gaoyakang
 * @date: 2017年11月19日 下午9:04:09
 * 
 */
public class Creator1 extends AbstractCreator{
	@Override
	public AbstractProductA createProductA() {
		//只生产产品等级为1的A产品
		return new ProductA1();
	}
  
	@Override
	public AbstractProductB createProductB() {
		//只生产产品等级为1的B产品
		return new ProductB1();
	}
}

```

#####4.2 产品等级2的实现类

```java
package definationAbstractFactoryPattern;
/**
 * @Title: Creator2
 * @description: 生产产品等级2的产品
 * @author: gaoyakang
 * @date: 2017年11月19日 下午9:06:03
 * 
 */
public class Creator2 extends AbstractCreator{
	@Override
	public AbstractProductA createProductA() {
		//只生产产品等级为2的A产品
		return new ProductA2();
	}

	@Override
	public AbstractProductB createProductB() {
		//只生产产品等级为2的B产品
		return new ProductB2();
	}
}

```

#### 5. 场景类

```java
package definationAbstractFactoryPattern;
/**
 * @Title: Client
 * @description: 场景类
 * @author: gaoyakang
 * @date: 2017年11月19日 下午9:08:45
 * 
 */
public class Client {
	public static void main(String[] args) {
		//定义出两个工厂
		AbstractCreator creator1=new Creator1();
		AbstractCreator creator2=new Creator2();
		//产生A1对象
		AbstractProductA a1=creator1.createProductA();
		//产生A2对象
		AbstractProductA a2=creator2.createProductA();
		//产生B1对象
		AbstractProductB b1=creator1.createProductB();
		//产品B2对象
		AbstractProductB b2=creator2.createProductB();
      
		a1.doSomething();
		a2.doSomething();
		b1.doSomething();
		b2.doSomething();
	}
}

```

### 二、抽象工厂模式的应用

#### 抽象工厂模式的优点

1. 封装性：不用关心对象是如何创建出来的，而只需要关心接口的。
2. 产品族内的约束为非公开状态：例如男女比例为1.2：1，那么这个关系只要在工厂类中实现就可以了，而高层模块调用时不用关心这个约束。

#### 抽象工厂模式的缺点

最大的缺点是产品族的扩展十分困难，例如我需要增加一个产品C，那么首先我需要增加一个抽象产品类AbstractProductC，其次增加产品C各等级的实现类，最后AbstractCreator还需要增加一个方法createProductC()，并且两个产品等级的实现类也需要有相应的修改，这**严重影响了开闭原则**。

####抽象工厂模式的使用场景

一个对象族或是一组没有任何关系的对象都有相同的约束，则可以使用抽象工厂模式。

####抽象工厂模式的注意事项

抽象工厂模式的缺点是产品族的扩展困难，而不是产品等级的扩展困难。该模式下，产品等级是容易扩展的，增加一个产品等级，只要增加一个工厂类负责新增加出来的产品生产任务即可。在只扩展产品等级这一点上看，抽象工厂模式是符合开闭原则的。

---

### 三、书中女娲造人例子源码

简而言之，这个例子是制造制造三种肤色的人，且人有男女之分。

####1. 人种接口

```java
/**
 * @Title: Human
 * @description: 人种接口
 * @author: gaoyakang
 * @date: 2017年11月13日 上午7:21:02
 * 
 */
public interface Human {
	//每个人有相应的颜色
	public void getColor();
	//每个人都会说话
	public void talk();
	//每个人都有性别
	public void getSex();
}
```

####2. 白色人种

```java
/**
 * @Title: AbstractWhiteHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月13日 上午7:22:33
 * 
 */
public abstract class AbstractWhiteHuman implements Human{
	public void getColor() {
		System.out.println("白色人种的皮肤都是白色的！");
	}
	public void talk() {
		System.out.println("白色人种会说话，一般都是单字节!");
	}
}

```

####3. 黑色人种

```java
/**
 * @Title: AbstractBlackHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月13日 上午7:24:57
 * 
 */
public abstract class AbstractBlackHuman implements Human {

	@Override
	public void getColor() {
		System.out.println("黑色人种的皮肤都是黑色的!");
	}

	@Override
	public void talk() {
		System.out.println("黑人会说话，一般人听不懂。");
	}
}

```

#### 4. 黄色人种

```java
/**
 * @Title: AbstractYellowHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午6:58:18
 * 
 */
public abstract class AbstractYellowHuman implements Human{
	@Override
	public void getColor() {
		System.out.println("黄色人种的皮肤是黄色的！");		
	}
	@Override
	public void talk() {
		System.out.println("黄色人种会说话，一般说的是双音节。");
	}
}

```

#### 5. 各种肤色人种不同性别的实现类

因为有三种肤色两种性别所以一共有6个实现类

#####5.1 黄色女性人种

```java
/**
 * @Title: FamaleYellowHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:04:43
 * 
 */
public class FemaleYellowHuman  extends AbstractYellowHuman{
	@Override
	public void getSex() {
		System.out.println("黄人女性");
	}
}

```

##### 5.2 黄色男性人种

```java
/**
 * @Title: MaleYellowHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:05:36
 * 
 */
public class MaleYellowHuman extends AbstractYellowHuman{
	@Override
	public void getSex() {
		System.out.println("黄人男性");
	}
}

```

##### 5.3 白色女性人种

```java
/**
 * @Title: FemaleWhiteHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:07:49
 * 
 */
public class FemaleWhiteHuman extends AbstractWhiteHuman{
	@Override
	public void getSex() {
		// TODO Auto-generated method stub
		System.out.println("白人女性");
	}
}

```

##### 5.4 白色男性人种

```java
/**
 * @Title: MaleWhiteHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:08:24
 * 
 */
public class MaleWhiteHuman extends AbstractWhiteHuman{
	@Override
	public void getSex() {
		System.out.println("白人男性");
	}
}

```

##### 5.5 黑色女性人种

```java
/**
 * @Title: FemaleBlackHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:06:40
 * 
 */
public class FemaleBlackHuman extends AbstractBlackHuman{
	@Override
	public void getSex() {
		// TODO Auto-generated method stub
		System.out.println("黑人女性");
	}
}

```

#####5.6  黑色男性人种

```java
/**
 * @Title: MaleBlackHuman
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月15日 上午7:07:16
 * 
 */
public class MaleBlackHuman extends AbstractBlackHuman{
	@Override
	public void getSex() {
		System.out.println("黑人男性");
	}
}

```

#### 6. 八卦炉的定义

```java
/**
 * @Title: HumanFactory
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月16日 下午9:13:48
 * 
 */
public interface HumanFactory {
	//制造一个黄色人种
	public Human createYellowHuman();
	//制造一个白色人种
	public Human createWhiteHuman();
	//制造一个黑色人种
	public Human createBlackHuman();
}

```

#### 7. 生产女性的八卦炉

```java
/**
 * @Title: FemaleFactory
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月16日 下午9:15:22
 * 
 */
public class FemaleFactory implements HumanFactory{
	@Override
	public Human createYellowHuman() {
		return new FemaleYellowHuman();
	}

	@Override
	public Human createWhiteHuman() {
		return new FemaleWhiteHuman();
	}

	@Override
	public Human createBlackHuman() {
		return new FemaleBlackHuman();
	}
}

```

#### 8. 生产男性的八卦炉

```java
/**
 * @Title: MaleFactory
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月16日 下午10:02:02
 * 
 */
public class MaleFactory implements HumanFactory{
	@Override
	public Human createYellowHuman() {
		return new MaleYellowHuman();
	}
	@Override
	public Human createWhiteHuman() {
		return new MaleWhiteHuman();
	}
	@Override
	public Human createBlackHuman() {
		return new MaleBlackHuman();
	}
	
}

```

#### 9. 具体场景类（女娲造人）

```java
/**
 * @Title: NvWa
 * @description: 
 * @author: gaoyakang
 * @date: 2017年11月16日 下午10:04:11
 * 
 */
public class NvWa {
	public static void main(String[] args) {
		//第一条生产线，男性生产线
		HumanFactory maleHumanFactory=new MaleFactory();
		//第二条生产线，女性生产线
		HumanFactory feMaleHumanFactory=new MaleFactory();
		
		Human maleYellowHuman=maleHumanFactory.createYellowHuman();
		maleYellowHuman.getColor();
		maleYellowHuman.getSex();
		maleYellowHuman.talk();
		Human femaleYellowHuman=feMaleHumanFactory.createYellowHuman();
		femaleYellowHuman.getColor();
		femaleYellowHuman.getSex();
		femaleYellowHuman.talk();
	}
}

```



