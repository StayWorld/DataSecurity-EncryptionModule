## DataSecurity-Encryption

相信很多开发者跟我当初一样，只是根据项目经理或者文档指示进行加密，简单会使用 但并不知道加密知识体系和内在原理，于是整理了一份相关资料，方法都以封装成类可以直接拿走调用。

本文为「简书-白开水ln」作者原创；我的写作，希望能简化到初学者尽快入门和老司机繁琐回顾 ^_^.

在「时间 & 知识 」有限内，总结的文章难免有「未全、不足 」的地方，还望各位好友指出，以提高文章质量。

## encryption【加密介绍】

数据安全的原则
- 在网络上`不允许`传输用户隐私数据的`明文`,（即:App网络传输安全，指对数据从客户端传输到Server中间过程的加密，防止网络世界当中其他节点对数据的窃听）。

- 在本地`不允许`保存用户隐私数据的`明文`,（即:App数据存储安全，主要指在磁盘做数据持久化的时候所做的加密）。

- App代码安全,（即:包括代码混淆，加密或者app加壳）。

### 常用加密算法

|常用加密算法 | 名称 |
|:-: |:-:|
| 编码方案 | Base64 |
| 哈希(散列)函数 | MD5 |
|   | SHA1 |
|   | SHA256 |
| 对称加密算法 | AES |
|  | DES |
| 非对称加密算法 | RSA |
| HTTPS | HTTP+SSL加密协议 |

### 常用加密方式

- 1、通过简单 `BASE64`编码 防止数据明文传输。

- 2、对普通请求、返回数据，生成`MD5`校验（MD5中加入动态密钥），进行数据完整性（简单防篡改，安全性较低，优点：快速）校验。

- 3、对于重要数据，使用`RSA`进行数字签名，起到防篡改作。

- 4、对于比较敏感的数据，如用户信息（登陆、注册等），客户端发送使用`RSA`加密，服务器返回使用`DES(AES)`加密。

- 5、要想非常安全的传输数据，建议使用`https`。抓包不可以，但是中间人攻击则有可能。建议双向验证防止中间人攻击。



### MAC上生成公钥、私钥的方法，及使用

- 1.打开终端，切换到自己想输出的文件夹下

- 2.输入指令，`openssl`（`openssl`是生成各种秘钥的工具，`mac`已经嵌入)

- 3.输入指令，`genrsa -out rsa_private_key.pem 1024` (生成私钥，java端使用的)

- 4.输入指令，`rsa -in rsa_private_key.pem -out rsa_public_key.pem -pubout` (生成公钥)

- 5.输入指令，`pkcs8 -topk8 -in rsa_private_key.pem -out pkcs8_rsa_private_key.pem -nocrypt`(私钥转格式，在`ios`端使用私钥解密时用这个私钥)

**注意**: 在MAC上生成三个`.pem`格式的文件，一个公钥，两个私钥，都可以在终端通过指令`vim xxx.pem` 打开，里面是字符串，第三步生成的私钥是`java`端用来解密数据的，第五步转换格式的私钥iOS端可以用来调试公钥、私钥解密（因为私钥不留在客户端）  

`iOS`端公钥加密私钥解密、`java`端公钥加密私钥解密，`java`端私钥加密公钥解密都容易做到，`iOS`不能私钥加密公钥解密，只能用于验签


## Getting Started【开始使用】

**ios-->Base64编码、解码，方法你拿走**
```objc
1、导入#import "NSString+Hash.h"

2、对字符串进行base64编码,并且返回
- (NSString *)base64EncodeString;

3、对base64编码之后的字符串解码,并且返回
-(NSString *)base64DecodeString;
```

**ios-->Md5加密，方法你拿走**
```objc
1、导入#import "NSString+Hash.h"

2、散列函数--md5对字符串加密
- (NSString *)md5String;

散列函数--HMAC md5加密
- (NSString *)hmacMD5StringWithKey:(NSString *)key;

散列函数--md5对文件加密
- (NSString *)fileMD5Hash;
```

**ios-->DES加密、解密，方法你拿走**
```objc
1、导入#import "DES3Util.h"

2、DES加密字符串并返回base64编码字符串
+(NSString *) encryptUseDES:(NSString *)string keyString:(NSString *)keyString;

3、DES解密字符串
+(NSString *)decryptUseDES:(NSString *)base64EncodedString keyString:(NSString *)keyString;
```

**ios-->AES加密、解密，方法你拿走**
```objc
1、导入#import "AES.h"

2、AES加密字符串并返回base64编码字符串
+ (NSString *)encrypt:(NSString *)string keyString:(NSString *)keyString;

3、AES解密字符串
+ (NSString *)decrypt:(NSString *)base64EncodedString keyString:(NSString *)keyString;
```

**ios-->RSA加密、解密，方法你拿走**
```objc
1、导入#import "RSAUtil.h"

2、公钥加密类方法：
+ (NSString *)encryptString:(NSString *)str publicKey:(NSString *)pubKey;
+ (NSData *)encryptData:(NSData *)data publicKey:(NSString *)pubKey;

3、私钥解密类方法
+ (NSString *)decryptString:(NSString *)str privateKey:(NSString *)privKey;
+ (NSData *)decryptData:(NSData *)data privateKey:(NSString *)privKey;
```

## 效果图
![iOS数据安全相关.gif](http://upload-images.jianshu.io/upload_images/2230763-5ff2f3b6e380f7f7.gif?imageMogr2/auto-orient/strip)


对于数据安全有太多的东西值得我们去学习推敲，方方面面都透露着数据安全的重要性 和 我们学习的必要性。

[附上总结写的小样 Demo，重要的部分代码中都有相应的注解和文字打印，运行程序可以很直观的表现]()  



## 期待

- 如果在阅读过程中遇到 error，希望你能 Issues 我，谢谢。

- 如果你想为【本文相关】分享【解决新的思想】，也希望你能 Issues 我，我非常想为这篇文章增加更多实用的内容，谢谢。

- [博客原文](https://custompbwaters.github.io/2016/07/15/iOS「UI%20+%20NET」/iOS数据安全—加密解密/)、[简书原文](http://www.jianshu.com/p/1a7d57b60778)，对本文我会【不定时、持续更新、一些 学习心得与文章、实用才是硬道理】^_^.





## About me

【我也是对所花费时间的一个总结】

我只是个【有思想的伐码猿🐒】加上【自己的学习总结☕️】写出来的文章。


