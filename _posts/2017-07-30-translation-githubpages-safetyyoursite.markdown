---
layout:     post
title:      "[Translation]使用HTTPS让你的GitHub Pages站点更安全"
date:       2017-07-30 19:50:00
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---


你可以强制HTTPS为访问你的站点增加一个安全层，如果你的站点使用了github.io 域名的话。

HTTPS 启用时，你GitHub Pages上的HTTP请求将会自动定向到HTTPS。

在2016年6月15日之后创建并且使用了*github.io* 域名的GitHub Pages 页面站点会强制使用HTTPS 增强。如果是在2016年6月15日之前创建的站点，你可以手动启用HTTPS 增强。HTTPS 增强不适用于使用自定义域名的站点。
> GitHub Pages 不建议用来存放敏感内容，如密码或信用卡号
1. 在GitHub页面，导航到仓库的主页面
2. 在你仓库名下，点击Settings按钮。
3. 在仓库的GitHub Pages中选择*Enforce HTTPS*

## 解决混合内容的问题

如果你为你的站点启动了HTTPS，同时你的站点依然使用HTTP 调用图片、CSS或JavaScript，那么你的站点就是运行于混合的内容。这种情况下，你在加载assets 时可能会出现问题。运行于混合内容点也会使你的站点变得不安全。
为了一处你站点内的混合内容，提升站点的安全性，解决加载混合内容时的问题，你可以编辑你站点的HTML 文件。将http://改为https://，这样你所有的配置文件都会运行在HTTPS下。

对于使用了Jekyll 的站点，你的HTML 文件可能存储在_layouts文件夹下。一般CSS在HTML文件的<head>节点下.JavaScript文件通常在<head>节点下或者在</body>节点前。图片通常在<body>标签里。

Tip:如果你找不到你的assets文件，可以尝试在你站点或GitHub 代码中搜索http。
### HTML文件中assets可能存放的位置

|Asset type| HTTP|HTTPS|
|---|---|---|
|CSS|< link rel="stylesheet" href="http://example.com/css/main.css">|< link rel="stylesheet" href="https://example.com/css/main.css">|
|JavaScript|< script type="text/javascript" src="http://example.com/js/main.js">< /script> |< script type="text/javascript" src="https://example.com/js/main.js">< /script>|
|Image|< A HREF="http://www.somesite.com">< IMG SRC="http://www.example.com/logo.jpg" alt="Logo">< /a>|< A HREF="https://www.somesite.com">< IMG SRC="https://www.example.com/logo.jpg" alt="Logo">< /a>|

