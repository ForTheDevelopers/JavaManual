hi，大家好，今天我们开始介绍消息摘要算法中的SHA（Secure Hash Algorithm）安全散列算法。由于其他曾被广泛使用的Hash算法，比如上一篇文章提到的MD5，后来都被发现存在一定的安全隐患，新的摘要算法算法就出现了。SHA 算法是由美国国家安全局（NSA）所设计，并由美国国家标准与技术研究院（NIST）于1993年发布，是美国的政府标准。从2005年至今，SHA或许已经成为仅存的Hash算法的标准了。

SHA家族的五个算法，分别是SHA-1、SHA-224、SHA-256、SHA-384 和 SHA-512。

#### SHA 算法家族的发展史

##### SHA-0

最初载明的算法于1993年发布，称做安全杂凑标准（Secure Hash Standard），FIPS PUB 180。这个版本常被称为SHA-0。由于很快被发现存在安全隐患，它在发布之后很快就被NSA撤回，并且由1995年发布的修订版本FIPS PUB 180-1（通常称为SHA-1）取代。

##### SHA-1

SHA-1算法和 MD5 算法都是由 MD4 算法导出，因此他们俩的特点、缺陷、应用场景基本是相同的。

它俩的区别在于SHA-1算法在长度上是40位十六进制，即160位的二进制；而MD5算法是32位的十六进制，即128位的二进制，所以2的160次是远远超过2的128次这个数量级的，所以 SHA-1 算法相对来说要比 MD5 算法更安全一些。

##### SHA-2

2002年，NIST分别发布了SHA-256、SHA-384、SHA-512，这些算法统称为SHA-2，2008年又新增了SHA-224。

由于SHA-1已经不太安全，目前SHA-2各版本已成为主流。SHA-2是一系列SHA算法变体的总称，其中包含如下子版本：

- SHA-256：可以生成长度256bit的信息摘要。

- SHA-224：SHA-256的“阉割版”，可以生成长度224bit的信息摘要。

- SHA-512：可以生成长度512bit的信息摘要。

- SHA-384：SHA-512的“阉割版”，可以生成长度384bit的信息摘要。

##### SHA-3

2012年10月，经过多年的测试和分析，美国NIST选择了Keccak算法作为SHA - 3的标准算法，Keccak拥有良好的加密性能以及抗解密能力。**SHA3算法是以太坊的基础加密算法**。

Keccak算法（读作为“ket-chak”）是Guido Bertoni, Joan Daemen, Michael Peters, and Giles Van Assche的工作。 SHA-3的候选人在2008年10月提交。

Keccak采用了创新的的“海绵引擎”散列消息文本。它是快速的，在英特尔酷睿2处理器下的平均速度为12.5周期每字节。它设计简单，方便硬件实现。

#### SHA 算法基本原理

前面我们简单的介绍了SHA算法家族，接下来我们以SHA-1为例来分析其基本原理。SHA-1是一种数据加密算法，该算法的思想是接收一段明文，然后以一种不可逆的方式将它转换成一段密文，也可以简单的理解为输入一串二进制码，并把它们转化为长度较短、位数固定的输出序列即散列值，也称为信息摘要或信息认证代码的过程。

SHA-1算法输入报文的最大长度不超过264位，产生的输出是一个160位的报文摘要。输入是按512 位的分组进行处理的。SHA-1是不可逆的、防冲突，并具有良好的雪崩效应。

一般来说SHA-1算法包括有如下的处理过程：

##### （1）对输入信息进行处理

既然SHA-1算法是对给定的信息进行处理得到相应的摘要，那么首先需要按算法的要求对信息进行处理。那么如何处理呢？对输入的信息按512位进行分组并进行填充。如何填充信息报文呢？其实即使填充报文后使其按512进行分组后，最后正好余448位。那填充什么内容呢？就是先在报文后面加一个1，再加很多个0，直到长度满足对512取模结果为448。到这里可能有人会奇怪，为什么非得是448呢？这是因为在最后会附加上一个64位的报文长度信息，而448+64正好是512。

##### （2）填充长度信息

前面已经说过了，最后会补充信息报文使其按512位分组后余448位，剩下的64位就是用来填写报文的长度信息的。至次可能大家也明白了前面说过的报文长度不能超过264位了。填充长度值时要注意必须是低位字节优先。

##### （3）信息分组处理

经过添加位数处理的明文，其长度正好为512位的整数倍，然后按512位的长度进行分组，可以得到一定数量的明文分组，我们用Y0，Y1，……YN-1表示这些明文分组。对于每一个明文分组，都要重复反复的处理，这些与MD5都是相同的。

##### （4）初始化缓存

所谓初始化缓存就是为链接变量赋初值。前面我们实现MD5算法时，说过由于摘要是128位，以32位为计算单位，所以需要4个链接变量。同样SHA-1采用160位的信息摘要，也以32位为计算长度，就需要5个链接变量。我们记为A、B、C、D、E。其初始赋值分别为：A = 0x67452301、B = 0xEFCDAB89、Ｃ = 0x98BADCFE、Ｄ = 0x10325476、Ｅ = 0xC3D2E1F0。

如果我们对比前面说过的MD5算法就会发现，前４个链接变量的初始值是一样的，因为它们本来就是同源的。

##### （5）计算信息摘要

经过前面的准备，接下来就是计算信息摘要了。SHA1有4轮运算，每一轮包括20个步骤，一共80步，最终产生160位的信息摘要，这160位的摘要存放在5个32位的链接变量中。

