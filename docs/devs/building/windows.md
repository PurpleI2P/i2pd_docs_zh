在 Windows 上构建
=========================

构建 i2pd 在 Windows 上有两种方式。哪种更好取决于你的需求和个人偏好。一种是使用 msys2 和[类 Unix 的构建环境](unix.md)，另一种是使用 Visual Studio。虽然对 i2pd 守护进程的最终用户来说两者可能没有差别，但开发者在选择以便将自己的软件链接到 libi2pd 时，应注意两种编译器之间 C++ 名字改编（name mangling）的差异。

如果你不熟悉 C++、系统上也未安装任何开发工具，而且你的唯一目标是从最新源码把 i2pd 跑起来，可以考虑使用 msys2。尽管它依赖命令行操作，但过程应当相对简单。

本指南将对两种方式都使用 CMake，并假定你通常把项目放在 C:\dev\ 作为开发目录以方便说明。如果不是这种情况，请相应调整路径。注意 msys 使用类 Unix 的路径，如 /c/dev/ 表示 C:\dev\。

msys2
-----

从 https://msys2.github.io 获取安装包 `msys2-$ARCH-*.exe`

其中 $ARCH 为 `i686` 或 `x86_64`（与你的系统匹配）。

- 打开 MSYS2 Shell（开始菜单中）。
- 安装所有先决条件并下载 i2pd 源码：

        export ARCH='i686'     # 或 'x86_64'
        export MINGW='mingw32' # 或 'mingw64'
        pacman -S mingw-w64-$ARCH-boost mingw-w64-$ARCH-openssl mingw-w64-$ARCH-gcc git make
        mkdir -p /c/dev/i2pd
        cd /c/dev/i2pd
        git clone https://github.com/PurpleI2P/i2pd.git
        cd i2pd
        # 我们需要 PATH 中的编译器；Windows 上 PATH 往往非常杂乱
        export PATH=/$MINGW/bin:/usr/bin
        make

### 注意事项

按上面所述限制 PATH 非常重要。
如果你安装了 Strawberry Perl 和/或 Mercurial，可能会从错误的位置拾取 gcc 和 openssl。

如果你使用预编译头来加快编译（推荐），一旦编译选项因各种原因发生变化，可能会导致问题。
只需删除构建文件夹中的 `stdafx.h.gch`（注意扩展名）即可。

如果你是 Arch Linux 用户，请避免使用 `pacman -Syu` 更新系统。
请始终按主页说明单独更新 runtime，否则可能遇到 DLL 兼容性问题。

### AES-NI

