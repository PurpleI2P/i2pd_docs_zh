在 iOS 上构建
===================

如何为 iOS 9 和 iOS 模拟器 386/x64 构建 i2pd

准备工作
--------------

Xcode 7+，cmake 3.2+

依赖项
------------

- 预编译的 openssl
- 预编译的 boost，包含模块 `filesystem`、`program_options`、`date_time` 和 `system`
- 来自 `https://github.com/vovasty/ios-cmake.git` 的 ios-cmake 工具链

构建
--------

假设你的目录结构如下

	lib/
		libboost_date_time.a
		libboost_filesystem.a
		libboost_program_options.a
		libboost_system.a
		libboost.a
		libcrypto.a
		libssl.a
	include/
		boost/
		openssl/
	ios-cmake/
	i2pd/

```bash
mkdir -p build/simulator/lib build/ios/lib include/i2pd

pushd build/simulator && \
cmake   -DIOS_PLATFORM=SIMULATOR \
        -DPATCH=/usr/bin/patch \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_TOOLCHAIN_FILE=../../ios-cmake/toolchain/iOS.cmake \
        -DWITH_STATIC=yes \
        -DWITH_BINARY=no \
        -DBoost_INCLUDE_DIR=../../include \
        -DOPENSSL_INCLUDE_DIR=../../include \
        -DBoost_LIBRARY_DIR=../../lib \
        -DOPENSSL_SSL_LIBRARY=../../lib/libssl.a \
        -DOPENSSL_CRYPTO_LIBRARY=../../lib/libcrypto.a \
        ../../i2pd/build && \
make -j16 VERBOSE=1 && \
popd

pushd build/ios
cmake   -DIOS_PLATFORM=OS \
        -DPATCH=/usr/bin/patch \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_TOOLCHAIN_FILE=../../ios-cmake/toolchain/iOS.cmake \
        -DWITH_STATIC=yes \
        -DWITH_BINARY=no \
        -DBoost_INCLUDE_DIR=../../include \
        -DOPENSSL_INCLUDE_DIR=../../include \
        -DBoost_LIBRARY_DIR=../../lib \
        -DOPENSSL_SSL_LIBRARY=../../lib/libssl.a \
        -DOPENSSL_CRYPTO_LIBRARY=../../lib/libcrypto.a \
        ../../i2pd/build && \
make -j16 VERBOSE=1 && \
popd

libtool -static -o lib/libi2pdclient.a build/*/libi2pdclient.a
libtool -static -o lib/libi2pd.a build/*/libi2pd.a

cp i2pd/*.h include/i2pd
```

集成到项目
--------------------

- 将 `lib` 文件夹中的所有库添加到 `Project linked frameworks`。
- 从系统库中添加 `libc++` 和 `libz` 到 `Project linked frameworks`。
- 将 i2p 头文件的路径添加到你的 `Headers search paths`。

或者，你可以使用 swift 封装 `https://github.com/vovasty/SwiftyI2P.git`