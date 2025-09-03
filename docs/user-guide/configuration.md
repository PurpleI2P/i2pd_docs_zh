i2pd 配置
==================

i2pd 可以通过命令行参数或配置文件进行配置。
修改 `i2pd.conf` 只是向 i2pd 在启动时传递命令行参数的一种方式；
例如，以参数 `--port=10123` 运行 i2pd，与在配置文件中设置选项 `port = 10123` 的效果相同。

有两个独立的配置文件：`i2pd.conf` 和 `tunnels.conf`。`i2pd.conf` 是主配置文件，你在其中配置所有选项。`tunnels.conf` 是隧道配置文件，你在其中配置 I2P 隐藏服务和客户端隧道。`tunnels.conf` 的选项文档在[此处](tunnels.md)。

`i2pd.conf` 接受类 INI 的语法，例如：<键> = <值>。
注释使用 “#”，而不是你可能预期的 “;”。参见 [boost 票据](https://svn.boost.org/trac/boost/ticket/808)
所有命令行参数都可以作为键，但对于包含点号 (.) 的参数，有特殊语法。

例如：

i2pd.conf:

    # 注释
    log = true # 使用标准输出（默认）
    ipv6 = true
    # 针对特定模块的设置
    [httpproxy]
    port = 4444
    # ^^ 这在命令行中将会是 --httproxy.port=
    # 另一个注释
    [sam]
    enabled = true

同时也建议查看 ``contrib/i2pd.conf``，其中包含带注释的配置和所有选项的示例。

命令行中指定的选项优先于配置文件中的选项。
如果你正在升级非常老的路由器（< 2.3.0），另请参见[这个](config_opts_after_2.3.0.md)页面。

可用选项
-----------------

运行 `./i2pd --help` 显示内置帮助信息（选项的默认值将以大括号显示）

### 通用选项

Option                                 | Description
-------------------------------------- | --------------------------------------
conf                                   | 配置文件（默认：~/.i2pd/i2pd.conf 或 /var/lib/i2pd/i2pd.conf）。如果指定的配置文件不存在，此参数将被静默忽略。
tunconf                                | 隧道配置文件（默认：~/.i2pd/tunnels.conf 或 /var/lib/i2pd/tunnels.conf）
pidfile                                | 写入 pidfile 的位置（默认：i2pd.pid，Windows 上不使用）
log                                    | 日志目标：stdout、file、syslog（若未设置或无效则为 stdout）（若作为守护进程运行，在某些情况下 stdout/未指定 会被替换为 file）
logfile                                | 日志文件路径（默认 - 自动检测）
loglevel                               | 记录此级别及以上的日志消息（debug、info、warn、error、none；默认 - warn）
logclftime                             | 将完整的 CLF 格式日期和时间写入日志（默认：false（仅写入时间））
datadir                                | i2pd 数据存储路径（配置、密钥、netdb、对等体档案等）。只能作为参数传递。
host                                   | 用于入站连接的路由器外部 IP（默认：若启用 SSU2 则自动）
port                                   | 用于入站连接的监听端口（默认：自动（随机））
daemon                                 | 路由器启动后转入后台（默认：true）
service                                | 路由器将使用系统目录，如（在 unix 上）'/var/lib/i2pd' 或（在 Windows 上）'C:\ProgramData\i2pd'。在 MacOS 和 Android 上忽略（默认：false）
ifname                                 | 要绑定的网络接口
ifname4                                | 绑定用于 IPv4 的网络接口
ifname6                                | 绑定用于 IPv6 的网络接口
address4                               | 绑定用于 IPv4 的本地地址
address6                               | 绑定用于公网 IPv6 的本地地址
nat                                    | 若为 true，假定我们处于 NAT 之后（默认：true）
ipv4                                   | 启用通过 IPv4 的通信（默认：true）
ipv6                                   | 启用通过公网 IPv6 的通信（默认：false）
notransit                              | 路由器将不接受中转隧道，完全禁用中转流量。将发布 G 路由能力标志（默认：false）
floodfill                              | 路由器将作为 floodfill（默认：false）
bandwidth                              | 带宽限制：整数（每秒千字节），或字母：L（32）、O（256）、P（2048）、X（无限），或带单位的整数（b、kb、mb、gb），例如，100mb = 1000000 kb/s
share                                  | 用于中转的带宽限制最大百分比。0-100（默认：100）
family                                 | 路由器所属的家族名称
netid                                  | 路由器所属的网络 ID。主 I2P 为 2。

#### 说明

`datadir` 和 `service` 选项仅可作为 i2pd 的启动参数使用，在 `i2pd.conf` 中设置它们无效。

### NTCP2

