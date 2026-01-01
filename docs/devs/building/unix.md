在类 Unix 系统上构建
=============================
本文档涵盖：
* [Debian/Ubuntu](#debian-ubuntu)（包括打包）
* [Arch/Manjaro](#arch-manjaro)
* [Fedora/RHEL/CentOS Stream](#fedora-rhel-centos-stream)
* [macOS](#macos)
* [FreeBSD](#freebsd)
* [OpenBSD](#openbsd)
* [Solaris/OpenIndiana](#solaris-openindiana)
请确保已为您的系统安装所有所需的依赖项。
请参阅 [此页面](requirements.md) 获取常见依赖要求。

## 克隆并构建（使用 CMake）
```bash
git clone https://github.com/PurpleI2P/i2pd.git
cd i2pd
mkdir -p build && cd build
cmake <cmake options> ..
cmake --build . -j
```
…或者使用普通 make 的快速简易方式：
```bash
cd i2pd/
make
```
运行测试：
```bash
cd i2pd/tests/
make
```
在成功构建后进行安装：
```bash
sudo make install
```

## CMake 选项
以 `-D<key>=<value>` 的形式传递选项（参见 `man 1 cmake`）：
* `CMAKE_BUILD_TYPE` 构建配置文件（Debug/Release，默认：none）
* `WITH_BINARY` 构建 i2pd（默认：ON）
* `WITH_LIBRARY` 构建 libi2pd（默认：ON）
* `WITH_STATIC` 构建静态库和静态二进制文件（默认：OFF）
* `WITH_UPNP` 启用 UPnP 支持（需要 miniupnpc，默认：OFF）
* `WITH_AESNI` 启用 AES-NI 支持（默认：ON）
* `WITH_HARDENING` 启用加固选项（GCC/Clang，默认：OFF）
* `WITH_MESHNET` 为 cjdns 测试网络构建（不适用于主网，默认：OFF）
* `WITH_ADDRSANITIZER` 启用 AddressSanitizer（默认：OFF）
* `WITH_THREADSANITIZER` 启用 ThreadSanitizer（默认：OFF）

列出已缓存的选项：
```bash
cmake -L
```

## Debian/Ubuntu
工具：
```bash
sudo apt install build-essential debhelper cmake
```
库：
```bash
sudo apt install \
  libboost-date-time-dev \
  libboost-filesystem-dev \
  libboost-program-options-dev \
  libboost-system-dev \
  libssl-dev \
  zlib1g-dev
```
UPnP（可选）：
```bash
sudo apt install libminiupnpc-dev
```
构建 `.deb` 包：
```bash
sudo apt install fakeroot devscripts dh-apparmor
cd i2pd
debuild --no-tgz-check -us -uc -b
```
（UPnP 开发包名称：`libminiupnpc-dev`。）

## Arch/Manjaro
工具：
```bash
sudo pacman -S --needed base-devel cmake
```
库：
```bash
sudo pacman -S --needed boost openssl zlib
```

## Fedora/RHEL/CentOS Stream
工具：
```bash
sudo dnf install make cmake gcc gcc-c++
```
库：
```bash
sudo dnf install boost-devel openssl-devel zlib-devel
```
UPnP（可选）：
```bash
sudo dnf install miniupnpc-devel
```
静态构建（可选）：
```bash
sudo dnf install boost-static
```
（UPnP/Boost 静态包名称：`miniupnpc-devel`、`boost-static`。在某些类似 RHEL 的系统上，您可能需要启用 EPEL/CRB 仓库。）

## macOS
需要 Homebrew 和 Xcode Command Line Tools。
安装依赖：
```bash
brew install boost openssl@3 cmake make
```
构建：
```bash
make HOMEBREW=1 -j8
```
安装到系统根目录（`/`）：
```bash
sudo make install HOMEBREW=1
```
安装到 Homebrew 前缀（Intel 为 `/usr/local`，Apple Silicon 为 `/opt/homebrew`）：
```bash
sudo make install HOMEBREW=1 PREFIX="$(brew --prefix)"
```

## FreeBSD
使用基础编译器（Clang）。以 root 身份安装工具和库：
```bash
pkg install boost-libs cmake gmake
```
UPnP（可选）：
```bash
pkg install miniupnpc
```
如果使用 BSD Makefile 路径，请使用 GNU make 构建：
```bash
gmake
```
（注意：FreeBSD 的基础系统中已包含 OpenSSL 和 zlib；您不需要额外安装 SSL 包。）

## OpenBSD
使用基础编译器（Clang）。以 root 身份安装工具和库：
```bash
pkg_add boost cmake gmake
```
UPnP（可选）：
```bash
pkg_add miniupnpc
```
如果使用 BSD Makefile 路径，请使用 GNU make 构建：
```bash
gmake
```
（注意：OpenBSD 的基础系统中已包含 LibreSSL 和 zlib；您不需要额外安装 SSL 包。）

## Solaris/OpenIndiana
安装所需软件包：
```bash
pkg install developer/gcc-14
pkg install developer/build/cmake
pkg install system/library/boost
pkg install developer/build/gnu-make
```
然后如果直接调用 Makefile，请使用 `gmake`。
（现代 OpenIndiana 提供 GCC 14；软件包 FMRI 如 `developer/gcc-14` 和 `system/library/boost` 遵循 IPS 命名规则。）
