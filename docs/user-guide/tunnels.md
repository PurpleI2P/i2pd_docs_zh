I2P 隧道配置
========================

概述
--------

`tunnels.conf` 用于支持多个 I2P 隧道。配置文件在类 Unix 系统上必须位于 `~/.i2pd`（分用户）或 `/var/lib/i2pd`（全系统），在 Windows 上必须位于 `%APPDATA%/i2pd`（分用户）。

该文件使用 .ini 文件格式。它由多个具有唯一名称的配置节（section）组成。

隧道类型
------------

配置节的类型由参数 type 指定。

可用的隧道类型：

类型          | 说明
------------- | --------------------------------------
client        | 到远程 I2P 目标（destination）的客户端隧道（TCP）
server        | 在 I2P 网络中设置任意 TCP 服务的通用服务器隧道
http          | 在 I2P 中设置网站的 HTTP 服务器隧道
irc           | 在 I2P 中设置 IRC 服务器的 IRC 服务器隧道
udpclient     | 将本地 UDP 端点转发到远程 I2P 目标
udpserver     | 将来自 N 个 I2P 目标的流量转发到本地 UDP 端点
socks         | 用于 I2P 的自定义 Socks 代理服务
httpproxy     | 用于 I2P 的自定义 HTTP 代理服务

签名类型
------------

在隧道配置中使用参数 `signaturetype = <编号>`。

可用的签名类型：

类型                                 | 编号 | 说明
------------------------------------ | ---- | -----------
DSA-SHA1                             | 0    | 已弃用
ECDSA-P256                           | 1    | 无（正在使用）
ECDSA-P384                           | 2    | 无（正在使用）
ECDSA-P521                           | 3    | 无（正在使用）
RSA-2048-SHA256                      | 4    | 已弃用
RSA-3072-SHA384                      | 5    | 已弃用
RSA-4096-SHA512                      | 6    | 已弃用
ED25519-SHA512                       | 7    | 默认
*ED25519ph-SHA512*                   | 8    | 未实现
GOSTR3410-A-GOSTR3411-256            | 9    | 与 Java 路由器不兼容
GOSTR3410-TC26-A-GOSTR3411-512       | 10   | 与 Java 路由器不兼容
RED25519-SHA512                      | 11   | 用于密钥盲化（加密 LeaseSet）
ML-DSA-44                            | 12   | 后量子。需要 OpenSSL 版本号大于或等于 3.5.0

LeaseSet（租约集）
------------

可用的 LeaseSet 类型（在隧道配置中参数 `i2cp.leaseSetType = <编号>`）：

类型        | 编号 | 说明
----------- | ---- | -----------
OLD         | 1    | 已弃用
STANDARD    | 3    | 默认
ENCRYPTED   | 5    | 加密 LeaseSet。对 floodfill 隐藏信息
META        | 7    | 未实现

0、2、4、6 类型保留给路由器（RouterInfo 类型）。

可用的 LeaseSet 加密类型（在隧道配置中参数 `i2cp.leaseSetEncType = <编号>`）：

类型                                 | 编号 | 说明
------------------------------------ | ---- | -----------
ELGAMAL                              | 0    | 遗留
ECIES_P256_SHA256_AES256CBC          | 1    | 与 Java 路由器不兼容
*ECIES_P384_SHA384_AES256CBC*        | 2    | 未实现
*ECIES_P521_SHA512_AES256CBC*        | 3    | 未实现
ECIES_X25519_AEAD                    | 4    | **默认**
ECIES_MLKEM512_X25519_AEAD           | 5    | 后量子。需要 OpenSSL 版本号大于或等于 3.5.0
ECIES_MLKEM768_X25519_AEAD           | 6    | **默认**。后量子。需要 OpenSSL 版本号大于或等于 3.5.0
ECIES_MLKEM1024_X25519_AEAD          | 7    | 后量子。需要 OpenSSL 版本号大于或等于 3.5.0

客户端隧道
--------------

一句话简介：我们可以以客户端身份连接到某人

每个客户端隧道包含一些必需参数，还有一些可选参数。

以下是一个客户端隧道的示例：

```ini
[irc-out]
type = client
address = 127.0.0.1
port = 6668
destination = irc.ilita.i2p
keys = irc.dat
```

如果 keys 为空，则每次重启都会创建临时密钥。如果未找到密钥文件，将创建新密钥并存储到指定文件中。
如果 keys 以 transient 开头，将创建新密钥，但不会存储到文件中。

如果密钥文件包含相同的身份，客户端隧道可以共享同一个本地目标。

可选参数：