Option                                 | Description
-------------------------------------- | --------------------------------------
ntcp2.enabled                          | 启用 NTCP2（默认：true）
ntcp2.published                        | 启用入站 NTCP2 连接（默认：true）
ntcp2.port                             | 监听入站 NTCP2 连接的端口（默认：自动 - 来自通用部分的端口）
ntcp2.addressv6                        | 用于入站连接的外部 IPv6
ntcp2.proxy                            | 为 NTCP2 指定代理服务器。应为 http://address:port 或 socks://address:port

### SSU2

Option                                 | Description
-------------------------------------- | --------------------------------------
ssu2.enabled                           | 启用 SSU2（默认：true）
ssu2.published                         | 启用入站 SSU2 连接（默认：true）
ssu2.port                              | 监听入站 SSU2 连接的端口（默认：自动 - 使用通用部分的 `port`）
ssu2.proxy                             | 为 SSU2 指定 UDP socks5 代理服务器。应为 socks://address:port  
ssu2.mtu4                              | 本地 IPv4 的 MTU（默认：自动）
ssu2.mtu6                              | 本地 IPv6 的 MTU（默认：自动）
    
下面所有选项仍可在命令行中设置，但更建议写在配置文件中：

### HTTP Web 控制台

Option                                 | Description
-------------------------------------- | --------------------------------------
http.enabled                           | 是否启用 Web 控制台（默认：true）
http.address                           | 监听地址（HTTP 服务器）
http.port                              | 监听端口（HTTP 服务器）（默认：7070）
http.auth                              | 为 Web 控制台启用基本 HTTP 认证（默认：false）
http.user                              | 基本认证的用户名（默认：i2pd）
http.pass                              | 基本认证的密码（默认：随机，见日志）
http.strictheaders                     | 在 WebUI 上启用严格的主机名检查（默认：true）
http.hostname                          | WebUI 期望的主机名（默认：localhost）
http.theme                             | 设置 Web 控制台的 CSS 主题。内置主题：`black`、`white`（默认：`light`）。要使用自定义主题，将 CSS 文件（例如 `hacker.css`）放在 `%DataDir%/webconsole/` 并使用 `--http.theme=hacker`。

### HTTP 代理

Option                                 | Description
-------------------------------------- | --------------------------------------
httpproxy.enabled                      | 是否启用 HTTP 代理（默认：true）
httpproxy.address                      | 监听地址（HTTP 代理）
httpproxy.port                         | 监听端口（HTTP 代理）（默认：4444）
httpproxy.addresshelper                | 启用地址助手（jump）（默认：true）
httpproxy.keys                         | HTTP 代理本地目的地的可选密钥文件
httpproxy.signaturetype                | 如果设置了密钥文件，则用于新密钥的签名类型（默认：7）
httpproxy.inbound.length               | 如果设置了密钥文件，入站隧道长度（默认：3）
httpproxy.inbound.quantity             | 如果设置了密钥文件，入站隧道数量（默认：5）
httpproxy.inbound.lengthVariance       | 如果设置了密钥文件，入站隧道长度方差（默认：0）
httpproxy.outbound.length              | 如果设置了密钥文件，出站隧道长度（默认：3）
httpproxy.outbound.quantity            | 如果设置了密钥文件，出站隧道数量（默认：5）
httpproxy.outbound.lengthVariance      | 如果设置了密钥文件，出站隧道长度方差（默认：0）
httpproxy.outproxy                     | HTTP 代理上游出站代理 URL（如 http://exit.stormycloud.i2p）
httpproxy.addresshelper                | 启用或禁用 addresshelper（默认：true）
httpproxy.senduseragent                | 若启用则透传用户的 User-Agent（默认：false）
httpproxy.i2cp.leaseSetType            | 要发送的 LeaseSet 类型。1、3 或 5（默认：3）
httpproxy.i2cp.leaseSetEncType         | 在 LeaseSet 类型为 3 或 5 时使用的加密类型，逗号分隔
httpproxy.i2p.streaming.profile        | HTTP 代理带宽使用配置。1 - 批量（高），2 - 交互（低）（默认：1）
httpproxy.i2p.streaming.maxWindowSize  | 流的最大窗口大小（默认：512）

### Socks 代理

