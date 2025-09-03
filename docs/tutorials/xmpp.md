去中心化即时通讯
===============================

> 可扩展消息与状态协议（XMPP）是一种用于实时通信的开放 XML 技术，支持包括即时消息、在线状态与协作在内的广泛应用。

本教程将向你展示如何在 I2P 网络中运行你自己的 [XMPP](https://xmpp.org/about/) 服务器。该服务器可用于去中心化即时通讯，并可基于 XMPP 协议构建其他应用。

## 创建服务器 I2P 隧道

在 `tunnels.conf` 文件中写入以下内容：

    [prosody-s2s]
    type=server
    host=127.0.0.1
    port=5269
    inport=5269
    keys=prosody.dat

    [prosody-c2s]
    type=server
    host=127.0.0.1
    port=5222
    inport=5222
    keys=prosody.dat

如果你仅打算在本地主机上使用即时通讯，则可以省略 prosody-c2s 部分。重启 i2pd 以应用新设置，并在网页控制台 http://127.0.0.1:7070/ 的 “I2P tunnels” 页面查看新的 I2P 地址：

![img](media/xmpp-2.png)

保存这个 xxx.b32.i2p 地址，它将作为你的 XMPP 服务器的域名。

## 安装与配置 XMPP 服务器

我们将使用 [prosody](https://prosody.im/) XMPP 服务器，它最为轻量，并且已有可直接使用的 I2P 模块。安装说明参见[官方文档](https://prosody.im/download/start)，在 Debian/Ubuntu 上直接运行 `apt install prosody` 即可。

`mod_darknet` 模块需要 Lua 的 bit32 库。如果你的 Lua 版本低于 5.2（很可能如此），运行 `apt install lua-bit32`。

安装 [mod_darknet](https://github.com/majestrate/mod_darknet) 模块。将[这个文件](https://raw.githubusercontent.com/majestrate/mod_darknet/master/mod_darknet.lua)下载到 prosody 模块目录，通常为 `/usr/lib/prosody/modules`。

编辑配置文件 `/etc/prosody/prosody.cfg.lua`。将 xxx.b32.i2p 替换为你的地址：

    interfaces = { "127.0.0.1" };
    admins = { "admin@xxx.b32.i2p" };
    modules_enabled = {
        "roster"; "saslauth"; "tls"; "dialback"; "disco"; "posix"; "private"; "vcard"; "register"; "admin_adhoc"; "darknet"; 
    };
    modules_disabled = {};
    allow_registration = false;
    darknet_only = true;
    c2s_require_encryption = true;
    s2s_secure_auth = false;
    authentication = "internal_plain";

    -- On Debian/Ubuntu
    daemonize = true;
    pidfile = "/var/run/prosody/prosody.pid";
    log = {
        error = "/var/log/prosody/prosody.err";
        "*syslog";
    }
    certificates = "certs";

    VirtualHost "xxx.b32.i2p";
    ssl = {
        key = "/etc/prosody/certs/xxx.b32.i2p.key";
        certificate = "/etc/prosody/certs/xxx.b32.i2p.crt";
    }
 
最后一步是生成证书。运行以下命令：

    openssl genrsa -out /etc/prosody/certs/xxx.b32.i2p.key 2048
    openssl req -new -x509 -key /etc/prosody/certs/xxx.b32.i2p.key -out /etc/prosody/certs/xxx.b32.i2p.crt -days 3650
    chown root:prosody /etc/prosody/certs/*.b32.i2p.{key,crt}
    chmod 640 /etc/prosody/certs/*.b32.i2p.{key,crt}

重启 prosody 以应用新设置。

## 创建账户与连接客户端

要添加管理员账户，在终端运行：

    sudo prosodyctl adduser admin@xxx.b32.i2p

现在配置你的 XMPP 客户端（例如 [Pidgin](https://pidgin.im)）。

![img](media/xmpp-3.png)

如果连接到本地主机，请指定自定义服务器地址 127.0.0.1，端口 5222。

![img](media/xmpp-4.png)

如果通过 I2P 远程连接到服务器，请指定 Socks5 代理服务器 127.0.0.1:4447。

![img](media/xmpp-5.png)

如果一切配置正确，你将能够把 I2P 联邦的其他用户添加到联系人并与他们聊天。

要测试你的设置，将 `hello@xmppoeiqpbeelicgkoim3ibegjonbqwh7vv7d6nsju73tjjmujpq.b32.i2p` 添加到联系人并发送 “hello”。

##（高级）明网到 I2P 联邦

你也可以将现有的“明网”XMPP 服务器配置为加入 I2P 联邦，与暗网用户进行聊天。为此，按上述方法创建一个隧道。每个希望与你的服务器通信的用户都需要在其 prosody 配置文件中添加自定义映射。以下是我的服务器与 `i2p.rocks` 通信的配置：

    darknet_map = {
        ["i2p.rocks"] = "ynkz7ebfkllljitiodcq52pa7fgqziomz4wa7tv4qiqldghpx4uq.b32.i2p";
        ["muc.i2p.rocks"] = "ynkz7ebfkllljitiodcq52pa7fgqziomz4wa7tv4qiqldghpx4uq.b32.i2p";
    }
 
如有任何问题，你可以在我们的 XMPP 会议室 `federation@muc.i2p.rocks` 提问。聊得愉快！