选项              | 说明
--------------------|--------------------
address             | 隧道绑定的本地接口，“127.0.0.1”表示仅允许来自本地主机的连接，“0.0.0.0”表示允许来自任何地方的连接。（默认：127.0.0.1）
port                | 客户端隧道的端口。
signaturetype       | 新密钥的签名类型。RSA 签名（4、5、6）不被允许，将会被改为 7。（默认：7）
cryptotype          | 新密钥的加密类型。实验性。应始终为 0
destinationport     | 连接到目标上的特定端口。默认 0（指向目标服务器侧的第一个隧道）
keepaliveinterval   | 在此间隔后向目标发送 ping，以秒为单位。（默认：0 - 不发送 ping）
keys                | 目标的密钥。当多个隧道相同，则每个隧道都会使用相同的目标。

因此，基于上述示例，如果你在本地主机上连接到 127.0.0.1:6668，i2pd 会将该连接通过隧道转发到 irc.ilita.i2p。

服务器/通用隧道
----------------------

一句话简介：我们在网络中向他人提供某项服务

以下是一个服务器隧道的示例：

```ini
[smtp-in]
type = server
host = 127.0.0.1
port = 25
keys = smtp-in.dat
```

如果 keys 为空，则每次重启都会创建临时密钥。如果未找到 keys 文件，将创建新密钥并存储到指定文件中。

将从 keys 文件中加载目标地址，并发布该地址的 LeaseSet。
服务器隧道必须使用其自己的目标，例如主机 127.0.0.1 和端口 80。

port 是（非 I2P）IP 主机上 TCP 监听端口，本地目标监听后会连接到该端口。

此隧道类型应适用于除 HTTP 之外的任何协议，即使是带 SSL 加密的 HTTP（HTTPS）也用这个。

可选参数：

选项              | 说明
--------------------|--------------------
host                | 服务器的 IP 地址（i2pd 会将来自 I2P 的数据发送到此地址）
port                | 服务器隧道的端口。
inport              |（非 TCP 非 UDP）I2P 本地目标监听的端口；一个无符号 16 位整数。服务器隧道在本地目标上监听的端口（默认：与 port 相同）
accesslist          | 允许连接的以逗号分隔的 b32 地址列表（不带 .b32.i2p）。默认允许所有人
gzip                | 若设为 false 则关闭内部压缩。（默认：false）
signaturetype       | 新密钥的签名类型。（默认：7）
cryptotype          | 新密钥的加密类型。实验性。应始终为 0
enableuniquelocal   | 若为 true，连接方地址将显示为 127.x.x.x，其中 x.x.x 为传入连接对端身份哈希的前 3 字节。（默认：IPv4 为 true，IPv6 为 false）
address             | 隧道连接到 host 时所使用的本地接口 IP 地址。通常不使用
keys                | 目标的密钥。当多个隧道相同，则每个隧道都会使用相同的目标。

服务器/HTTP 隧道
-------------------

http 隧道的配置与常规服务器隧道相同，但必须将 'Host:' 字段设置为配置中提供的地址。在必要时 i2pd 也会对其进行解析。

以下是一个 http 隧道的示例：

```ini
[http-in]
type = http
host = 127.0.0.1  
port = 80
keys = our-website.dat
```

可选参数：

选项              | 说明
--------------------|--------------------
hostoverride        | 在 'Host:' 头中发送的值，默认：与 host 参数相同
ssl                 | 对上游服务器使用 SSL 连接。可使用 `hostoverride` 参数设置 SNI 域。默认：false（自 2.44.0 起）

服务器/IRC 隧道
-------------------

IRC 隧道应通过 WEBIRC 连接到 IRC 服务器。它将 IP 地址（通常是 127.0.0.1）替换为用户的 .b32 I2P 地址。

可选参数：

选项              | 说明
--------------------|--------------------
webircpassword      | 与 WEBIRC 命令一起发送的密码

UDP 隧道
-----------

UDP 隧道有两种类型：`udpclient` 和 `udpserver`

`udpclient` 将 1 个本地 UDP 端点转发到 1 个远程 I2P 目标

```ini
[openvpn-client-simple]
type = udpclient
destination = something.b32.i2p
port = 1194
```

选项              | 说明
--------------------|--------------------
destination         | udpserver 隧道的 I2P 目标，必需参数
address             | 绑定本地 UDP 端点的 IP 地址（默认：`127.0.0.1`）
port                | 绑定本地 UDP 端点的端口，必需参数
gzip                | 若设为 false 则关闭内部压缩。（默认：false）
keys                | 目标的密钥。当多个隧道相同，则每个隧道都会使用相同的目标。

`udpserver` 将来自 N 个 I2P 目标的流量转发到 1 个本地 UDP 端点

```ini
[openvpn-simple-server]
type = udpserver
keys = openvpn.dat
host = 127.0.0.1
port = 1194
```

选项              | 说明
--------------------|--------------------
address             | 用于本地 UDP 端点的 IP 地址（默认：`127.0.0.1`）
host                | 要转发流量到的 IP 地址，必需参数
port                | 要转发流量使用的 UDP 端口，必需参数
gzip                | 若设为 false 则关闭内部压缩。（默认：false）
keys                | 目标的密钥。当多个隧道相同，则每个隧道都会使用相同的目标。

