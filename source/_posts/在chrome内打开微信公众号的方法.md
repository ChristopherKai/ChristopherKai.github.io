---
title: 在chrome内打开微信公众号的方法
tags:
  - Note
  - web
  - wechat
---


问题：“请在微信客户端打开链接”。
![“请在微信客户端打开链接”][1]

不要问为啥把好好能在微信打开的网页放在chrome里面打开，因为你能debug卡断点，看代码啊！

首先，需要明白，打开一个第三方网页的时候到底发生了什么。当你在微信内打开一个公众号网页，其会访问公众号的第三方网站如`https://appsth.h5.xiaoeknow.com/content_page/example`，如果公众号检测到请求的User-Agent,Cookie,Referer不对，则会返回HTTP 302，将浏览器重定向到响应头部的Location字段。所以，要避免这一重定向，就将要发送的HTTP头部使用chrome插件修改。

![windows微信客户端访问公众号的http请求头部][2]

安装chrome插件：[Modify Headers for Google Chrome][3]

修改浏览器http头部：
最重要的User-Agent：Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36 MicroMessenger/6.5.2.501 NetType/WIFI WindowsWechat QBCore/3.43.691.400 QQBrowser/9.0.2524.400
次重要的Cookie：按具体网页而定，经过抓包分析(推荐[charles][4])
![修改http头部][5]


  [1]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1526563881042.jpg
  [2]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1526563736658.jpg
  [3]: https://chrome.google.com/webstore/detail/modify-headers-for-google/innpjfdalfhpcoinfnehdnbkglpmogdi?hl=en-US
  [4]: http://blog.devtang.com/2015/11/14/charles-introduction/
  [5]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1526565326711.jpg