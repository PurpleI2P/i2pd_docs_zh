构建要求
==================

一般来说，要构建 i2pd，你需要以下几样东西：

* 完全支持 C++17 标准的编译器（例如：gcc >= 8、clang）
* boost >= 1.66
* OpenSSL 库 >= 1.1.0
* zlib 库（OpenSSL 已经依赖它）

可选工具：

* cmake >= 2.8（如果你想在 Windows 上使用预编译头文件，则需要 3.3+）
* miniupnp 库（用于 UPnP 支持）