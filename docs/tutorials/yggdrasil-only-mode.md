# 仅通过 Yggdrasil 的路由器配置

适用于仅通过 Yggdrasil 与其他路由器和 reseed 服务器通信的路由器。  
先启动 yggdrasil，并确保你使用的是全新安装的 i2pd。创建 `~/.i2pd/i2pd.conf`

```
daemon=true # 注释：在 Windows 上请移除此项
ipv4=false
ipv6=false
ssu=false
ntcp2.enabled=false
ssu2.enabled=false
meshnets.yggdrasil=true  
```

如果你使用 Yggdrasil 子网，并希望将路由器绑定到特定地址，请使用
`meshnets.yggaddress=` 指定你的本地 yggdrasil 地址

启动 i2pd，它还会从 Yggdrasil 的 reseed 服务器进行补种。不会涉及明网通信。   
重要：确保系统时钟已同步。