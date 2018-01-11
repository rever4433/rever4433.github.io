---
layout:     post
title:      "Java Mail 邮件发送简单Demo"
date:       2017-10-17 10:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

上周公司的项目要求开发邮件发送功能。自己在网上跟着教程边学边做了一下午，现在基本开发完成了。由于一个同事也想看下该怎么写，顺便学习下。所以我就写成了一遍教程，顺便巩固下邮件发送里面的内容。

---

# Demo
第一步，我们先写个Demo，用最快的方式最少的代码向你展示Java Mail 是如何工作的。
我的开发环境是：
1. JDK1.6
2. java-mail.jar 1.45

构建一个简单的邮件发送，一共需要四步：
##### 1.配置发件人邮箱信息以及创建一个存放SMTP服务器地址等参数的Java 配置类。
``` java
        String sendEmailAccount = "Example@outlook.com";          //发件人邮箱
        String sendEmailPassword = "password";          //发件人密码
        String sendEmailSMTPHost = "smtp-mail.outlook.com";       // 发件人邮箱的 SMTP 服务器地址, 此处为Outlook邮箱的SMTP服务器
        String receiveMailAccount = "Example@163.com";          // 收件人邮箱
        final String smtpPort = "587";                   //默认端口号设置为587，也可以设置为465，具体取决于SMTP服务器要求的端口号
        Properties props = new Properties();                    // 使用Java配置类进行配置
        props.setProperty("mail.transport.protocol", "smtp");   // 使用的协议（JavaMail规范要求）
        props.setProperty("mail.smtp.host", sendEmailSMTPHost);   // 发件人的邮箱的 SMTP 服务器地址
        props.setProperty("mail.smtp.auth", "true");            // 需要请求认证
        props.setProperty("mail.smtp.port", smtpPort);
        props.setProperty("mail.smtp.socketFactory.fallback", "false");
        props.setProperty("mail.smtp.starttls.enable", "true");
        props.setProperty("mail.smtp.socketFactory.port", smtpPort );  
```

#####2.创建一个同邮件服务器交互的session
``` java
        Session session = Session.getDefaultInstance(props);
        session.setDebug(true);                                 // debug模式下会在控制台打印出日志，如果不想看日志应该设置为false
```
#####3.创建一封格式化的邮件
```java
        // 1. 创建一封邮件
        MimeMessage message = new MimeMessage(session);
        // 2. From: 发件人
        message.setFrom(new InternetAddress(sendEmailAccount , "ExampleFrom", "UTF-8"));
        // 3. To: 收件人
        message.setRecipient(MimeMessage.RecipientType.TO, new InternetAddress(receiveMailAccount, "ExampleUser", "UTF-8"));
        // 4. Subject: 邮件主题（标题有广告嫌疑，避免被邮件服务器误认为是滥发广告以至返回失败，请修改标题）
        message.setSubject("", "UTF-8");
        // 5. Content: 邮件正文
        message.setContent("<h3>This is a test email.</h3>", "text/html;charset=UTF-8");
        // 6. 设置邮件发件时间
        message.setSentDate(new Date());
        // 7. 保存设置
        message.saveChanges();
``` 
#####4.发送邮件
``` java
        // 1. 根据 Session 获取邮件传输对象
        Transport transport = session.getTransport();
        // 2. 使用 邮箱账号 和 密码 连接邮件服务器
        transport.connect(myEmailAccount, myEmailPassword);
        // 3. 发送邮件, 发到所有的收件地址, message.getAllRecipients() 获取到的是在创建邮件对象时添加的所有收件人, 抄送人, 密送人
        transport.sendMessage(message, message.getAllRecipients());
        // 4. 关闭连接
        transport.close();
```
#####完整代码
整合以上步骤的完整代码如下：
```java
import java.util.Date;
import java.util.Properties;

import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

public class MailExample {
	public static void main(String[] args) throws Exception {
		/**
		 * 1.配置发件人邮箱信息以及创建一个Java 配置类存放SMTP服务器地址等参数。
		 */
		String sendEmailAccount = "Example@outlook.com"; 							// 发件人邮箱
		String sendEmailPassword = "password"; 										// 发件人密码
		String sendEmailSMTPHost = "smtp-mail.outlook.com"; 						// 发件人邮箱的 SMTP 服务器地址, 此处为Outlook邮箱的SMTP服务器
		String receiveMailAccount = "Example@163.com"; 							    // 收件人邮箱
		Properties props = new Properties(); 										// 使用Java配置类进行配置
		props.setProperty("mail.transport.protocol", "smtp"); 						// 使用的协议（JavaMail规范要求）
		props.setProperty("mail.smtp.host", sendEmailSMTPHost); 					// 发件人的邮箱的 SMTP 服务器地址
		props.setProperty("mail.smtp.auth", "true");								// 需要请求认证
		final String smtpPort = "465";                                              // 默认端口号设置为587，也可以设置为465，具体取决于SMTP服务器要求的端口号
                props.setProperty("mail.smtp.port",smtpPort );
		props.setProperty("mail.smtp.socketFactory.fallback", "false");
		props.setProperty("mail.smtp.starttls.enable", "true");
		props.setProperty("mail.smtp.socketFactory.port", smtpPort ); 						
		
		/**
		 * 2.创建一个同邮件服务器交互的session
		 */
		Session session = Session.getDefaultInstance(props);
		session.setDebug(true);
		MimeMessage message = new MimeMessage(session); 								 // 1. 创建一封邮件
		message.setFrom(new InternetAddress(sendEmailAccount, "ExampleFrom", "UTF-8"));  // 2. From: 发件人
		message.setRecipient(MimeMessage.RecipientType.TO, 
				new InternetAddress(receiveMailAccount, "ExampleUser", "UTF-8"));     	 // 3. To: 收件人
		message.setSubject("", "UTF-8");     											 // 4. Subject: 邮件主题（标题有广告嫌疑，避免被邮件服务器误认为是滥发广告以至返回失败，请修改标题）
		message.setContent("<h3>This is a test email.</h3>", "text/html;charset=UTF-8"); // 5. Content: 邮件正文
		message.setSentDate(new Date());  												 // 6. 设置邮件发件时间
		message.saveChanges();  														 // 7. 保存设置
		
		/**
		 * 3.创建一封格式化的邮件
		 */
		Transport transport = session.getTransport();									 // 1. 根据 Session 获取邮件传输对象
		transport.connect(sendEmailAccount, sendEmailPassword);							 // 2. 使用 邮箱账号 和 密码 连接邮件服务器
		transport.sendMessage(message, message.getAllRecipients());						 // 3. 发送邮件, 发到所有的收件地址, message.getAllRecipients() 获取到的是在创建邮件对象时添加的所有收件人, 抄送人,
		transport.close();                     											 // 4. 关闭连接
	}
}
```
这样一个简单的JavaMail的Demo就完成了。快去测试下吧。





