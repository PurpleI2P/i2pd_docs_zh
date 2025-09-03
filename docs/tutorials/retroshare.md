使用 RetroShare 搭配 i2pd
==========================

RetroShare 是一套功能丰富的 P2P 应用程序，能够在匿名的 I2P 网络之上工作。
引用 [维基百科](https://en.wikipedia.org/wiki/RetroShare)：

> RetroShare 是基于 GNU Privacy Guard（GPG）构建的好友对好友（friend-to-friend）网络上的用于加密文件共享、无服务器电子邮件、即时消息、在线聊天和 BBS 的开源软件。

为了让 RetroShare 与 i2pd 协同工作，你需要在 [tunnels.conf](../user-guide/tunnels.md) 中创建一个新的服务器隧道，如下所示： 

    [retroshare]  
    type=server  
    host=127.0.0.1  
    port=10123            # 选择你喜欢的任意随机端口 
    keys=retroshare.dat  

确保已启用 Socks 代理，默认情况下它监听在 ``127.0.0.1:4447``。如果不是，请查看 [i2pd.conf 文档](../user-guide/configuration.md)。

使用新配置重启 i2pd。 

查找你的 RetroShare 节点的 .b32.i2p 地址。
进入 web 控制台 -> [I2P 隧道页面](http://127.0.0.1:7070/?page=i2p_tunnels)。查找 Server 隧道，在 “retroshare” 旁边你会看到类似于 \<long random string\>.b32.i2p 的地址。

现在一切准备就绪，你可以运行 RetroShare 本体并创建一个新资料。
将其标记为 **hidden**，并输入你的 **.b32.i2p address** 和你在 tunnels.conf 中指定的 **port**。

更多有关 RetroShare 的信息请参见他们的[网站](http://retroshare.cc)。