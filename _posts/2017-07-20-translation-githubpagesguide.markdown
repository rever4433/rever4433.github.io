---
layout:     post
title:      "GitHub Pages Guides官方文档翻译之搭建个人和项目页面"
subtitle:   "阅读官方文档，简单搭建GitHub Pages页面"
date:       2017-07-5 10:44:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---


# GitHub Pages 是什么？

GitHub Pages是一项静态网页托管服务。
GitHub Pages可以直接从GitHub仓库为你生成个人、企业或项目页面。获取更多关于不同类型的GitHub Pages站点信息，请参考[个人、企业和项目页面](https://help.github.com/articles/user-organization-and-project-pages/)

你可以使用Jekyll主题选择器在线创建和发布GitHub Pages。如果你喜欢在本地工作，你可以使用桌面版本的GitHub或CMD命令行来发布页面。

GitHub Pages是一项静态网页托管服务，因此不支持PHP、Ruby或Python这样的服务端代码。

---

## GitHub Pages 使用教程
在2016年6月15号之后创建的GitHub Pages 站点和使用github.io 域名的站点都需要使用HTTPS传输协议。如果你的站点在在2016年6月15号之前创建，你可以启用HTTPS支持来分流你的站点。

GitHub Pages 不建议用来存放敏感内容，如密码或信用卡号

GitHub Pages 的使用服从GitHub的服务条款，包括禁止倒卖站点服务。

警告：即使存储GitHub Pages站点的仓库是私有的，这些站点也会在互联网上公开访问。如果你在你的页面仓库里有敏感数据，你可以在发布之前删除它。

--- 

## 使用限制
GitHub Pages 服从以下使用条款：

-  GitHub Pages 源代码库有1GB的容量限制。
- 发布的GitHub Pages 站点大小不应该超过1GB。
- GitHub Pages 每月流量带宽限制为100GB.
- GitHub Pages 站点每小时最多构建10次

如果你的站点超过了上述使用条款，我们就会停止为你的站点服务或者你会收到一封来自GitHub支持中心的邮件，此邮件会给你策略来减少你的站点对我们的服务的影响，包括在你的站点上使用第三方CDN服务，充分利用GitHub的其他功能，诸如发布或迁移到跟符合你的需求的其他主机服务。
除此之外，GitHub Pages 包含以下内容的站点会受到限制:

- 不合法的内容或被我们的服务条款和社区指导所禁止的内容或行为
- 包含暴力或恐吓的内容和行为
- 过量自动化的批量活动（如垃圾邮件）
- 对GiuHub用户或服务妥协的行为
- 含有过度频繁操作
- 含有性暗示的内容
- 不代表你个人或网站观点的内容

如果你对你的使用或使用目的是否包含在这些目录中有任何疑问，请与GitHub 支持中心联系

---



# 配置一个可发布的GitHub Pages 源文件

你可以配置GitHub Pages  来发布你站点的源文件。项目页面和其他符合特定要求的页面的源文件可以放在master、gh-pages、或者是你的master分支下的/docs文件夹中。

如果你的站点是在被命名为<username>.github.io 或<orgname>.github.io 的仓库中的个人或机构页面，那么你不能发布来自不同位置的站点源文件。这种类型的个人仓库名只能使用master分支来发布。
获取更多关于不同类型的GitHub Pages 站点信息，请参考[个人、企业和项目页面](https://help.github.com/articles/user-organization-and-project-pages/)

## 为没有命名空间的仓库设置默认源

发布网站源文件的默认设置取决于你的网站类型和你的页面仓库的分支。
如果你的页面仓库没有master 或gh-pages 分支，你的GitHub 页面发布源会被设置为None并且你的页面不会被发布。

在你同时创建了master 和gh-pages 分支后，你可以设置一个分支作为你的发布源，之后你的页面将会被发布。

如果你fork 或者upload 的页面仓库只包含一个master或者gh-pages分支，你的页面源设置会自动为该分支启用。

## 为你来自master 或gh-pages 的分支启用GitHub Pages

为了选择master 或gh-pages 作为你的发布源，你必须在你的仓库里有一个当前分支。如果你没有master 或gh-pages 分支，你可以创建他们然后返回到源设置来更改你的发布源。

1. 在GitHub 主页，导航栏选择你要设置的GitHub Pages 页面的仓库
2. 在你选择的仓库页面下，点击Settings
3. 在Settings 页面，向下滑动选择master或gh-pages作为你的发布源
4. 点击Save

## 从master 分支下的/docs文件夹发布你的GitHub Pages页面

为了发布你的master分支下的/doc文件夹下的页面源文件，你必须有一个master分支和仓库。
- 在仓库的根目录有一个/docs文件夹
- 不按照<username>.github.io 或 <orgname>.github.io这样的命名规则

为了发布你的页面，GitHub Pages将会读取你仓库下的所有文件，包括/docs下的CNAME文件.比如，当你通过GitHub Pages设置来编辑你自己的域名时，自定义域名将会写入/docs/CNAME
tips:在/docs下的文件被使用后，如果你移除master分支下的/docs文件夹，你的页面将不会build并且你将会收到缺少/docs文件夹的页面build错误信息。
> 
1. 在GitHub主页，导航栏选择你要设置的GitHub Pages页面的仓库
2. 在你的master分支的根目录创建一个/docs文件夹
3. 在你选择的仓库页面下，点击Settings
4. 在Settings页面，向下滑动选择master或gh-pages作为你的发布源
5. 点击Save

---

#个人、企业和项目页面

有两种基本的GitHub Pages类型：个人/企业页面和项目页面。这两种十分相似，不过他们之间有几个重大的差别。

在2016年6月15日之后创建并且使用了github.io作为域名的GitHub Pages页面将使用HTTPS传输协议.获取更多相关信息，请参考的[Securing your GitHub Pages site with HTTPS](xxx)

tips:GitHub Pages不建议用来存放敏感数据，如密码或信用卡号

记住，页面是将会一直公开访问即使他们的仓库是私有的。

| 站点类型 | 页面默认域名        |建立页面站点的源文件所在位置|
|--------------|---------------------|----------------------------|
| 个人页面 |username.github.io   |master                      |
| 企业页面 |orgname.github.io    |master                      |
| 个人项目 |username.github.io/projectname |master, gh-pages, or a /docs folder on master|
| 企业项目 |orgname.github.io/projectname |master, gh-pages, or a /docs folder on master|

学习更多GitHub Pages 页面重定向到个人域名的信息，请访问[custom domain redirects chart.](https://help.github.com/articles/custom-domain-redirects-for-github-pages-sites/)

## 个人和企业页面

个人和企业页面需要专用的仓库存放GitHub Pages文件。你需要用用户名命名此仓库


- 将仓库命名为<username>.github.io
- master分支下的内容将会被构建并发布到你的GitHub Pages 站点

你只能使用你自己的账户名作为个人或企业页面的仓库名。像joe/bob.github.io这样的仓库名将不会被build为一个个人页面。
当用户页面被构建后，可以通过 http(s)://<username>.github.io 地址来访问。

## 建立个人或企业页面
个人页面可以被任何已经验证过邮箱的账户所构建。他们瞳影可以使用deploy-keys 来自动化完成处理。

企业页面可以被任何push到仓库的成员且已经验证过邮箱的账户所建立。为了自动建立，你可以搭建一个机器用户作为企业的成员。Deploy-keys 不支持企业页面。

##项目页面
不像个人和企业页面，项目页面保存在项目的仓库里。个人账户和企业账户都可以创建项目页面。个人账户的项目页面URL 是 “http(s)://<username>.github.io/<projectname>”，企业的项目页面URL 是“http(s)://<orgname>.github.io/<projectname>”。这两种账户创建项目页面的步骤是一样的。
项目页面与个人/企业页面十分相似，只有几点不同：
- 你可以建立并发布来自master 或gh-pages 分支下的项目页面站点。你也可以发布在master 分支下/docs文件夹里的站点。获取更多信息，请看[Configuring a publishing source for GitHub Pages.](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/)
- 如果你没有使用自定义域名，项目页面站点会被运行在个人页面的子目录下:username.github.io/projectname
- 如果你的个人/企业站点使用了自定义域名，自定义的域名将会替代username.github.io 作为该账户项目页面站点的URL。使用了自定义域名的项目页面站点同时也支持通过username.github.io/projectname （个人用户）,orgname.github.io/projectname (企业用户)来访问。
- 自定义404页面只能应用自定义域名的站点，否则用户页面404会被使用。

警告：如果你的页面站点的URL包含了以破折号"-"为起止的用户名或企业名，或者包含了连续的破折号"--"，当Linux用户访问时会收到服务错误。为了避免此种情况，你的GitHub 用户名应该删除非字母表的字符。获取更改用户名指引请参考[Changing your GitHub username](https://help.github.com/articles/changing-your-github-username/)