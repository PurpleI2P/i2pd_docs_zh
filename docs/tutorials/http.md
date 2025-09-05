匿名网站
==================

## 浏览匿名网站

要在隐形互联网中浏览匿名网站，请将你的网页浏览器配置为使用 HTTP 代理 127.0.0.1:4444（i2pd 默认提供）。

在 Firefox 中：Settings -> General -> Network 标签 -> Configure Proxy Access to the Internet -> 选择 Manual proxy configuration，输入 HTTP 代理 127.0.0.1，端口 4444

![img](media/http-1.png)

在 Chromium 中：使用参数运行可执行文件

    chromium --proxy-server="http://127.0.0.1:4444"

注意，如果你也希望保持匿名，你需要对浏览器进行隐私强化设置。请自行研究，[可以从这里开始](http://www.howtogeek.com/102032/how-to-optimize-mozilla-firefox-for-maximum-privacy/)。

隐形互联网网站的大列表可在 [identiguy.i2p](http://identiguy.i2p) 找到。

## 托管匿名网站

如果你希望在隐形互联网中运行自己的网站，请按以下步骤操作：

1) 运行你的 Web 服务器，并找出它使用的主机:端口（例如 127.0.0.1:8080）。

2) 配置 i2pd 创建 HTTP 服务器隧道。在你的 ~/.i2pd/tunnels.conf 文件中加入：

    [anon-website]
    type = http
    host = 127.0.0.1
    port = 8080
    keys = anon-website.dat

3) 重启 i2pd 或发送 SIGHUP 信号。

4) 查找你的网站的 b32 目的地。

   前往 web 控制台 -> [I2P 隧道页面](http://127.0.0.1:7070/?page=i2p_tunnels)。查找 Server tunnels，你会在 anon-website 旁边看到类似 \<long random string\>.b32.i2p 的地址。

   现在通过访问该地址，你的网站已在隐形互联网中可用。

5)（可选）在 [reg.i2p](http://reg.i2p) 上注册一个简短且易记的 .i2p 域名。