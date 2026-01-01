安装
==========

## 从源代码构建

请参阅[文档的“开发者部分”](../devs/building/requirements.md)，了解如何在你的操作系统上从源代码构建 i2pd。


## 通过预编译软件包安装

安装 i2pd 的最简单方法是使用预编译的软件包和二进制文件。
前往[最新发布页面](https://github.com/PurpleI2P/i2pd/releases/latest)，为你的操作系统选择合适的文件。


## Windows

查看[最新发布页面](https://github.com/PurpleI2P/i2pd/releases/latest)，并根据你的系统架构选择文件：
* `i2pd_*_win32_mingw.zip` —— 适用于 x86 系统
* `i2pd_*_win64_mingw.zip` —— 适用于 x86_64（x64）系统
* `i2pd_*_winxp_mingw.zip` —— 适用于 Windows XP 的兼容构建
* `setup_i2pd_*.exe` —— 可以自动检测所用系统并正确解压配置文件的安装程序


## Android

你可以在 F-Droid 获取应用：

[<img src="https://fdroid.gitlab.io/artwork/badge/get-it-on.png"
     alt="Get it on F-Droid"
     height="80">](https://f-droid.org/packages/org.purplei2p.i2pd/)

或者，你可以通过 PurpleI2P 社区成员 [R4SAS](https://twitter.com/i2pr4sas) 运营的 F-Droid 软件源安装 i2pd：[软件源主页](https://fdroid.i2pd.xyz/)

此外，还可以在 i2pd-android 仓库的[发布页面](https://github.com/PurpleI2P/i2pd-android/releases/latest)找到安装包


## Docker 镜像

你可以使用我们的[预构建 Docker 镜像](https://hub.docker.com/r/purplei2p/i2pd/)。

    docker pull purplei2p/i2pd


## Linux

### 跨发行版兼容的 GUI（图形界面）

你可以使用 `flatpak` 从 Flathub 安装 i2pd GUI：

    sudo apt install flatpak
    flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
    flatpak install flathub website.i2pd.i2pd

### Arch

i2pd 软件包在 Arch 的[extra 仓库](https://archlinux.org/packages/extra/x86_64/i2pd/)提供正式版（release version），在 [AUR](https://aur.archlinux.org/packages/i2pd-git/) 提供每日构建版（nightly version）。

若需下载，你可以运行

     pacman -S i2pd --noconfirm


### Gentoo

i2pd [在 Gentoo 主仓库中有可用的 ebuild](https://packages.gentoo.org/packages/net-vpn/i2pd)。截至 2018 年 5 月，该 ebuild 仍被标记为“不稳定（unstable）”，因此如果你在 “stable” 分支下使用 Gentoo，则需要在 package.keywords 中添加例外。
要安装 i2pd，输入命令：
    
    emerge --ask net-vpn/i2pd

如果你使用 `gcc` 编译软件包并希望启用 CMake 的加固特性，请使用 `i2p-hardening` 标志（推荐）。
如果你打算使用 websocket 服务器，请启用 `websocket` 标志。

### Debian

在[最新发布页面](https://github.com/PurpleI2P/i2pd/releases/latest)查找 Debian 软件包。

或者，你可以使用由 PurpleI2P 社区成员 [R4SAS](https://twitter.com/i2pr4sas) 运营的仓库安装 i2pd。

安装 apt-transport-https 软件包

    sudo apt-get install apt-transport-https

自动添加仓库

    wget -q -O - https://repo.i2pd.xyz/.help/add_repo | sudo bash -s -

之后你可以像安装其他软件包一样安装 i2pd：

    sudo apt-get update
    sudo apt-get install i2pd

在[此处](https://repo.i2pd.xyz/.help/readme.html)了解有关 Debian 仓库的更多信息。

### Fedora/CentOS

你可以从由 PurpleI2P 社区成员 [villain](https://twitter.com/el_villano_loco) 运营、由 [R4SAS](https://twitter.com/i2pr4sas) 维护的[仓库](https://copr.fedorainfracloud.org/coprs/supervillain/i2pd/)安装 i2pd。

#### CentOS 7：

    curl -s https://copr.fedorainfracloud.org/coprs/supervillain/i2pd/repo/epel-7/supervillain-i2pd-epel-7.repo -o /etc/yum.repos.d/i2pd-epel-7.repo
    yum install epel-release -y
    yum install i2pd -y
    systemctl enable --now i2pd

#### Fedora：

    dnf copr enable supervillain/i2pd
    dnf install i2pd -y
    systemctl enable --now i2pd

### Ubuntu

你可以从[最新的发布页面](https://github.com/PurpleI2P/i2pd/releases/latest)安装二进制包。 

或者，你可以使用 [PPA 仓库](https://launchpad.net/~purplei2p/+archive/ubuntu/i2pd)或下文提供的[仓库](#debian)（由 PurpleI2P 社区成员 [R4SAS](https://twitter.com/i2pr4sas) 运营）。

    sudo add-apt-repository ppa:purplei2p/i2pd
    sudo apt-get update
    sudo apt-get install i2pd

### Void

你可以通过使用 /current [xpbs 仓库](https://github.com/void-linux/void-packages/tree/master/srcpkgs/i2pd) 安装 i2pd。

若要安装并设定守护进程，请执行下列步骤：

    sudo xbps-install -Sy i2pd
    sudo ln -s /etc/sv/i2pd /var/service

## FreeBSD

你可以通过[ports](https://www.freshports.org/security/i2pd/)安装 i2pd。

## OpenBSD

你可以通过[ports](https://openports.pl/path/net/i2pd)安装 i2pd

## MacOS X

你可以通过 [brew](https://brew.sh/) 包管理器安装 i2pd：

    brew install i2pd

或者使用[最新发布页面](https://github.com/PurpleI2P/i2pd/releases/latest)提供的预编译静态二进制文件。