Option                                 | Description
-------------------------------------- | --------------------------------------
socksproxy.enabled                     | 是否启用 SOCKS 代理（默认：true）
socksproxy.address                     | 监听地址（SOCKS 代理）
socksproxy.port                        | 监听端口（SOCKS 代理）（默认：4447）
socksproxy.keys                        | SOCKS 代理本地目的地的可选密钥文件
socksproxy.signaturetype               | 如果设置了密钥文件，则用于新密钥的签名类型（默认：7）
socksproxy.inbound.length              | 如果设置了密钥文件，入站隧道长度（默认：3）
socksproxy.inbound.quantity            | 如果设置了密钥文件，入站隧道数量（默认：5）
socksproxy.inbound.lengthVariance      | 如果设置了密钥文件，入站隧道长度方差（默认：0）
socksproxy.outbound.length             | 如果设置了密钥文件，出站隧道长度（默认：3）
socksproxy.outbound.quantity           | 如果设置了密钥文件，出站隧道数量（默认：5）
socksproxy.outbound.lengthVariance     | 如果设置了密钥文件，出站隧道长度方差（默认：0）
socksproxy.outproxy.enabled            | 启用或禁用 SOCKS5 出站代理（默认：false）
socksproxy.outproxy                    | 出站代理地址（IP 或本地）。I2P 外的请求将发送到此处。
socksproxy.outproxyport                | 出站代理端口。若为 0，socksproxy.outproxy 包含本地套接字路径
socksproxy.i2cp.leaseSetType           | 要发送的 LeaseSet 类型。1、3 或 5（默认：3）
socksproxy.i2cp.leaseSetEncType        | 在 LeaseSet 类型为 3 或 5 时使用的加密类型，逗号分隔
socksproxy.i2p.streaming.profile       | SOCKS 代理带宽使用配置。1 - 批量（高），2 - 交互（低）（默认：1）
socksproxy.i2p.streaming.maxWindowSize | 流的最大窗口大小（默认：512）

### SAM 接口

Option                                 | Description
-------------------------------------- | --------------------------------------
sam.enabled                            | 是否启用 SAM（默认：true）
sam.address                            | 监听地址（SAM 桥）
sam.port                               | SAM 桥端口。通常为 7656。若未指定则 SAM 关闭
sam.singlethread                       | 若为 false，每个 SAM 会话在自己的线程中运行（默认：true）

### BOB 接口

Option                                 | Description
-------------------------------------- | --------------------------------------
bob.enabled                            | 是否启用 BOB（默认：false）
bob.address                            | 监听地址（BOB 命令通道）
bob.port                               | BOB 命令通道端口。通常为 2827。若未指定则 BOB 关闭

### I2CP 接口

Option                                 | Description
-------------------------------------- | --------------------------------------
i2cp.enabled                           | 是否启用 I2CP（默认：true）
i2cp.address                           | 监听地址，或 Android LocalSocket 的抽象地址
i2cp.port                              | I2CP 服务器端口。通常为 7654。对 Android 忽略
i2cp.singlethread                      | 若为 false，每个 I2CP 会话在自己的线程中运行（默认：true）
i2cp.inboundlimit                      | 在 BandwidthLimitsMessage 中返回的客户端入站限制（KBps）。若为 0 则为路由器带宽（默认：0）
i2cp.outboundlimit                     | 在 BandwidthLimitsMessage 中返回的客户端出站限制（KBps）。若为 0 则为路由器带宽（默认：0）

### I2PControl 接口

Option                                 | Description
-------------------------------------- | --------------------------------------
i2pcontrol.enabled                     | 是否启用 I2P control（默认：false）
i2pcontrol.address                     | 监听地址（IP 或本地套接字路径）
i2pcontrol.port                        | I2P control 服务端口。通常为 7650。若为 0，则 i2pcontrol.address 包含本地套接字路径
i2pcontrol.password                    | I2P control 认证密码（默认：itoopie）
i2pcontrol.cert                        | I2P control HTTPS 证书文件名（默认：i2pcontrol.crt.pem）
i2pcontrol.key                         | I2P control HTTPS 证书密钥文件名（默认：i2pcontrol.key.pem）

### UPNP

Option                                 | Description
-------------------------------------- | --------------------------------------
upnp.enabled                           | 启用或禁用 UPnP，CLI 默认为 false，GUI（Windows、Android）默认为 true
upnp.name                              | i2pd 在 UPnP 转发表中的显示名称（默认：I2Pd）

### 密码学

Option                                 | Description
-------------------------------------- | --------------------------------------
precomputation.elgamal                 | 使用 ElGamal 预计算表（x86-64 默认为 false，其他平台为 true）

### 重新播种（Reseeding）

Option                                 | Description
-------------------------------------- | --------------------------------------
reseed.verify                          | 验证 .su3 签名（默认：false）
reseed.urls                            | Reseed 的 URL，逗号分隔
reseed.yggurls                         | Yggdrasil 的 Reseed URL，逗号分隔
reseed.file                            | 本地 .su3 文件路径或用于 Reseed 的 HTTPS URL
reseed.zipfile                         | 用于 Reseed 的本地 .zip 文件路径
reseed.threshold                       | 在请求 Reseed 前已知路由器的最小数量（默认：25）
reseed.proxy                           | https/socks Reseed 代理的 URL

