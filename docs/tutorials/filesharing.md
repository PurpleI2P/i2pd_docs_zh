匿名文件共享
=====================

**支持I2P的BitTorrent软件**

- [qBittorrent](http://qbittorrent.org/)（qBittorrent实验性I2P支持）。qBittorrent使用SAM协议，可在混合模式或仅I2P模式下使用。目前为qBittorrent配置I2P支持不够用户友好，且没有官方文档，因此需结合[此](http://www.i2pforum.net/viewtopic.php?t=1224)和[此](https://strict3443.codeberg.page/i2p-info/hugo/public/posts/how-to-use-i2p-on-qbittorrent-nox/)指南进行设置。请注意，这是实验性功能，需使用qBittorrent v4.6.0或更高版本，可能会出现bug。*小心使用并注意设置。该软件同时支持I2P和明网，可能会意外泄露你的地址。*

- [I2PSnark独立版](https://gitlab.com/i2pplus/I2P.Plus/-/jobs/artifacts/master/raw/i2psnark-standalone.zip?job=Java8)（由[I2P+项目](https://i2pplus.github.io/)提供的独立构建）。I2PSnark使用I2CP协议，运行i2pd时需设置参数`--i2cp.enabled=true`或在配置文件中进行类似设置。

- [Vuze](https://en.wikipedia.org/wiki/Vuze)和[BiglyBT](https://www.biglybt.com)（Vuze的分支）。Vuze和BiglyBT使用I2CP协议，运行i2pd时需设置参数`--i2cp.enabled=true`或在配置文件中进行类似设置。*小心使用并注意设置。该软件同时支持I2P和明网，可能会意外泄露你的地址。*

- [XD](https://github.com/majestrate/XD)。*警告：XD使用SAM API，当前可能会导致i2pd崩溃。*

- [Robert](http://en.wikipedia.org/wiki/Robert_%28P2P_Software%29)。Robert使用BOB协议，运行i2pd时需设置参数`--bob.enabled=true`或在配置文件中进行类似设置。

另请访问[postman tracker](http://tracker2.postman.i2p)。

*警告：如果不先停止使用SAM API的应用程序就尝试停止i2pd，SAM API当前可能会导致i2pd崩溃。*