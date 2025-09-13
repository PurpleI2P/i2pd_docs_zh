i2pd 对 I2P 协议的独有更改
======================================

# SAM
SESSION CREATE 和 DEST GENERATE 支持额外参数 `CRYPTO_TYPE`，用于为新的本地目的地（destination）指定加密类型  

`SIGNATURE_TYPE` 的新值：
- `GOST_GOSTR3411256_GOSTR3410CRYPTOPROA` 或 `9`
- `GOST_GOSTR3411512_GOSTR3410TC26A512` 或 `10`

# I2PControl
`i2p.router.status` 在共享本地目的地已就绪时为 1，否则为 0  
`i2p.router.net.tunnels.successrate` 返回隧道创建成功率（百分比）  
`i2p.router.net.total.received.bytes` 返回自上次重启以来接收的总字节数  
`i2p.router.net.total.sent.bytes` 返回自上次重启以来发送的总字节数  

# BOB
与 Java-I2P 不同，i2pd 继续支持 BOB，并具有以下扩展：

## 会话管理

- zap —— 完全终止 BOB 服务，关闭所有活动会话，并阻止服务接受新的连接。
- quit —— 结束当前用户与 BOB 的会话，而不关闭服务本身。

## 隧道控制

- start —— 启动与当前昵称关联的隧道。
- stop —— 停止与当前昵称关联的隧道。
- status `<NICKNAME>` —— 显示由指定昵称标识的隧道状态。
- list —— 列出所有已配置的隧道。
- clear —— 从已配置隧道列表中移除当前昵称。

## 昵称与密钥管理

- setnick `<NICKNAME>` —— 为隧道创建一个新的昵称。
- getnick `<TUNNELNAME>` —— 将当前昵称设置为与指定隧道名称关联的昵称。
- newkeys `[signaturetype]` —— 为当前昵称生成一对新的密钥（公钥和私钥）。  
  默认使用 DSA 签名类型和 ElGamal 加密。  
  要用特定签名类型（例如 EdDSA）生成密钥，请指定所需类型：  
  例如，newkeys 7 生成一对 EdDSA 密钥（仅在 i2pd 中受支持）。
- getkeys —— 获取当前昵称的密钥对。
- setkeys `<BASE64_KEYPAIR>` —— 使用提供的 BASE64 编码密钥对为当前昵称设置密钥对。
- getdest —— 返回当前昵称的目的地。

## 隧道配置

- outhost `<HOSTNAME|IP>` —— 设置隧道的出站主机名或 IP 地址。
- outport `<PORT_NUMBER>` —— 设置隧道将要联系的出站端口号。
- inhost `<HOSTNAME|IP>` —— 设置隧道的入站主机名或 IP 地址。
- inport `<PORT_NUMBER>` —— 设置隧道监听的入站端口号。
- quiet `<True|False>` —— 决定是否发送入站目的地信息。
- option `<KEY>=<VALUE>` —— 为当前隧道设置一个选项。注意：键或值中不要使用空格。
- settunneltype `<socks|httpproxy>` —— 将隧道类型设置为 SOCKS 或 HTTP 代理。

## 其他命令

- lookup `<I2P_HOSTNAME>` —— 对指定 I2P 主机名执行查询并返回其目的地。
- lookuplocal `<I2P_HOSTNAME>` —— 在路由器的 netdb 中查找具有指定地址的 LeaseSet。
- ping `<I2P_HOSTNAME>` —— Ping 远程目的地。成功时返回 "pong from `<BASE32_ADDRESS>`: time=`<VALUE>` ms"，否则返回 "timeout"。
- help `<COMMAND>` —— 提供指定命令的帮助信息；若未指定命令，则列出所有可用命令。