运行 i2pd
============

启动、停止与重新加载配置
----------------------------------------------

本章说明如何在类 Unix 操作系统下启动和管理 i2pd 守护进程。

当你已从源代码构建好 i2pd 后，直接运行二进制文件：

    ./i2pd

显示所有可用选项：

    ./i2pd --help

i2pd 可以通过信号进行控制。进程 ID 默认写入文件 `~/.i2pd/i2pd.pid` 或 `/var/run/i2pd/i2pd.pid`。你可以使用 `kill` 工具如下发送信号：

    kill -INT $( cat /var/run/i2pd/i2pd.pid )

i2pd 支持以下信号：

* INT - 优雅关闭。i2pd 将等待最多 10 分钟后停止。再次发送 INT 信号可立即关闭 i2pd。
* HUP - 重新加载隧道配置文件。


### systemd 单元

某些 i2pd 软件包附带 systemd 控制单元，对于使用 systemd 的系统，可以通过它管理 i2pd。

启动/停止 i2pd：

    sudo systemctl start i2pd.service
    sudo systemctl stop i2pd.service --no-block

stop 命令会发起优雅关闭流程，i2pd 会在完成中转隧道路由后停止（最多 10 分钟）。

启用/禁用开机自启动：

    sudo systemctl enable i2pd.service
    sudo systemctl disable i2pd.service


推荐的从源代码构建的 i2pd 运行方式
---------------------------------------------

以下命令展示了在不使用包管理器的情况下运行从源代码构建的 i2pd 的推荐方法。以这种方式安装将确保所有 i2pd 相关文件存储在 `$HOME/dist`。

    mkdir $HOME/dist
    cp i2pd $HOME/dist
    cp -R contrib/certificates $HOME/dist
    cp contrib/i2pd.conf $HOME/dist
    cd $HOME/dist
    ulimit -n 4096  # 仅在 Linux 上，用于提升打开文件数限制

然后，进入安装目录并运行：

    ./i2pd --datadir .

覆盖 systemd 服务参数
-----

若要覆盖 systemd 服务的默认参数，创建 `/etc/systemd/system/i2pd.service.d/override.conf` 文件并放入需要覆盖的选项。别忘了使用选项节。

```
mkdir -p /etc/systemd/system/i2pd.service.d/
touch /etc/systemd/system/i2pd.service.d/override.conf
```

启用 coredump 并提升 `nofile` 限制的示例内容

```
[Service]
LimitNOFILE=16386
LimitCORE=infinity
```