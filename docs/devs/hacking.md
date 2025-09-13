# 在 i2pd 上进行开发（Hacking on i2pd）

本文包含在 i2pd 上进行开发的笔记

## 事前准备

本指南假设你具备：

* 对 C++ 有较好的理解
* 对 I2P 在 i2np 层及以上的工作机制有基本了解

## 总体结构

关于多线程的注意事项

* 每个组件都在其各自的线程中运行

* 每个组件（通常）都有一个公共函数 `GetService()`，可用于获取其使用的 `boost::asio::io_service`。

* 在组件/线程之间通信时，务必使用 `GetService().post()`，并注意栈上分配的内存。

### NetDb

#### NetDb.h

`i2p::data::netdb` 是一个 `i2p::data::NetDb` 实例，用于处理和分发从传输层传入的入站 I2NP 消息。

自 2.10.1 起在 `i2p::data::netdb` 处作为全局单例。

#### NetDbRequests.h

用于挂起的 RouterInfo/LeaseSet 查找与存储请求。

### ClientContext 

#### ClientContext.h

`i2p::client::ClientContext` 会生成 I2P 路由器使用的所有目的地（Destination），包括共享的本地目的地。

自 2.10.1 起在 `i2p::client::context` 处作为全局单例。

### Daemon

文件：Daemon.cpp

`i2p::util::Daemon_Singleton_Private` 的子类实现守护进程的启动与关闭，并创建 HTTP WebUI 和 I2P 控制服务器。

### Destinations

#### Destination.h

每个目的地都在其自己的线程中运行。

##### i2p::client::LeaseSetDestination

`i2p::client::ClientDestination` 的基类。

##### i2p::client::ClientDestination

能够创建（TCP/I2P）流以及数据报会话的目的地。

#### Streaming.h

##### i2p::stream::StreamingDestination

不实现任何与目的地相关的成员，这个名字有点误导性。

持有一个 `i2p::client::ClientDestination`，并在目的地线程中运行。

任何在目的地线程之外创建或使用流的人都必须了解多线程 C++ 的后果 :^)

如果你使用 Streaming，请考虑通过 `ClientDestination::GetService().post()` 在目的地线程内运行所有代码。

#### Garlic.h

提供目的地间（Inter-Destination）的路由原语。

##### i2p::garlic::GarlicDestination

`i2p::client::LeaseSetDestination` 的子类，用于沿共享的路由路径发送消息。

##### i2p::garlic::GarlicRoutingSession

我们与另一个目的地之间的点对点对话。

##### i2p::garlic::GarlicRoutingPath

当前路由会话所使用的一条路由路径。指定应使用哪个出站隧道，以及在 `OBEP` 到 `IBGW` 的跨隧道通信中使用哪个远程 LeaseSet。

成员：

* outboundTunnel (OBEP)
* remoteLease (IBGW)
* rtt（往返时间）
* updatedTime（此路径的 IBGW/OBEP 上次更新的时间）
* numTimesUsesd（此路径被使用的次数）

### Transports

每个传输（transport）都在其自身的线程中运行。

#### Transports.h

`i2p::transport::Transports` 包含 NTCP 和 SSU 传输实例。