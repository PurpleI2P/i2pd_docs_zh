家族配置
====================

你可能希望为你的路由器指定所属的家族。
有两种方式可以做到这一点：创建一个新家族，或加入一个现有家族。

新建家族
-----------

要创建新的家族，必须先生成家族的自签名证书和密钥。  
唯一支持的密钥类型是 prime256v1。
可使用以下 openssl 命令生成：  

    openssl ecparam -name prime256v1 -genkey -out <your family name>.key  
    openssl req -new -key <your family name>.key -out <your family name>.csr  
    touch v3.ext
    openssl x509 -req -days 3650 -in <your family name>.csr -signkey <your family name>.key -out <your family name>.crt -extfile v3.ext  

（your family name 为你的家族名称）

在软件提示时，将 CN（Common Name）填写为 &lt;your family name>.family.i2p.net。

生成完成后，将 &lt;your-family-name>.key 和 &lt;your-family-name>.crt 放到 &lt;ip2d data>/family 目录（例如 ~/.i2pd/family）。
你应将这两个文件提供给加入你家族的其他成员。
如果你想注册你的家族并让 I2P 网络认识它，请把你的 .crt 文件向 contrib/certificate/family 提交一个 Pull Request。
以这种方式添加到公共仓库的证书将会出现在 i2pd 和 I2P 的后续发行软件包中。不要提交 .key 文件，它只应在你的家族成员之间共享。

如何加入现有家族
---------------------------

一旦你与该家族达成一致，他们必须把 .key 和 .crt 文件提供给你，你需要将其放到 <i2pd datadir>/certificates/family/ 目录中。

发布你的家族
-------------------

使用参数 'family=&lt;your-family-name>' 运行 i2pd，并确保在你的 'family' 目录中有 &lt;your-family-name>.key 和 &lt;your-family-name>.crt。
如果一切设置正确，你的 router.info 将包含两个新字段：'family' 和 'family.sig'。
否则，你的路由器在启动时会输出以 "Family:" 为前缀、严重级别为 'warn' 或 'error' 的日志提示。
    
从 i2pd 导出到 Java-I2P
------------------
1. **将私钥文件转换为 PKCS#8**
    
当前私钥为 openssl 的“EC 参数文件”格式：
```
-----BEGIN EC PARAMETERS-----
(base64)
-----END EC PARAMETERS-----
-----BEGIN EC PRIVATE KEY-----
(base64)
-----END EC PRIVATE KEY-----
```
首先必须将其转换为 PKCS#8 格式。
```
openssl pkcs8 -topk8 -nocrypt -in your-family-name.key -out your-family-name.pkcs8
```
现在，在 your-family-name.pkcs8 文件中是一个 PKCS#8 私钥： 
```
-----BEGIN PRIVATE KEY-----
(base64)
-----END PRIVATE KEY-----
```
2. **合并 PKCS#8 和证书文件**
    
现在将 pkcs8 和证书文件合并为一个文件： 
```
cat your-family-name.pkcs8 your-family-name.crt > your-family-name.secret
```
3. **导入合并后的文件**
    
现在访问 Java I2P 控制台 http://127.0.0.1:7657/configfamily 页面，在“Join Existing Router Family”处选择文件 your-family-name.secret 以加入该家族。

（[来源](http://zzz.i2p/topics/3313)）

从 Java-I2P 导出到 i2pd
----------------------------

前往 Java I2P 控制台 http://127.0.0.1:7657/configfamily 页面并导出家族密钥。你将得到一个文件 `family-your-family-name-secret.crt`。它同时包含私钥和公钥证书。

将其复制两份，名称分别为 `your-family-name.key` 和 `your-family-name.crt`。
    
用文本编辑器编辑 `your-family-name.key`，删除证书部分，使其只包含私钥部分。
    
用文本编辑器编辑 `your-family-name.crt`，删除私钥部分，使其只包含证书部分。

按照[此处](https://i2pd.readthedocs.io/en/latest/user-guide/family/)的说明，将 `your-family-name.key` 和 `your-family-name.crt` 文件移动到 i2pd 的 /certificates/family/ 目录。 
    
这假定 i2pd/openssl 能处理 PKCS#8 格式的私钥。 

（[来源](http://zzz.i2p/topics/3313)）

------------------------

待办：列出常见错误