如果你的处理器支持 [AES 指令集](https://en.wikipedia.org/wiki/AES_instruction_set)，
使用 `make USE_AESNI=1` 替代 `make`。不过不会进行检测，它会编译通过，
但如果处理器不支持该特性，运行时可能以 `Illegal instruction` 崩溃。

你应该可以运行 ./i2pd。如果需要从新的 shell 启动，
考虑启动 “MinGW-w64 Win32 Shell” 而不是 “MSYS2 Shell”，
因为它会将 `/minw32/bin` 添加到 PATH。

### UPnP

可以通过 MSYS2 安装并使用 `USE_UPNP` 选项进行构建。

	export ARCH='i686' # 或 'x86_64'
	pacman -S mingw-w64-$ARCH-miniupnpc
	make USE_UPNP=yes

使用 Visual Studio
-------------------

### 通过 vcpkg 安装依赖

要求：

* [vcpkg](https://vcpkg.io/)（注意：由于微软的遥测策略，强烈建议参考[此文档](https://github.com/microsoft/vcpkg#telemetry)选择退出数据收集。）
* [Visual Studio Community 版](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)（已用 VS2022 测试）

本部分假定你已经会在 Visual Studio 中使用并集成 vcpkg。
如果不确定，请阅读[这里](https://learn.microsoft.com/en-us/vcpkg/)。
进入 vcpkg 目录并运行（32 位构建将 x64-windows 替换为 x86-windows）：

    .\vcpkg install boost:x64-windows miniupnpc:x64-windows openssl:x64-windows

现在你可以直接跳到[创建 Visual Studio 项目](#creating-visual-studio-project)。

### 不使用 vcpkg

构建所需：

* [CMake](https://cmake.org/)（测试版本 3.1.3）
* [Visual Studio Community 版](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)（测试版本 VS2013 Update 4）
* [Boost](http://www.boost.org/)（测试版本 1.59）
* 可选 [MiniUPnP](http://miniupnp.free.fr)（测试版本 1.9），只需要少量客户端头文件
* OpenSSL（测试版本 1.0.1p 和 1.0.2e），如果从源码构建（推荐），还需要
	* [Netwide assembler](http://www.nasm.us/)
	* Strawberry Perl 或 ActiveState Perl，请勿尝试 msys2 的 perl，它无法工作

### 构建 Boost

打开命令提示符（构建 Boost 无需使用 Visual Studio 命令提示符）并运行：

	cd C:\dev\boost
	bootstrap
	b2 toolset=msvc-12.0 --build-type=complete --with-filesystem --with-program_options --with-date_time

如果你在 64 位 Windows 上，并且也想构建 64 位版本：

	b2 toolset=msvc-12.0 --build-type=complete --stagedir=stage64 address-model=64 --with-filesystem --with-program_options --with-date_time

Boost 编译完成后，将环境变量 `BOOST_ROOT` 设置为 Boost 解压目录，例如 C:\dev\boost。

如果你只计划构建某种变体，例如仅 Debug 且静态链接，
或者你的空间/时间有限，可以考虑使用 `--build-type=minimal`。
关于测试构建的细节可以参考 [appveyor.yml](../appveyor.yml)。

### 构建 OpenSSL

下载 OpenSSL，例如使用 git

	git clone https://github.com/openssl/openssl.git
	cd openssl
	git checkout OpenSSL_1_0_1p

现在打开 Visual Studio 命令提示符并切换到 OpenSSL 目录

	set "PATH=%PATH%;C:\Program Files (x86)\nasm"
	perl Configure VC-WIN32 --prefix=c:\OpenSSL-Win32
	ms\do_nasm
	nmake -f ms\ntdll.mak
	nmake -f ms\ntdll.mak install

现在应已安装到 C:\OpenSSL-Win32。

注意你可以考虑向 CMake 提供 `-DOPENSSL_ROOT_DIR`，和/或
如果你计划维护多个版本（例如 64 位 和/或 静态/共享），可创建指向 C:\OpenSSL 的联接（mklink /J）。
详见 `C:\Program Files (x86)\CMake\share\cmake-3.3\Modules\FindOpenSSL.cmake`。

### 获取 miniupnpc

如果你在启用 UPnP 的路由器后面且不想手动配置端口转发，
可以考虑使用 [MiniUPnP](http://miniupnp.free.fr) 客户端。
i2pd 可以构建为使用 miniupnpc 共享库（DLL）来打开所需端口。
你需要准备好头文件以便构建具有该支持的 i2pd。
将客户端源码解压到子目录，例如 `C:\dev\miniupnpc`。
你可能希望把下载归档中带版本号的目录名移除版本号。

### 创建 Visual Studio 项目

启动 CMake GUI，定位到 i2pd 目录，选择构建目录（如 ./out），并配置选项。

或者，如果你喜欢用命令行，可以试试：

	mkdir i2pd\out
	cd i2pd\out
	cmake ..\build -G "Visual Studio 12 2013" -DWITH_UPNP=ON -DWITH_PCH=ON -DCMAKE_INSTALL_PREFIX:PATH=C:\dev\Debug_Win32_stage

如果未找到必要的文件，`WITH_UPNP` 将保持关闭。
vcpkg 用户可参考[这里](https://learn.microsoft.com/en-us/vcpkg/users/buildsystems/cmake-integration)获取 CMake 选项。

### 构建 i2pd

你可以用 Visual Studio 打开生成的解决方案/项目并从那里构建，
或者使用 `cmake --build . --config Release --target install`，或
[MSBuild 工具](https://msdn.microsoft.com/en-us/library/dd293626.aspx)

	msbuild i2pd.sln /p:Configuration=Release