### 地址簿选项

Option                                 | Description
-------------------------------------- | --------------------------------------
addressbook.enabled                    | 启用或禁用地址簿（默认：true）  
addressbook.defaulturl                 | 地址簿订阅 URL。仅用于初始化地址簿。
addressbook.subscriptions              | 地址簿订阅的 URL，逗号分隔。注意 defaulturl 不会自动加入订阅 URL
addressbook.hostsfile                  | 以 hosts.txt 格式导出地址簿的文件

### 限制

Option                                 | Description
-------------------------------------- | --------------------------------------
limits.transittunnels                  | 覆盖中转隧道的最大数量（默认：10000）
limits.openfiles                       | 限制打开的文件描述符数量（默认：0 - 使用系统限制）
limits.coresize                        | core 文件的最大大小（KB）（默认：0 - 使用系统限制）
limits.zombies                         | 若成功创建的隧道比例低于该最小百分比，则暂停隧道清理（默认 [%]：0.00）

### 信任选项

Option                                 | Description
-------------------------------------- | --------------------------------------
trust.enabled                          | 启用显式信任选项（默认：false）
trust.family                           | 仅与指定 Family 中的路由器建立直接 I2P 连接
trust.routers                          | 仅与此处指定的路由器建立直接 I2P 连接。以逗号分隔的 base64 身份列表
trust.hidden                           | 我们是否应对其他路由器隐藏本路由器？（默认：false）

### 探测（Exploratory）隧道

Option                                 | Description
-------------------------------------- | --------------------------------------
exploratory.inbound.length             | 探测入站隧道长度（默认：2）
exploratory.inbound.quantity           | 探测入站隧道数量（默认：3）
exploratory.outbound.length            | 探测出站隧道长度（默认：2）
exploratory.outbound.quantity          | 探测出站隧道数量（默认：3）

### 共享本地目的地

Option                                 | Description
-------------------------------------- | --------------------------------------
shareddest.inbound.length              | 共享本地目的地的入站隧道长度（默认：3）
shareddest.inbound.quantity            | 共享本地目的地的入站隧道数量（默认：3）
shareddest.outbound.length             | 共享本地目的地的出站隧道长度（默认：3）
shareddest.outbound.quantity           | 共享本地目的地的出站隧道数量（默认：3）
shareddest.i2cp.leaseSetType           | 共享本地目的地的 LeaseSet 类型（默认：3）
shareddest.i2cp.leaseSetEncType        | 共享本地目的地的 LeaseSet 加密类型（默认：0,4）
shareddest.i2p.streaming.profile       | 共享本地目的地的带宽使用配置（默认：2）

### 时间同步

Option                                 | Description
-------------------------------------- | --------------------------------------
nettime.enabled                        | 启用 NTP 同步（默认：false）
nettime.ntpservers                     | NTP 服务器的逗号分隔列表（默认：pool.ntp.org）
nettime.ntpsyncinterval                | NTP 时间同步间隔（小时）（默认：72）

### 网络信息持久化

Option                                 | Description
-------------------------------------- | --------------------------------------
persist.profiles                       | 启用将对等体档案持久化到磁盘（默认：true）

### 网状网络传输

Option                                 | Description
-------------------------------------- | --------------------------------------
meshnets.yggdrasil                     | 支持通过 Yggdrasil 的传输（默认：false）
meshnets.yggaddress                    | 要发布的本地 Yggdrasil 地址

### Windows 特定选项

Option                                 | Description
-------------------------------------- | --------------------------------------
insomnia                               | 防止系统休眠
close                                  | 关闭时的动作：最小化、退出、询问

### UNIX 特定选项

Option                                 | Description
-------------------------------------- | --------------------------------------
unix.handle_sigtstp                    | 处理 SIGTSTP 与 SIGCONT 信号（默认：false）

`handle_sigtstp` 启用对 SIGTSTP 和 SIGCONT 信号的处理（自 2.43.0 起）。

可以使用在运行 i2pd 的终端里按 Ctrl+Z 或通过 `kill` 命令来发送 SIGTSTP。收到该信号后，i2pd 将切换到离线模式，停止发送流量并停止清理 netDb。所有活动隧道将被冻结。

要恢复网络连接，你需要向该进程发送 SIGCONT 信号。若隧道在冻结状态期间到期，它们将被清除，并建立新的隧道。

本地地址簿
-----------------

工作目录中还有一个特殊的地址簿配置文件 `addressbook/local.csv`。
如果你在路由器上运行该二级域（2ld）地址并且它已在地址簿中呈现，它可让你的路由器充当三级域（3ld）域名的解析器。
只有 i2pd 能解析此类地址，通过发送特殊的数据报请求来完成。