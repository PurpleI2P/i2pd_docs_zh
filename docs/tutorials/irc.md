匿名聊天服务器
======================

## 连接到匿名 IRC 服务器

你可以通过使用 Socks 代理连接 I2P 中的 IRC 服务器。默认情况下，它监听在 ``127.0.0.1:4447``（详情参见[配置文档](../user-guide/configuration.md)）。将你的 IRC 客户端配置为使用该 Socks 代理，然后像连接其他服务器一样连接到 I2P 服务器。

或者，你也可以为特定服务器创建一个客户端 I2P 隧道。这样，i2pd 会在你的计算机上“绑定”IRC 服务器端口，你无需修改任何 IRC 客户端设置即可连接到服务器。

要连接位于 irc.ilita.i2p:6667 的 IRC 服务器，将以下内容添加到 ~/.i2pd/tunnels.conf：

    [IRC2]
    type = client
    address = 127.0.0.1
    port = 6669 
    destination = irc.ilita.i2p
    destinationport = 6667
    #keys = irc-client-key.dat

重启 i2pd，然后使用你的 IRC 客户端连接到 irc://127.0.0.1:6669。

## 运行匿名 IRC 服务器

1) 运行你的 IRC 服务器软件，并确认它使用的主机与端口（例如 127.0.0.1:5555）。

   对于小型私有 IRC 服务器，你可以使用 [miniircd](https://github.com/jrosdahl/miniircd)，对于大型公共网络，可以使用 [UnreadIRCd](https://www.unrealircd.org/)。

2) 配置 i2pd 以创建 IRC 服务器隧道。

   最简单的情况，如果你的服务器不支持 WebIRC，将以下内容加入 ~/.i2pd/tunnels.conf：

```
[anon-chatserver]
type = irc
host = 127.0.0.1     
port = 5555
keys = chatserver-key.dat
```

   就这些。

   或者，如果你的 IRC 服务器支持 WebIRC，例如 UnreadIRCd，在 UnrealIRCd 配置中加入：

```
webirc {
    mask 127.*.*.*;
    password your_password;
};
```

   还需将以下行：

    modes-on-connect "+ixw";

   修改为

    modes-on-connect "+iw";

   然后在 ~/.i2pd/tunnels.conf 中加入：

    [anon-chatserver]
    type = irc
    host = 127.0.0.1
    port = 5555
    keys = chatserver-key.dat
    webircpassword = your_password

3) 加固 UnrealIRCd

   默认情况下，如果你运行 I2Pd 服务，I2P 将使用 IP 127.0.0.1 在本地主机上连接到 IRCd。
   
   这有两个问题：
   
   第一，你将无法区分来自 I2P 的流量与其他本地主机流量。
   第二，所有 I2P 用户都将无法被封禁，因为 127.0.0.1 被排除在所有封禁之外，包括 glines。

   因此，我们可以伪造主机以将本地主机流量与 i2pd 流量分离。

   为此，我们将创建 UnrealIRCd 将要访问的目录并创建套接字文件：

```
    mkdir /etc/i2pd/unrealircd
    chown unrealircd:unrealircd /etc/i2pd/unrealircd
    chmod 750 /etc/i2pd/unrealircd
```
   注意：这里假设你的 IRCd 用户名为 unrealircd。如果不是，请在上面的 chown 命令中将 unrealircd:unrealircd 改为实际的用户与组。

   如果你在 Debian/Ubuntu 上并安装了 AppArmor（很可能是的！），请运行接下来的命令。如果不执行这些步骤，那么后续一切可能会莫名其妙地失败。

   仍以 root 身份运行：
```
    echo "/etc/i2pd/unrealircd/ip2d_ircd.socket rw," >>/etc/apparmor.d/local/system_i2pd
    apparmor_parser -r /etc/apparmor.d/system_i2pd
```

   配置 UnrealIRCd，将以下内容添加到你的 unrealircd.conf 文件中：
```
    listen {
    file "/etc/i2pd/unrealircd/i2pd_ircd.socket";
    mode 0777;
    spoof-ip 127.0.0.3;
    }
```

   并关闭封禁检查：
```
   except ban {
    mask { ip 127.0.0.3; }
    type { blacklist; connect-flood; maxperip; handshake-data-flood; }
    }
```
   
   我们将创建一个通信桥，将 TCP/IP 端口 5555 与位于 “/etc/i2pd/unrealircd/i2pd_ircd.socket” 的 UNIX 套接字连接起来。

   只需运行：
```
    socat TCP-LISTEN:5555,bind=localhost,reuseaddr,fork UNIX-CONNECT:/etc/i2pd/unrealircd/i2pd_ircd.socket &
```
   这样，当用户连接到 I2P 隧道客户端地址时，他们会被重定向到 127.0.0.1:5555，而该端口会桥接到 UnrealIrcd 创建的 Unix 套接字，从而以 127.0.0.3 的 IP 出现，并将其排除在封禁检查之外。

4) 重启 i2pd。

5) 查找你的匿名 IRC 服务器的 b32 目标地址。

   进入 web 控制台 -> [I2P 隧道页面](http://127.0.0.1:7070/?page=i2p_tunnels)。查看 Sever tunnels，你会在 anon-chatserver 旁看到类似 \<long random string\>.b32.i2p 的地址。

   客户端将使用该地址匿名连接到你的服务器。