---
layout:     post
title:      "「读书笔记」《设计模式之禅》(二)之工厂方法模式"
date:       2017-11-10 11:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

工厂方法模式的定义为:
> Define an interface for creating an object , but let subclass decide which class to instantiate. Factory Method lets a  class defer instantiation to subclasses.(定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。)

在工厂方法模式中，抽象产品类Product 负责定义产品的共性，实现对事物最抽象的定义；Creator 为抽象创建类，也就是抽象工厂，具体如何创建产品类是由具体的实现工厂ConcreteCreator完成的。
###1.抽象产品类
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
###2.具体产品类
```java
public class ConcreteProduct1 extends Product{
	@Override
	public void method2() {
       //业务逻辑处理
	}
}
```
###3.抽象工厂类
```java
public abstract class Creator {
	public abstract<T extends Product> T createProduct(Class<T> c);
}
```
###4.具体工厂类
```java
public class ConcreteCreator extends Creator{
	public <T extends Product> T createProduct(Class<T> c) {
		Product product=null;
		try {
			product=(Product)Class.forName(c.getName()).newInstance();
		}catch(Exception e) {
			
		}
		return (T)product;
	}
}
```
###5.场景类
```java
public class Client {
	public static void main(String[] args) {
		Creator creator=new ConcreteCreator();
		Product product=creator.createProduct(ConcreteProduct1.class);
	}
}
```
---

###工厂方法模式的优点
- **良好的封装性，代码结构清晰。**创建对象时只需要知道类名就可以了，不用知道创建对象的艰辛过程，**降低模块间的耦合度**
- **优秀的扩展性**，增加产品类时只需要修改具体的工厂类或者新增一个工厂类就可以完成系统的扩展。
- **屏蔽产品类**，调用者只需要关心产品的接口，而系统的上层模块不会发生变化。如使用JDBC连接数据库，数据库从MySQL切换到Oracle时只需要切换驱动名称。
- 工厂方法模式时典型的**解耦框架**。高层模块只需要知道产品的抽象类，其他的实现类都不用关心，**符合迪米特法则**，我不需要的就不要去交流；也**符合依赖倒置原则**，只依赖产品类的抽象；当然也**符合里氏替换原则**，使用产品子类替换产品父类。

###工厂方法模式的使用场景
- 需要生成对象的地方
- 需要灵活的、可扩展的框架时。如需要设计一个连接邮件服务器的框架，有三种网络协议可供选择：IMAP、HTTP、POP3，可以使用这三种连接方法作为产品类。
- 异构项目中。如使用WebService 与一个非Java项目进行交互时。