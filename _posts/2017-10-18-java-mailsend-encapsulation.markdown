---
layout:     post
title:      "Java Mail 邮件发送简单封装"
date:       2017-10-18 10:20:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---

上一篇文章我们用写了一个Java Mail 的[Demo](http://www.jianshu.com/p/dfdc2f5bdd5e)，相信你已经可以用那个例子来发送邮件了。但是Demo 有很多的问题。
首先每次发送需要配置的东西很多，包括发件人的邮箱和密码、smtp服务器和SMTP端口号等信息。其次，没有将发送和邮件内容相分离。按照单一职责原则，应该有且仅有一个原因引起类的变更[^设计模式]。最后一个问题是，我们的代码不仅自己用，也很可能让别人调用。别人调用的时候不想去了解邮件发送的细节，调用的人只想传尽量少的参数获得预期的效果。因此让Demo变成可以使用的代码需要我们重新设计代码的结构。
从Demo中我们可以抽象出两种类型的POJO，也就是发件人和邮件。你可能会问收件人怎么办？收件人可以跟邮件POJO放在一起吗？
仔细思考下我们就知道，邮件和收件人应该是分开的。因为如果邮件和收件人放在一起，那么就意味着我的一封邮件只能发送给特定的人了，而实际上我们会把相同的邮件发送给不同的收件人。因此收件人只要作为发送时的参数就可以了。

###1.发件人POJO
此处的代码我引用了CSDN上一篇博文[JAVA邮件发送的简单实现](http://www.cnblogs.com/codeplus/archive/2011/10/30/2229391.html)
```java
/**
 * @Title: MailAuthenticator
 * @author: ykgao
 * @description: 
 * @date: 2017-10-11 下午04:55:37
 */
import javax.mail.Authenticator;
import javax.mail.PasswordAuthentication;
 
/**
 * 服务器邮箱登录验证
 * 
 * @author MZULE
 * 
 */
public class MailAuthenticator extends Authenticator {
 
    /**
     * 用户名（登录邮箱）
     */
    private String username;
    /**
     * 密码
     */
    private String password;
 
    /**
     * 初始化邮箱和密码
     * 
     * @param username 邮箱
     * @param password 密码
     */
    public MailAuthenticator(String username, String password) {
    this.username = username;
    this.password = password;
    }
 
    String getPassword() {
    return password;
    }
 
    @Override
    protected PasswordAuthentication getPasswordAuthentication() {
    return new PasswordAuthentication(username, password);
    }
 
    String getUsername() {
    return username;
    }
 
    public void setPassword(String password) {
    this.password = password;
    }
 
    public void setUsername(String username) {
    this.username = username;
    }
 
}

```
###2.邮件POJO
用于存储邮件主题和内容。
```java
/**
 * @Title: SimpleMail
 * @author: ykgao
 * @description:
 * @date: 2017-10-11 下午04:56:27
 */
public class SimpleMail {
	/** 邮件主题 */
	public String Subject;

	/** 邮件内容 */
	public String Content;

	/**
	 * @return the subject
	 */
	public String getSubject() {
		return Subject;
	}

	/**
	 * @param subject
	 *            the subject to set
	 */
	public void setSubject(String subject) {
		Subject = subject;
	}

	/**
	 * @return the content
	 */
	public String getContent() {
		return Content;
	}

	/**
	 * @param content
	 *            the content to set
	 */
	public void setContent(String content) {
		Content = content;
	}

}
```
###3.邮件发送
设计好了POJO，我们现在需要当然是发送邮件了。在Demo中我们需要配置SMTP服务器，但是我们使用邮箱发送邮件的时候并不需要填写SMTP服务器。其实SMTP服务器大多数的格式是：smtp.emailType.com。此处emailType 就是你的邮箱类型也就是@后面跟的名称。比如163邮箱就是163。不过这个方法也不是万能的，因为outlook邮箱的smtp服务器就不是这个格式，而是smtp-mail.outlook.com ，所以我单独为outlook邮箱写了个例外。
我们还需要群分邮件的功能。这个设计起来很容易，只需要一个单人发送的重载方法，其收件人的参数可以是一个List。
为了减少接口的参数个数，我们把SMTP端口默认为587。
```java
import java.io.UnsupportedEncodingException;
import java.security.GeneralSecurityException;
import java.util.List;
import java.util.Properties;
import javaMailDevelopment.SimpleMail;
import javax.mail.MessagingException;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.AddressException;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMessage.RecipientType;

import com.sun.mail.util.MailSSLSocketFactory;

/**
 * @Title: SimpleMailSender
 * @author: ykgao
 * @description: 邮件发送器
 * @date: 2017-10-11 下午04:54:50
 */

public class SimpleMailSender {

	/**
	 * 发送邮件的props文件
	 */
	private final transient Properties props = System.getProperties();
	/**
	 * 邮件服务器登录验证
	 */
	private transient MailAuthenticator authenticator;

	/**
	 * 邮箱session
	 */
	private transient Session session;

	/**
	 * 初始化邮件发送器
	 * 
	 * @param smtpHostName
	 *            SMTP邮件服务器地址
	 * @param username
	 *            发送邮件的用户名(地址)
	 * @param password
	 *            发送邮件的密码
	 */
	public SimpleMailSender(final String smtpHostName, final String username, final String password) {
		init(username, password, smtpHostName);
	}

	/**
	 * 初始化邮件发送器
	 * 
	 * @param username
	 *            发送邮件的用户名(地址)，并以此解析SMTP服务器地址
	 * @param password
	 *            发送邮件的密码
	 */
	public SimpleMailSender(final String username, final String password) {
		// 通过邮箱地址解析出smtp服务器，对大多数邮箱都管用
		String smtpHostName = "smtp." + username.split("@")[1];
		if (username.split("@")[1].equals("outlook.com")) {
			smtpHostName = "smtp-mail.outlook.com";
		}
		init(username, password, smtpHostName);

	}

	/**
	 * 初始化
	 * 
	 * @param username
	 *            发送邮件的用户名(地址)
	 * @param password
	 *            密码
	 * @param smtpHostName
	 *            SMTP主机地址
	 */
	private void init(String username, String password, String smtpHostName) {
		// 初始化props
		props.setProperty("mail.transport.protocol", "smtp"); // 使用的协议（JavaMail规范要求）
		props.setProperty("mail.smtp.host", smtpHostName); // 发件人的邮箱的 SMTP 服务器地址
		props.setProperty("mail.smtp.auth", "true"); // 需要请求认证
		final String smtpPort = "587";
		props.setProperty("mail.smtp.port", smtpPort);
		// props.setProperty("mail.smtp.socketFactory.class",
		// "javax.net.ssl.SSLSocketFactory");
		props.setProperty("mail.smtp.socketFactory.fallback", "false");
		props.setProperty("mail.smtp.starttls.enable", "true");
		props.setProperty("mail.smtp.socketFactory.port", smtpPort);

		// 验证
		authenticator = new MailAuthenticator(username, password);
		// 创建session
		session = Session.getInstance(props, authenticator);
		session.setDebug(true);
	}

	/**
	 * 发送邮件
	 * 
	 * @param recipient
	 *            收件人邮箱地址
	 * @param subject
	 *            邮件主题
	 * @param content
	 *            邮件内容
	 * @throws AddressException
	 * @throws MessagingException
	 * @throws UnsupportedEncodingException
	 */
	public void send(String recipient, String subject, Object content) throws Exception {
		// 创建mime类型邮件
		final MimeMessage message = new MimeMessage(session);
		// 设置发信人
		message.setFrom(new InternetAddress(authenticator.getUsername()));
		// 设置收件人
		message.setRecipient(RecipientType.TO, new InternetAddress(recipient));
		// 设置主题
		message.setSubject(subject);
		// 设置邮件内容
		message.setContent(content.toString(), "text/html;charset=utf-8");
		// 发送
		Transport.send(message);
	}

	/**
	 * 群发邮件
	 * 
	 * @param recipients
	 *            收件人们
	 * @param subject
	 *            主题
	 * @param content
	 *            内容
	 * @throws AddressException
	 * @throws MessagingException
	 */
	public void send(List<String> recipients, String subject, Object content)
			throws AddressException, MessagingException {
		// 创建mime类型邮件
		final MimeMessage message = new MimeMessage(session);
		// 设置发信人
		message.setFrom(new InternetAddress(authenticator.getUsername()));
		// 设置收件人们
		final int num = recipients.size();
		InternetAddress[] addresses = new InternetAddress[num];
		for (int i = 0; i < num; i++) {
			addresses[i] = new InternetAddress(recipients.get(i));
		}
		message.setRecipients(RecipientType.TO, addresses);
		// 设置主题
		message.setSubject(subject);
		// 设置邮件内容
		message.setContent(content.toString(), "text/html;charset=utf-8");
		// 发送
		Transport.send(message);
	}

	/**
	 * 发送邮件
	 * 
	 * @param recipient
	 *            收件人邮箱地址 @param mail 邮件对象 @throws AddressException @throws
	 *            MessagingException @throws
	 */
	public void send(String recipient, SimpleMail mail) throws Exception {
		send(recipient, mail.getSubject(), mail.getContent());
	}

	/**
	 * 群发邮件
	 * 
	 * @param recipients
	 *            收件人们
	 * @param mail
	 *            邮件对象
	 * @throws AddressException
	 * @throws MessagingException
	 */
	public void send(List<String> recipients, SimpleMail mail) throws AddressException, MessagingException {
		send(recipients, mail.getSubject(), mail.getContent());
	}

}

```
###4.测试代码
代码写完了，现在需要测试下代码是否可行。
```java
import java.util.ArrayList;
import java.util.List;

/**
 * @Title: testMail
 * @author: ykgao
 * @description: 
 * @date: 2017-10-11 下午02:13:02
 *
 */

public class testMail {
	public static void main(String[] args) throws Exception {
        /** 创建一个邮件发送者*/
		SimpleMailSender simpleMailSender=new SimpleMailSender("Example@outlook.com", "password");

        /** 创建一封邮件*/
		SimpleMail mail=new SimpleMail();
		mail.setContent("This is a test email.");
		mail.setSubject("We want to invite you to our home.");
/** 
		List<String> recipients=new ArrayList<String>();
		recipients.add("Example1@qq.com");
        recipients.add("Example2@qq.com");
        recipients.add("Example2@qq.com");
		simpleMailSender.send(recipients, mail);
		
	}
}

```
再次感谢[小瓶子](http://www.cnblogs.com/codeplus/archive/2011/10/30/2229391.html)的博客，我的大部分代码都是从他的博客基础上修改的。
[^设计模式]:引自《设计模式之禅》，秦小波 著