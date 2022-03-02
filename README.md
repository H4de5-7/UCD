# 校园查分助手-微信小程序

## 背景

本科时我们的成绩系统距离比较远，网络延迟很严重，而且查分的时候需要跳转好多个页面，非常麻烦。出分的时候本来就够紧张的了。。还得看页面转圈圈。所以我就趁着那学期还没出分的时候写了个微信小程序来帮助大家更快、更友好地查分。

由于外方对隐私（毕竟是类似于爬虫的过程）比较看重，这里我就不放代码了，主要是总结一下思路以及遇到的问题。

## 准备工作

1、服务器：

最开始用了个小厂商的服务器，但是发现速度好慢啊。。后来果断换了腾讯云的服务器（也不算贵其实，轻量服务器24元/月）。

服务器起到了个加速的作用，查询成绩时的多次响应都是在服务器上先处理，这样比起之前的通信要快了很多，最后服务器只需要将查询成绩的结果传回前端即可。

即原本：前端<=200ms=>学校的服务器（假设需要10次交互）

现在为：前端<=150ms=>服务器<=50ms=>学校的服务器（第一段只需要两次交互，即发送查询成绩请求和返回查询结果，中间的八次交互均在第二段）

因此总查询速度快了很多。

因此服务器只是起到了中转的作用，并且考虑到成绩与账号密码是大家的隐私，我就没有用数据库。

2、微信小程序的注册及使用
我想吐槽的是大家要区分好微信的各种平台，微信公众平台[https://mp.weixin.qq.com/](https://mp.weixin.qq.com/)才是小程序需要的，然后需要下载微信开发者工具。

微信小程序的前端整体和html+css+js很像，感觉就是修改了一部分然后改了个名。

微信小程序用wx.request来发送请求，这里有两个需要注意的点：

（1）微信小程序首先将请求发送到微信自己的服务器上，然后微信的服务器会发送请求到我们自己的服务器，这个过程避免了跨域的问题。

（2）微信小程序要求https，即后端服务器的域名需要是经过备案的，因此引出了第三个需要准备的东西。

3、备案过的域名

一般备案需要30-40天，不过正好我之前备案过一个域名，就拿来直接用了。

## 思路

前端有三个界面：

1、登录界面

2、个人主页

3、成绩查询界面

前端第一版的界面太丑了，后来阿隋帮我改了一版，就好看多了。

整体就是一个模拟登陆的过程，通过一步步抓包找到关键的登陆、查询成绩的信息，然后发送请求来模拟登录。因为服务器配置的是LAMP，因此用的是PHP的curl发送请求。

后端分为两个部分：

1、login.php：处理登录。原本的登陆逻辑其实很简单，但是后来变成了SSO，加了一堆的跳转。就只能耐心地一步步找接口，提交表格的时候还需要把CSRF token带上，最后返回给前端登陆验证的结果。

2、results.php：用户登陆成功的半小时内会把系统返回的PHPSESSID留存在本地，半小时内再次查询的话不用登录。在网页上只能查询一年的成绩，为了让同学更清晰地查看成绩，我一次性地请求了全部的成绩返回给前端。
</br>

## 总结
前端加后端陆陆续续写了五六天吧。思路其实不难，快的话一两天就能写完，但是当时是第一次接触微信小程序开发，并且还要找网站接口，就做得比较久了。

本来是想做着玩的，结果反响还可以，最高一天有300+人，1500+页面浏览量（三个页面加在一起）。

![image](https://user-images.githubusercontent.com/48757788/156334346-5b2f7734-ee3d-4906-be7c-4699ed7ee908.png)





