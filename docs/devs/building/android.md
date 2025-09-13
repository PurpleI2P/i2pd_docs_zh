在 Android 上构建
===================

### 注意：构建仅在 Linux 系统上工作。当前文档展示如何在 Ubuntu 18.04 上构建应用

准备工作
--------------

你需要安装用于构建库所需的 `rename`、`OpenJDK` 11+、`gradle` 5.1+ 以及 `Android SDK`。

```
sudo apt-get install g++ rename openjdk-11-jdk gradle
```

- Android [SDK](https://developer.android.com/studio#downloads)（仅需选择命令行工具）
- 如果系统提供的 gradle 版本低于 5.1，请从 [Gradle](https://gradle.org/install/) 主页下载

依赖
------------

准备 Android SDK 并安装所需组件

```bash
mkdir /tmp/android-sdk
cd /tmp/android-sdk
wget <latest SDK link>
unzip commandlinetools-linux-*_latest.zip
# install required tools
./cmdline-tools/bin/sdkmanager --sdk_root=/opt/android-sdk "build-tools;31.0.0" "cmake;3.18.1" "ndk;21.4.7075529"
```

克隆包含子模块的仓库
```bash
git clone --recurse-submodules https://github.com/PurpleI2P/i2pd-android.git
```

编译所需库

```bash
export ANDROID_SDK_ROOT=/opt/android-sdk
export ANDROID_NDK_HOME=$ANDROID_SDK_ROOT/ndk/21.4.7075529

pushd app/jni
./build_boost.sh
./build_openssl.sh
./build_miniupnpc.sh
popd
```

构建 Android 应用
--------

### 构建应用

- 创建包含 SDK 与 NDK 路径的 `local.properties` 文件

  ```
  sdk.dir=/opt/android-sdk
  ndk.dir=/opt/android-sdk/ndk/21.4.7075529
  ```

- 运行 `gradle clean assembleDebug`
- 你将在 `app/build/outputs/apk` 目录下找到 .apk 文件

### 创建发布版 .apk

要创建发布版 .apk，你必须获得一个 Java keystore 文件（.jks）。你可以使用已有的，或使用 keytool 自行生成，或基于你已有的某个知名证书生成。
例如，i2pd 的发布版是用这个[证书](https://raw.githubusercontent.com/PurpleI2P/i2pd/9000b3df4edcbe7f2c8afd0e1e30609746311ace/contrib/certificates/router/orignal_at_mail.i2p.crt)签名的。

修改文件 `app\build.gradle`，用你自己的值替换预设值

```
release {
    storeFile file("i2pdapk.jks")
    storePassword "android"
    keyAlias "i2pdapk"
    keyPassword "android"
}
```

运行 `gradle clean assembleRelease`

构建可执行二进制
------------------------------

- 设置环境变量：

  ```
  export ANDROID_SDK_ROOT=/opt/android-sdk
  export ANDROID_NDK_HOME=$ANDROID_SDK_ROOT/ndk/21.4.7075529
  ```

- 在 `binary/jni` 目录运行 `$ANDROID_NDK_HOME/ndk-build -j <threads> NDK_MODULE_PATH=$PWD`
- 你将在 `binary/libs/<architecture>` 目录下找到 `i2pd` 可执行文件