#### SHA 算法的对比

不同SHA 算法的数据比较如下表，其中的长度单位均为位：

| 类别         | SHA-1 | SHA-224 | SHA-256 | SHA-384 | SHA-512 |
| ------------ | ----- | ------- | ------- | ------- | ------- |
| 消息摘要长度 | 160   | 224     | 256     | 384     | 512     |
| 消息长度     | <2^64 | <2^64   | <2^64   | <2^128  | <2^128  |
| 分组长度     | 512   | 512     | 512     | 1024    | 1024    |
| 计算字步长   | 32    | 32      | 32      | 64      | 64      |
| 计算步骤数   | 80    | 64      | 64      | 80      | 80      |

从上表中我们不难发现，SHA-224和SHA-256、SHA-384和SHA-512在消息长度、分组长度、计算字长以及计算步骤各方面分别都是一致的。通常认为SHA-224是SHA-256的缩减版，而SHA-384是SHA-512的缩减版。

#### SHA 算法实现

| 算法    | 摘要长度（bit） | 实现方                            |
| ------- | --------------- | --------------------------------- |
| SHA-1   | 160             | JDK、Bouncy Castle、Commons Codec |
| SHA-224 | 224             | JDK、Bouncy Castle                |
| SHA-256 | 256             | JDK、Bouncy Castle、Commons Codec |
| SHA-384 | 384             | JDK、Bouncy Castle、Commons Codec |
| SHA-512 | 512             | JDK、Bouncy Castle、Commons Codec |

##### JDK 的 SHA 算法实现

从上面的介绍可以知道，SHA分为多种算法，上述算法在JDK均有实现，以下实现为通用实现方法，例如，当algorithm传入对应的算法名称就可以获得对应的算法实例，比如“SHA-256”：

```java
// SHA 加密
public static String encodeSha(String data, String algorithm) throws Exception {
    MessageDigest sha = MessageDigest.getInstance(algorithm);
    byte[] srcBytes = data.getBytes();
    // 使用srcBytes更新摘要
    sha.update(srcBytes);
    // 完成哈希计算，得到result
    byte[] resultBytes = sha.digest();
    // 返回十六进制字符串
    return new HexBinaryAdapter().marshal(resultBytes);
}
```

##### Bouncy Castle 的 SHA 算法实现

第三方加密组件包Bouncy Castle是对JDK的补充，同样也实现了所有的SHA算法，以下为算法的通用实现：

```java
public static String encodeSha(String data, String algorithm) throws NoSuchAlgorithmException {
    // 加入BouncyCastleProvider支持
    Security.addProvider(new BouncyCastleProvider());
    // 初始化MessageDigest
    MessageDigest sha = MessageDigest.getInstance(algorithm);
    // 获取消息摘要
    byte[] bytes = sha.digest(data.getBytes());
    // 返回十六进制字符串
    return Hex.toHexString(bytes);
}
```

##### Commons Codec 的 SHA 算法实现

Commons Codec 提供了 SHA 系列算法的消息摘要算法的实现，在使用时可以通过封装的工具类-----DigestUtils类来进行操作。DigestUtils类是对Sun提供的MessageDigest类的一次封装，提供了完整的实现方法。下面是以SHA-256算法实现的示例：

```java
public static String encodeSha1Hex(String data) {
    return DigestUtils.sha1Hex(data);
}

public static String encodeSha256Hex(String data) {
    return DigestUtils.sha256Hex(data);
}

public static String encodeSha384Hex(String data) {
    return DigestUtils.sha384Hex(data);
}

public static String encodeSha512Hex(String data) {
    return DigestUtils.sha512Hex(data);
}
```

> 查看完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### 应用场景

##### 文件校验

很多软件，尤其是安全性要求较高的软件，会在官网上公布软件的SHA值，用户下载软件后，可以自行计算软件SHA值，然后与官网公布的SHA值进行比较，确认软件是否被篡改过，和前面介绍的MD5的作用是一样的。同样的，Oracle官网也提供了两个版本的摘要值，分别是MD5和SHA-256，无论使用那种算法，都可以校验文件的完整性。

##### 基于口令的加密

SHA也被用于基于口令的加密（Password Based Encryption，PBE），PBE的原理是将口令和盐（salt）混合后计算其SHA值，然后将这个散列值用作加密的秘钥。PBE可以防御针对口令的字典攻击。

##### 消息认证码

消息认证码是将“发送者和接收者之间的共享秘钥”和“消息”进行混合后计算出的SHA。使用消息认证码可以检测并防止通信过程中的错误、篡改以及伪装。

##### 数字签名

数字签名是现实社会中的签名和盖章这样的行为在数字世界中的实现。数字签名的处理过程非常耗时，因此一般不会对整个消息内容施加数字签名，而是先取消息内容的SHA值，对SHA值进行数字签名。

##### 安全协议

SHA算法在许多安全协议中广为使用，包括TSL/SSL、PGP、SSH、S/MIME和IPsec，同时在TSL/SSL安全协议数字证书中，也有SHA的影子，例如证书指纹一般都是通过SHA-1来实现的。

#### 总结

SHA算法在当前生产应用中比较常见的，其中SHA-256算法的安全性是被世界各国密码学家所广泛承认的，另外也欢迎大家关注我们的公众号，回复【加解密】获取本系列的全部源码。

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***