Socks 代理
-----------

可以在 `tunnels.conf` 中定义 SOCKS 代理接口。

以下是一个 Socks 代理示例：

```ini
[alt-socks]
type = socks
address = 127.0.0.1
port = 14447
keys = socks-keys.dat 
```

选项              | 说明
--------------------|--------------------
address             | Socks 代理绑定的本地地址（默认：`127.0.0.1`）
port                | Socks 代理绑定的 TCP 端口

I2CP 参数
---------------

这些 I2CP 参数对所有隧道类型通用，用于指定本地目标的设置。

Parameter                     | Description
------------------------------|--------------------
inbound.length                | 入站隧道的跳数。默认 3，最大 8；数值越低越快，但去匿名风险越高
outbound.length               | 出站隧道的跳数。默认 3，最大 8；数值越低越快，但去匿名风险越高
inbound.quantity              | 入站隧道的数量。默认 5，最大 16
outbound.quantity             | 出站隧道的数量。默认 5，最大 16
inbound.lengthVariance        | 入站隧道随机增减的跳数，范围 -3 到 3。默认 0（自 2.42.0 起）
outbound.lengthVariance       | 出站隧道随机增减的跳数，范围 -3 到 3。默认 0（自 2.42.0 起）
crypto.tagsToSend             | 要发送的 ElGamal/AES 标签数量。默认 40；值过低可能导致隧道构建问题
crypto.ratchet.inboundTags    | 每个标签集可生成的入站标签最大数量（默认：800）
explicitPeers                 | 以逗号分隔的对等方 b64 地址列表（默认：未设置）
i2p.streaming.initialAckDelay | 在发送 Ack 之前等待的毫秒数。（默认：200）
i2p.streaming.answerPings     | 启用发送 pong。1 或 true，0 或 false（默认：1）
i2p.streaming.maxOutboundSpeed| 流的最大出站速度（字节/秒）。（默认：1730000000）
i2p.streaming.maxInboundSpeed | 流的最大入站速度（字节/秒）。（默认：1730000000）
i2p.streaming.profile         | 带宽使用配置。1 - 批量（高），2 - 交互（低）。（默认：1）
i2p.streaming.maxConcurrentStreams  | 同时存在的流总数量上限。（默认：2048）
i2p.streaming.maxWindowSize   | 流的最大窗口大小（默认：512）
i2p.streaming.dontSign        | 在需要时不包含签名。仅当对端不验证签名时才启用（默认：false）
i2cp.leaseSetType             | 要发送的 LeaseSet 类型。1、3 或 5。（默认：3）
i2cp.leaseSetEncType          | 用于 LeaseSet 类型 3 或 5 的加密类型（逗号分隔）。（默认：0,4）
i2cp.leaseSetPrivKey          | 加密 LeaseSet 的解密密钥，base64。PSK（预共享密钥） 或 DH 私钥
i2cp.leaseSetAuthType         | 加密 LeaseSet 的认证类型。0 - 无认证（默认），1 - DH，2 - PSK
i2cp.leaseSetClient.dh.nnn    | 客户端名:客户端的 DH 公钥（base64），用于认证类型 1，nnn 为整数
i2cp.leaseSetClient.psk.nnn   | 客户端名:客户端的 PSK（base64），用于认证类型 2，nnn 为整数
i2cp.dontPublishLeaseSet      | 若设为 true 则不发布 LeaseSet。（默认：false）

其他示例
--------------

```ini
# 出站隧道示例，连接到远程服务
# 必需参数：
# * type -- 始终为 "client"
# * port -- 本地监听端口
# * destination -- I2P 主机名
# 可选参数（可省略）
# * keys -- 我们的身份；若未设置，每次启动都会生成，
#     若已设置且文件缺失，将生成密钥并写入该文件
# * address -- 绑定的本地接口
# * signaturetype -- 新目标的签名类型。0（DSA/SHA1）、1（EcDSA/SHA256）或 7（EdDSA/SHA512）
[IRC]
type = client
address = 127.0.0.1
port = 6668
destination = irc.ilita.i2p
keys = irc-keys.dat
#
# 入站隧道示例，用于本地服务
# 必需参数：
# * type -- "server" 或 "http"
# * host -- 我们服务的 IP 地址
# * port -- 我们服务的端口
# * keys -- I2P 中该地址的 LeaseSet 文件
# 可选参数（可省略）
# * inport -- 可选，I2P 服务端口；若未设置，与 'port' 相同
# * accesslist -- 允许连接的 I2P 地址的逗号分隔列表，
#    每个地址为 b32，且不含 '.b32.i2p' 部分
[LOCALSITE]
type = http
host = 127.0.0.1
port = 80
keys = site-keys.dat
#
[IRC-SERVER]
type = server
host = 127.0.0.1
port = 6667
keys = irc.dat
```