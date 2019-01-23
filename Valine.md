### Valine -- 极简无后端评论系统

A fast, simple & powerful comment system. 一款基于Leancloud云引擎的极简风格评论系统。安装、使用和管理都非常简单。瞬间提升网站的社交功能和颜值！  

[[Valine Official Websit -- 官方网站]](https://valine.js.org/)  

**Features -- 特性**

- No server-side implementation  |  无后端实现
- Support for full [Markdown](https://aodabo.tech/blog/001536942700189b3b8ca62a0624a3d92f729ab6f54337f000) syntax  |  全面支持[[Markdown]](https://aodabo.tech/blog/001536942700189b3b8ca62a0624a3d92f729ab6f54337f000)语法
- Simple and lightweight (~15kB gzipped)  |  极简轻量
- Support Emoji  |  支持Emoji表情
- Support [Gravatar](https://aodabo.tech/blog/001536949899298bb9ae32357af4ba1bb88862591c21e5e000)  |  支持[[Gravatar]](https://aodabo.tech/blog/001536949899298bb9ae32357af4ba1bb88862591c21e5e000)通用头像



**Guide -- 教程**

请先[登录](https://leancloud.cn/dashboard/login.html#/signin)或[注册](https://leancloud.cn/dashboard/login.html#/signup) `LeanCloud`, 进入[控制台](https://leancloud.cn/dashboard/applist.html#/apps)后点击左下角[创建应用](https://leancloud.cn/dashboard/applist.html#/newapp)：

![img](https://ws1.sinaimg.cn/large/006qRazegy1fkwo2fpoetj30h40coaak.jpg)

应用创建好以后，进入刚刚创建的应用，选择左下角的`设置`>`应用Key`，然后就能看到你的`APP ID`和`APP Key`了：

![img](https://ws1.sinaimg.cn/large/006qRazegy1fkwo6w2b6uj30xe0etjt4.jpg)



然后在你的网页中加入以下HTML片段，修改初始化对象中的`appId`和`appKey`的值为上面刚刚获取到的值即可(其他可以默认)，打开网页看是不是评论系统已出现在你的页面了。

```html
<head>
    ...
    <script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
    <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
    ...
</head>
<body>
    ...
    <div id="vcomments"></div>
    <script>
        new Valine({
            el: '#vcomments',
            appId: '<API_ID>',
            appKey: '<API_Key>'
        })
    </script>
</body>
```

除了以上的默认代码，还可以根据情况添加或修改配置项:

```js
new Valine({
    el: '#vcomments' ,  //Valine 的初始化挂载器
    appId: '<APP_ID>',  //从LeanCloud的应用中得到的appId
    appKey: '<APP_KEY>',  //从LeanCloud的应用中得到的APP Key
    notify:false,   //评论回复邮件提醒
    verify:false,   //验证码服务
    avatar:'mm',   //Gravatar 头像,选择默认头像
    meta:['nick','mail','link'],  //评论者相关属性
    pageSize: 10,  //评论列表分页，每页条数
    lang:'zh-cn',  //自定义语言
    highlight: true,  //代码高亮
    placeholder: 'just go go'   //评论框占位提示符
});
```



如要进行评论数据管理，请自行登录`Leancloud`应用管理。`登录`>选择你创建的`应用`>`存储`>选择Class`Comment`，然后就可以编辑和管理了。

[![img](https://ws1.sinaimg.cn/large/006qRazegy1fibb4pbvv4j31820iqjw0.jpg)](https://ws1.sinaimg.cn/large/006qRazegy1fibb4pbvv4j31820iqjw0.jpg)  



**Advance Setting(Optional) -- 高级设置(可选)**

Valine Admin 是 [Valine 评论系统](https://panjunwen.com/diy-a-comment-system/)的扩展和增强，主要实现评论邮件通知、评论管理、垃圾评论过滤等功能。支持完全自定义的邮件通知模板，基于Akismet API实现准确的垃圾评论过滤。以下是如何使用Valine Admin的教程。

首先，在[Leancloud](https://leancloud.cn/dashboard/#/apps)云引擎设置界面，填写代码库并保存：<https://github.com/DesertsP/Valine-Admin.git>

![设置仓库](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-15-xia-wu-12-56-04.png)



*邮件通知*

在设置页面，设置环境变量以及 Web 二级域名。

![环境变量](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-15-xia-wu-3-40-48.png)

| 变量          | 示例                                                         | 说明                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SITE_NAME     | Aodabo                                                       | [必填]博客名称                                               |
| SITE_URL      | [https://aodabo.leanapp.cn](https://aodabo.tech.leanapp.cn/) | [必填]首页地址                                               |
| SMTP_SERVICE  | QQ                                                           | [新版支持]邮件服务提供商，支持 QQ、163、126、Gmail 以及 [更多](https://nodemailer.com/smtp/well-known/#supported-services) |
| SMTP_USER     | [xxxxxx@qq.com](mailto:xxxxxx@qq.com)                        | [必填]SMTP登录用户                                           |
| SMTP_PASS     | ccxxxxxxxxch                                                 | [必填]SMTP登录密码（QQ邮箱需要获取独立密码）                 |
| SENDER_NAME   | Aodabo                                                       | [必填]发件人                                                 |
| SENDER_EMAIL  | [xxxxxx@qq.com](mailto:xxxxxx@qq.com)                        | [必填]发件邮箱                                               |
| ADMIN_URL     | <https://xxx.leanapp.cn/>                                    | [建议]Web主机二级域名，用于自动唤醒                          |
| BLOGGER_EMAIL | [xxxxx@gmail.com](mailto:xxxxx@gmail.com)                    | [可选]博主通知收件地址，默认使用SENDER_EMAIL                 |

以上必填参数请务必正确设置。  



*评论后台管理*

二级域名用于评论后台管理，如[https://aodabo.leanapp.cn](https://aodabo.tech.leanapp.cn/) 。

![二级域名](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-15-xia-wu-1-06-41.png)

切换到部署标签页，分支使用master，点击部署即可

![一键部署](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-15-xia-wu-12-56-50.png)

第一次部署需要花点时间。

![部署过程](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-15-xia-wu-1-00-45.png)

评论管理。访问设置的二级域名，注册管理员登录信息

```
https://二级域名.leanapp.cn/sign-up
```

> 注：使用原版Valine如果遇到注册页面不显示直接跳转至登录页的情况，请手动删除_User表中的全部数据。

此后，可以通过`https://二级域名.leanapp.cn/`管理评论。  



*定时任务设置*

目前实现了两种云函数定时任务：(1)自动唤醒，定时访问Web APP二级域名防止云引擎休眠；(2)每天定时检查24小时内漏发的邮件通知。

进入云引擎-定时任务中，创建定时器，创建两个定时任务。

选择self-wake云函数，Cron表达式为`0 0/30 7-23 * * ?`，表示每天早6点到晚23点每隔30分钟访问云引擎，`ADMIN_URL`环境变量务必设置正确：

![唤醒云引擎](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-18-xia-wu-2-57-43.png)

选择resend-mails云函数，Cron表达式为`0 0 8 * * ?`，表示每天早8点检查过去24小时内漏发的通知邮件并补发：

![通知检查](https://cloud.panjunwen.com/2018/09/ping-mu-kuai-zhao-2018-09-18-xia-wu-2-57-53.png)

添加定时器后记得点击启动方可生效。

至此，Valine Admin 已经可以正常工作。



*垃圾评论检测*

> Akismet (Automattic Kismet)是应用广泛的一个垃圾留言过滤系统，其作者是大名鼎鼎的WordPress 创始人 Matt Mullenweg，Akismet也是WordPress默认安装的插件，其使用非常广泛，设计目标便是帮助博客网站来过滤留言Spam。有了Akismet之后，基本上不用担心垃圾留言的烦恼了。
> 启用Akismet后，当博客再收到留言会自动将其提交到Akismet并与Akismet上的黑名单进行比对，如果名列该黑名单中，则该条留言会被标记为垃圾评论且不会发布。

如果你用过 WordPress 你应该有 Akismet Key；如果还没有，你可以去 [AKISMET FOR DEVELOPERS 申请一个(现在貌似已不免费了)](https://akismet.com/development/)；如果你不需要反垃圾评论，Akismet Key 环境变量可以忽略。

为了实现较为精准的垃圾评论识别，采集的判据除了评论内容、邮件地址和网站地址外，还包括评论者的IP地址、浏览器信息等，但仅在云引擎后台使用这些数据，确保隐私和安全。

如果使用了本站最新的Valine和Valine Admin，并设置了Akismet Key，可以有效地拦截垃圾评论。被标为垃圾的评论可以在管理页面取消标注。

| 环境变量    | 示例         | 说明                               |
| ----------- | ------------ | ---------------------------------- |
| AKISMET_KEY | xxxxxxxxxxxx | [可选]Akismet Key 用于垃圾评论检测 |