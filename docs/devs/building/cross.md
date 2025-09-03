交叉编译说明
=======================

在 Ubuntu 15.10（Wily Werewolf）上构建静态 64 位 Windows 可执行文件
------------------------------------------------------------

安装交叉编译器及相关工具

	sudo apt-get install g++-mingw-w64-x86-64

默认使用 Win32 线程模型，它缺少 std::mutex 等。因此我们要更改默认设置

	sudo update-alternatives --set x86_64-w64-mingw32-g++ /usr/bin/x86_64-w64-mingw32-g++-posix

从现在起，我们假定所有内容都在 `~/dev/`。获取 Boost 源码并解压到 `~/dev/boost_1_60_0/`，并切换到该目录。
现在添加我们的交叉编译器配置。警告：下面的操作会清空你之前在该文件中的内容。

	echo "using gcc : mingw : x86_64-w64-mingw32-g++ ;" > ~/user-config.jam

按常规方式构建 Boost，但我们定义一个专用的 staging 目录

	./bootstrap.sh
	./b2 toolset=gcc-mingw target-os=windows variant=release link=static runtime-link=static address-model=64 \
	  --build-type=minimal --with-filesystem --with-program_options --with-date_time \
	  --stagedir=stage-mingw-64
	cd ..

现在获取并构建 OpenSSL

	git clone https://github.com/openssl/openssl
	cd openssl
	git checkout OpenSSL_1_0_2g
	./Configure mingw64 no-rc2 no-rc4 no-rc5 no-idea no-bf no-cast no-whirlpool no-md2 no-md4 no-ripemd no-mdc2 \
	  no-camellia no-seed no-comp no-krb5 no-gmp no-rfc3779 no-ec2m no-ssl2 no-jpake no-srp no-sctp no-srtp \
	  --prefix=~/dev/stage --cross-compile-prefix=x86_64-w64-mingw32-
	make depend
	make
	make install
	cd ..

……以及 zlib

	git clone https://github.com/madler/zlib
	cd zlib
	git checkout v1.2.8
	CC=x86_64-w64-mingw32-gcc CFLAGS=-O3 ./configure --static --64 --prefix=~/dev/stage
	make
	make install
	cd ..

现在为 CMake 准备交叉工具链提示文件，命名为 `~/dev/toolchain-mingw.cmake`

	set(CMAKE_SYSTEM_NAME Windows)
	set(CMAKE_C_COMPILER x86_64-w64-mingw32-gcc)
	set(CMAKE_CXX_COMPILER x86_64-w64-mingw32-g++)
	set(CMAKE_RC_COMPILER x86_64-w64-mingw32-windres)
	set(CMAKE_FIND_ROOT_PATH /usr/x86_64-w64-mingw32)
	set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)

下载 miniupnpc，解压并将其符号链接至 `~/dev/miniupnpc/`。
最后，我们可以利用以上成果构建 i2pd

	git clone https://github.com/PurpleI2P/i2pd
	mkdir i2pd-mingw-64-build
	cd i2pd-mingw-64-build
	BOOST_ROOT=~/dev/boost_1_60_0 cmake -G 'Unix Makefiles' ~/dev/i2pd/build -DBUILD_TYPE=Release \
	  -DCMAKE_TOOLCHAIN_FILE=~/dev/toolchain-mingw.cmake -DWITH_AESNI=ON -DWITH_UPNP=ON -DWITH_STATIC=ON \
	  -DWITH_HARDENING=ON -DCMAKE_INSTALL_PREFIX:PATH=~/dev/i2pd-mingw-64-static \
	  -DZLIB_ROOT=~/dev/stage -DBOOST_LIBRARYDIR:PATH=~/dev/boost_1_60_0/stage-mingw-64/lib \
	  -DOPENSSL_ROOT_DIR:PATH=~/dev/stage
	make
	x86_64-w64-mingw32-strip i2pd.exe

至此，你应该得到一个去除符号的发布版本构建。