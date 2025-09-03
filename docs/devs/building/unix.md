在 Unix 系统上构建
=============================

本文档涵盖：

* [Debian/Ubuntu](#debian-ubuntu)（包含打包说明）
* [Fedora/Centos](#fedora-centos)
* [Mac OS X](#mac-os-x)
* [FreeBSD](#freebsd)
* [Solaris](#solaris)

请确保你的系统已成功安装所有必需的依赖项。
常见依赖请参阅[此页面](requirements.md)。

如果都已就绪，我们就可以开始了！
克隆代码库并开始构建 i2pd：

	git clone https://github.com/PurpleI2P/i2pd.git

通用的构建流程如下（使用 cmake）：

	cd i2pd/build
	cmake <cmake options> . # 参见下面“CMake 选项”部分
	make                    # 你可以在命令行添加 VERBOSE=1 以便调试

……或者使用仅 make 的快捷方式：

	cd i2pd/
	make

然后运行测试：

	cd i2pd/tests/
	make

构建成功后，可以安装 i2pd：

	make install

CMake 选项
-------------

可用的 CMake 选项（每个选项形式为 `-D<key>=<value>`，更多信息参见 `man 1 cmake`）：

* `CMAKE_BUILD_TYPE` 构建配置（Debug/Release，默认：无优化也无调试符号）
* `WITH_BINARY`      构建 i2pd 本体（默认：ON）
* `WITH_LIBRARY`     构建 libi2pd（默认：ON）
* `WITH_STATIC`      构建静态版本的库和 i2pd 可执行文件（默认：OFF）
* `WITH_UPNP`        启用 UPnP 支持（需要 libminiupnp，默认：OFF）
* `WITH_AESNI`       启用 AES-NI 支持（默认：ON）
* `WITH_HARDENING`   启用加固特性（仅 gcc，默认：OFF）
* `WITH_MESHNET`     为 cjdns 测试网络构建（使应用无法用于主网，默认：OFF）
* `WITH_ADDRSANITIZER`   启用 Address Sanitizer（默认：OFF）
* `WITH_THREADSANITIZER` 启用 Thread Sanitizer（默认：OFF）

此外，CMake 的 `-L` 标志可用于列出当前缓存的选项：

	cmake -L

Debian/Ubuntu
-------------

你需要编译器和其他工具，可以通过安装 `build-essential`、`debhelper` 和 `cmake` 软件包获得：

	sudo apt-get install build-essential debhelper cmake

此外你还需要一组开发库：

	sudo apt-get install \
	    libboost-date-time-dev \
	    libboost-filesystem-dev \
	    libboost-program-options-dev \
	    libboost-system-dev \
	    libssl-dev \
	    zlib1g-dev

如果需要 UPnP 支持，需要安装 miniupnpc 开发库（别忘了用所需选项重新运行 CMake）：

	sudo apt-get install libminiupnpc-dev

你也可以使用以下方式构建 deb 包：

	sudo apt-get install fakeroot devscripts dh-apparmor
	cd i2pd
	debuild --no-tgz-check -us -uc -b

Arch/Manjaro
-------------
你需要编译器和其他工具，可以通过安装 `base-devel` 软件包获得：
	
	sudo pacman -S base-devel

此外你还需要一组库：
	
	sudo pacman -S boost zlib openssl

Fedora/Centos
-------------

你需要用于构建的编译器和其他工具：

	sudo yum install make cmake gcc gcc-c++

此外你还需要一组开发库：

	sudo yum install boost-devel openssl-devel

如果需要 UPnP 支持，需要安装 miniupnpc 开发库（别忘了用所需选项重新运行 CMake）：

	sudo yum install miniupnpc-devel

 对于静态构建需要 boost-static：

	sudo yum install boost-static

最新的 Fedora 系统默认使用 [DNF](https://en.wikipedia.org/wiki/DNF_(software)) 而不是 YUM，你也可以选择使用 DNF，但 YUM 应该也可以。

Centos 7 的官方仓库中 CMake 版本为 2.8.11，过旧，无法构建 i2pd，需要 CMake ≥ 3.7。
但你可以使用 epel 仓库中的 cmake3：

	yum install epel-release -y
	yum install make cmake3 gcc gcc-c++ miniupnpc-devel boost-devel openssl-devel -y

……然后使用 “cmake3” 替代 “cmake”。

Mac OS X
--------

需要 [homebrew](http://brew.sh)

	brew install boost openssl@1.1

构建：

	make HOMEBREW=1 -j8 # 使用 8 个线程

安装到系统根目录（`/`）：

	sudo make install HOMEBREW=1

安装到 Homebrew 根目录（`/usr/local/`）：

	sudo make install HOMEBREW=1 PREFIX=/usr/local

FreeBSD
-------

对于 10.X 使用 clang。你还需要安装 devel/boost-libs、security/openssl 和 devel/gmake 这几个 port。
输入 gmake，它会调用 Makefile.bsd，如有需要请在其中进行必要更改。

9.X 分支的 gcc 为 v4.2，过旧（不支持 -std=c++11）。

所需的 ports：

* `devel/cmake`
* `devel/boost-libs`
* `lang/gcc47`（或更高版本）

要使用更新的编译器你需要设置以下变量（将“47”替换为你的实际 gcc 版本）：

	export CC=/usr/local/bin/gcc47
	export CXX=/usr/local/bin/g++47

Solaris
-------

对于 OpenIndiana，安装以下软件包：

	pkg install developer/gcc-14
 	pkg install system/library/boost 	
   
然后使用 ‘gmake’