# Qt 桌面 GUI 应用构建说明

## 构建要求

Qt 5

## 在 Windows 下

### 不使用 Qt Creator

所有命令应在 MSYS2 mingw32 下运行。

（过时的说明，如果你有可用的说明请提 PR）

```bash
pacman -S git make tar mingw-w64-i686-gcc mingw-w64-i686-boost mingw-w64-i686-libpng mingw-w64-i686-openssl mingw-w64-i686-zlib
pacman -S mingw-w64-i686-qt5-static
pacman -S openssl-devel mingw-w64-i686-miniupnpc
mkdir -p /c/dev/
cd /c/dev/
git clone https://github.com/PurpleI2P/i2pd.git
cd i2pd
git checkout openssl
export MINGW='mingw32'
export PATH=/$MINGW/bin:/usr/bin:/mingw32/qt5-static/bin
cd qt/i2pd_qt
qmake
make USE_UPNP=yes
```

#### 注意事项

按上述方式限制 PATH 很重要。如果你安装了 Strawberry Perl 和/或 Mercurial，它会从错误的位置拾取 gcc 和 openssl。

如果你是 Arch Linux 用户，请避免使用 `pacman -Syu` 更新系统。始终按照主页所述单独更新运行时，否则你可能会遇到 DLL 不兼容问题。

## 在 Debian/Ubuntu 下

### 使用 Qt Creator

```bash

# Debian
sudo apt-get install build-essential g++ make libcrypto++-dev libssl-dev \
  libboost-all-dev libminiupnpc-dev libwebsocketpp-dev libqt5gui5 \
  libqt5widgets5 git zlib1g-dev qt5-qmake qtbase5-dev

# Ubuntu
sudo apt install qtcreator qt5-default build-essential g++ make libcrypto++-dev \
  libssl-dev libboost-all-dev libminiupnpc-dev libwebsocketpp-dev libqt5gui5 git \
  zlib1g-dev

mkdir git
cd git
git clone --recursive https://github.com/PurpleI2P/i2pd-qt.git
```

然后，打开 Qt Creator；打开 `git/i2pd-qt/i2pd_qt.pro`；将构建的 make 参数设置为 `-jCPU-核心数`，例如 `-j7`。最后，点击 Make 按钮。

### 不使用 Qt Creator

```bash
# Debian
sudo apt-get install build-essential g++ make libcrypto++-dev libssl-dev \
  libboost-all-dev libminiupnpc-dev libwebsocketpp-dev libqt5gui5 \
  libqt5widgets5 git zlib1g-dev qt5-qmake qtbase5-dev

# Ubuntu
sudo apt-get install build-essential g++ make libcrypto++-dev libssl-dev \
  libboost-all-dev libminiupnpc-dev libwebsocketpp-dev qt5-default \
  libqt5gui5 git zlib1g-dev

mkdir git
cd git
git clone --recursive https://github.com/PurpleI2P/i2pd-qt.git
cd i2pd-qt
```

发布版构建，

`qmake`

调试版构建，

`qmake i2pd_qt.pro "CONFIG += debug"`

当 `qmake` 完成后，运行：

`make USE_UPNP=yes`

## 在 Kali 下

### 不使用 Qt Creator

```bash
sudo apt-get install build-essential g++ make libcrypto++-dev libssl-dev \
  libboost-all-dev libminiupnpc-dev libwebsocketpp-dev qtbase5-dev \
  libqt5gui5 git zlib1g-dev
mkdir git
cd git
git clone --recursive https://github.com/PurpleI2P/i2pd-qt.git
cd i2pd-qt
```

发布版构建，

`qmake`

调试版构建，

`qmake i2pd_qt.pro "CONFIG += debug"`

当 `qmake` 完成后，运行：

`make USE_UPNP=yes`

## 构建 Flatpak 包

```bash
sudo apt install flatpak flatpak-builder
flatpak --user remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak --user install flathub org.kde.Sdk/x86_64/5.14     # required SDK version might be higher, check the manifest at https://github.com/flathub/website.i2pd.i2pd/blob/master/website.i2pd.i2pd.json
git clone https://github.com/flathub/website.i2pd.i2pd && cd website.i2pd.i2pd 
export FLATPAK_BUILDER_N_JOBS=4                            # build process jobs count
flatpak-builder --user --install --force-clean i2pd_build_dir website.i2pd.i2pd.json
```

## 更多细节

关于构建流程的更多细节，请参见 GitHub Actions：https://github.com/PurpleI2P/i2pd-qt/tree/master/.github/workflows 。