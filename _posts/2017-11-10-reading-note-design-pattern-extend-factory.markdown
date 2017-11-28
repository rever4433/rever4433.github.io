---
layout:     post
title:      "[读书笔记]《设计模式之禅》(三)之扩展工厂方法模式"
date:       2017-11-10 14:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

## 一、缩小为简单工厂模式
考虑这样一个问题：一个模块仅需要一个工厂类，没有必要把它产生出来，使用静态的方法就可以了。类图如下
![image.png](http://upload-images.jianshu.io/upload_images/129905-a211adb5c795c214.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出简单工厂模式，是把抽象的工厂类变为静态的工厂类，简化了类的创建过程。需要变更的源码仅仅是HumanFactory和NvWa类。之前的笔记中没给出书上的例子，考虑到只写出抽象的工厂方法模式例子不能直观的提现出特点，所以在本篇中给出完整的例子：
#### 1. Human类接口
```java
package extendFactoryMethodPattern;
public interface Human {     
	//每个人种的皮肤都有相应的颜色    
	public void getColor();    
	//人类会说话    
	public void talk(); 
	}
```
#### 2. Human类实现
##### 2.1 黑人的实现
```java
package extendFactoryMethodPattern;

public class BlackHuman implements Human {
	public void getColor() {
		System.out.println("黑色人种的皮肤颜色是黑色的！");
	}

	public void talk() {
		System.out.println("黑人会说话，一般人听不懂。");
	}
}

```
##### 2.2 白人实现

```java
package extendFactoryMethodPattern;

public class WhiteHuman implements Human {
	public void getColor() {
		System.out.println("白色人种的皮肤颜色是白色的！");
	}

	public void talk() {
		System.out.println("白色人种会说话，一般都是但是单字节。");
	}
}
```
##### 2.3 黄种人的实现
```java
package extendFactoryMethodPattern;

public class YellowHuman implements Human {
	public void getColor() {
		System.out.println("黄色人种的皮肤颜色是黄色的！");
	}

	public void talk() {
		System.out.println("黄色人种会说话，一般说的都是双字节。");
	}
}
```
#### 3.工厂类(静态的)
```java
package extendFactoryMethodPattern;

public class HumanFactory {
	public static <T extends Human> T createHuman(Class<T> c) {

		// 定义一个生产出的人种
		Human human = null;
		try {
			// 产生一个人种
			human = (Human) Class.forName(c.getName()).newInstance();
		} catch (Exception e) {
			System.out.println("人种生成错误！");
		}
		return (T) human;
	}
}
```
#### 4.实现类（女娲）
```java
package extendFactoryMethodPattern;

public class NvWa {
	public static void main(String[] args) {
		// 女娲第一次造人，火候不足，于是白色人种产生了
		System.out.println("--造出的第一批人是白色人种--");
		Human whiteHuman = HumanFactory.createHuman(WhiteHuman.class);
		whiteHuman.getColor();
		whiteHuman.talk();
		// 女娲第二次造人，火候过足，于是黑色人种产生了
		System.out.println("\n--造出的第二批人是黑色人种--");
		Human blackHuman = HumanFactory.createHuman(BlackHuman.class);
		blackHuman.getColor();
		blackHuman.talk();
		// 第三次造人，火候刚刚好，于是黄色人种产生了
		System.out.println("\n--造出的第三批人是黄色人种--");
		Human yellowHuman = HumanFactory.createHuman(YellowHuman.class);
		yellowHuman.getColor();
		yellowHuman.talk();
	}
}
```
#### 简单工厂模式的特点
> 类图和调用都比较简单，所以称为简单工厂模式（Simple Factory Pattern），也叫做静态工厂模式。其缺点是工厂类的扩展比较困难，不符合开闭原则，但它仍然是一个非常实用的设计模式。

---

## 二、升级为多个工厂类
![image.png](http://upload-images.jianshu.io/upload_images/129905-fc17d778431d30bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个设计模式都是因为具体的问题而产生的。当我们在做一个比较复杂的项目时，如果实体类很多，并且实体类的属性也很多时，把所有的类放到一个工厂类中进行初始化（初始化不仅是new一个对象，还包括为对象设置初始值）会使工厂类的代码结构变得不清晰。
为了让代码结构变得清晰，我们就为每个产品定义一个创造者然后由调用者自己去选择与哪个工厂方法关联。对于女娲造人这个例子，我们可以为每个人种单独设置一个工厂。这样符合单一职责原则。
## 1.多工厂模式的抽象工厂类

```java
public abstract class AbstractHumanFactory{
    public abstract Human createHuman();
}
```
与工厂方法模式的抽象工厂类相比，多工厂模式的抽象工厂类的方法中没有参数，因为工厂的实现类将各自为自己的产品类创建对象。
#### 2. 工厂的实现类
##### 2.1 黑人工厂实现
```java
public class BlackHumanFactory extends AbstractHumanFactory {     
    public Human createHuman() {            
         return new BlackHuman();     
          } 
    }
```
##### 2.2 黄种人工厂实现
```java
public class YellowHumanFactory extends AbstractHumanFactory {     
		public Human createHuman(){             
			return new YellowHuman();     
			} 
		}
```
##### 2.3 白人工厂实现
```java
public class WhiteHumanFactory extends AbstractHumanFactory {     
		public Human createHuman(){             
			return new WhiteHuman();     
			} 
		}
```
#### 3.场景类
```java
public class NvWa {
    public static void main(String[] args) {
        //女娲第一次造人，火候不足，于是白色人种产生了             
        System.out.println("--造出的第一批人是白色人种--");

        Human whiteHuman = (new WhiteHumanFactory()).createHuman();
        whiteHuman.getColor();
        whiteHuman.talk();
        //女娲第二次造人，火候过足，于是黑色人种产生了            
        System.out.println("\n--造出的第二批人是黑色人种--");

        Human blackHuman = (new BlackHumanFactory()).createHuman();
        blackHuman.getColor();
        blackHuman.talk();
        //第三次造人，火候刚刚好，于是黄色人种产生了            
        System.out.println("\n--造出的第三批人是黄色人种--");

        Human yellowHuman = (new YellowHumanFactory()).createHuman();
        yellowHuman.getColor();
        yellowHuman.talk();
    }
}
```
#### 多工厂模式的优点和缺点

>每一个产品类都对应了一个创建类，好处就是创建
>类的职责清晰，而且结构简单，但是给可扩展性和可维护性带来了一定的影响。为什么这么
>说呢？如果要扩展一个产品类，就需要建立一个相应的工厂类，这样就增加了扩展的难度。
>因为工厂类和产品类的数量相同，维护时需要考虑两个对象之间的关系。


#### 比较简单工厂模式和多工厂模式
简单工厂模式，传入工厂的参数是有限制的。如果一旦传入的参数有错误，就不能正确的创建对象。多工厂模式，由于是静态的不需要实例化工厂类，直接调用方法就可以创建需要的对象。


---


##  三、替代单例模式

单例模式的核心要求就是在内存中 只有一个对象，我们可以使用工厂模式替代单例模式。

#### 创建一个单例类

```java
public class Singleton {        
//不允许通过new产生一个对象     
	private Singleton(){
	}     
	public void doSomething(){             
		//业务处理     
		}
 }
```

  

#### 生成单例的工厂类

```java
public class SingletonFactory {
	private static Singleton singleton;
	static {
		try {
			Class cl = Class.forName(Singleton.class.getName()); // 获得无参构造
			Constructor constructor = cl.getDeclaredConstructor(); // 设置无参构造是可访问的
			constructor.setAccessible(true); // 产生一个实例对象
			singleton = (Singleton) constructor.newInstance();
		} catch (Exception e) { // 异常处理
		}
	}
	public static Singleton getSingleton() {
		return singleton;
	}
}
```

>  通过获得类构造器，然后设置访问权限，生成一个对象，然后提供外部访问，保证内存中的对象唯一。

---

## 四、延迟初始化

一个对象被消费完毕后，并不立刻释放，工厂类保持其初始状态，等待再次被使用，这就是延迟初始化。它的使用场景可以参考作者的原话：

> 延迟加载框架是可以扩展的，例如限制某一个产品类的最大实例化数量，可以通过判断 Map中已有的对象数量来实现，这样的处理是非常有意义的，例如JDBC连接数据库，都会 要求设置一个MaxConnections最大连接数量，该数量就是内存中最大实例化的数量。延迟加载还可以用在对象初始化比较复杂的情况下，例如硬件访问，涉及多方面的交互，则可以通过延迟加载降低对象的产生和销毁带来的复杂性。



#### 抽象产品类

```java
public abstract class Product {
	// 产品类的公共方法
	public void method1() {
		// 业务逻辑处理
	}
	// 抽象方法
	public abstract void method2();
}
```

#### 具体产品类

```java
public class ConcreteProduct1 extends Product {
	@Override
	public void method2() {
		// TODO Auto-generated method stub
	}
}
```

```java
public class ConcreteProduct2 extends Product{
	@Override
	public void method2() {
		// TODO Auto-generated method stub
	}
}
```

#### 延迟加载的工厂类

```java
package lazy_initialization;
import java.util.HashMap;
import java.util.Map;

public class ProductFactory {
	private static final Map<String, Product> prMap = new HashMap();
	public static synchronized Product createProduct(String type)
			throws Exception {
		Product product = null;
		// 如果Map中已经有这个对象
		if (prMap.containsKey(type)) {
			product = prMap.get(type);
		} else {
			if (type.equals("Product1")) {
				product = new ConcreteProduct1();
			} else {
				product = new ConcreteProduct2();
			}
		}
		// 同时把对象放到缓存容器中
		prMap.put(type, product);
		return product;
	}
}

```

#### 延迟初始化的作用

> 延迟加载框架是可以扩展的，例如限制某一个产品类的最大实例化数量，可以通过判断 Map中已有的对象数量来实现，这样的处理是非常有意义的，例如JDBC连接数据库，都会 要求设置一个MaxConnections最大连接数量，该数量就是内存中最大实例化的数量。延迟加载还可以用在对象初始化比较复杂的情况下，例如硬件访问，涉及多方面的交互，则可以通过延迟加载降低对象的产生和销毁带来的复杂性。

#### 总结

本篇中扩展工厂方法模式一共有四种，分别是简单工厂模式、多工厂模式、替代单例模式和延迟初始化。

**简单工厂模式**是简化的工厂模式，它不需要抽象的工厂类，而是使用静态的工厂类，简化了类创建的过程。

**多工厂模式**的目的是提高代码结构的清晰度，让每种产品的创建对应一个专门的工厂类，这样在创建对象的时候我们无需传递参数，而是直接调用相应的工厂就可以创建产品，工厂类的职责更清晰。

**替代单例模式**的目的是为了解决单例模式存在的问题。单例模式扩展困难且不符合单一职责原则，因此我们用工厂方法模式去创建单例，提高了扩展性，且符合单一职责原则。

**延迟初始化**的用途有两个，一是为了限制某一对象在内存中的最大数量，二是为了降低频繁创建销毁对象时带来的复杂度。
