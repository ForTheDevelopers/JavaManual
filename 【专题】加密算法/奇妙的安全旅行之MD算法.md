hi，大家好，今天我们开始介绍消息摘要算法中的MD（Message Digest）算法，MD算法家族包括：MD2，MD4，MD5，MD算法生成的消息摘长度要都是128位的。

其中MD5算法是消息摘要算法的第五个版本，是当前最典型的消息摘要算法，它是由MD2和MD4算法改进而来的。不论是哪一种MD算法，它们都需要获得一个随机长度的信息并产生一个128位长度的信息摘要。如果将这个128位的二进制摘要信息换算成十六进制，就可以得到一个32位长的字符串，所以平时我们见到的大部分MD5算法生成的字符串都是一个32位长的十六进制的字符串。

> 从安全性上说：MD5 > MD4 > MD2，MD5算法安全性最高。

#### MD 算法家族的发展史：

##### MD2 算法：

1989年，著名的非对称算法RSA发明人之一麻省理工学院教授**罗纳德·李维斯特**教授开发了MD2算法。这个算法首先对信息进行数据补位，使信息的字节长度是16的倍数；再以一个16位的检验和做为补充信息追加到原信息的末尾；最后根据这个新产生的信息计算出一个128位的散列值，MD2算法由此诞生。

##### MD4 算法：

1990年，**罗纳德·李维斯特**教授开发出较之MD2算法有着更高安全性的MD4算法。在这个算法中，我们仍需对信息进行数据补位。不同的是，这种补位使其信息的字节长度加上448个字节后成为512的倍数（信息字节长度mod 512 = 448）。此外，关于MD4算的处理和MD2算法有很大的差别，但最终仍旧会获得一个长度为128位的散列值。MD4算法对后续消息摘要算法起到了推动作用， 许多比较有名的消息摘要算法都是在MD4算法的基础上发展而来的，例如MD5、SHA-1、RIPE-MD和HAVAL算法等。

##### MD5 算法：

1991年，继MD4算法后，罗纳德.李维斯特教授开发了MD5算法，最终将MD系列算法推向了成熟。MD5算法经MD2和MD4算法发展而来，算法复杂程度和安全强度也大大提高，但这些MD算法的最终结果都是产生一个128位长度的信息摘要，这也是MD系列算法的主要特点。

#### MD 算法的对比

| 算法 | 摘要长度（bit） | 实现方                            |
| ---- | --------------- | --------------------------------- |
| MD2  | 128             | JDK、Bouncy Castle、Commons Codec |
| MD4  | 128             | Bouncy Castle                     |
| MD5  | 128             | JDK、Bouncy Castle、Commons Codec |

- MD2，MD4，MD5 计算的结果都是是一个128位（即16字节）的散列值，用于确保数据信息的完整一致性。
- MD2 算法加密速度较慢但相对安全，MD4 速度很快，但安全性下降，MD5 则比 MD4 更安全、速度更快。
- MD5 算法被广泛应用于数据完整性校验、数据（消息）摘要、数据加密等。
- 2004年，证实MD5 算法无法防止碰撞攻击，因此不适用于安全性认证，如SSL公开密钥认证或是数字签名等用途。对于需要高度安全性的数据，一般建议改用其他更安全不容易破解的算法，如SHA-2。

#### MD 算法实现

##### JDK 提供的 MD2 和 MD5 算法实现

Java的jdk中提供了MD2和MD5算法的实现，如果想实现MD5，可以直接使用JDk提供的方法来实现，示例代码如下所示：

```java
// MD2加密
public static String encodeMd2(byte[] data) throws Exception {
    // 初始化MessageDigest
    MessageDigest md = MessageDigest.getInstance("MD2");
    // 执行摘要信息
    byte[] digest = md.digest(data);
    // 将摘要信息转换为32位的十六进制字符串
    return new HexBinaryAdapter().marshal(digest);
}

// MD5加密
public static String encodeMd5(byte[] data) throws Exception {
    // 初始化MessageDigest
    MessageDigest md = MessageDigest.getInstance("MD5");
    // 执行摘要信息
    byte[] digest = md.digest(data);
    // 将摘要信息转换为32位的十六进制字符串
    return new HexBinaryAdapter().marshal(digest);
}
```

##### Bouncy Castle 补充的 MD4 算法实现

JDK中不提供对 MD4 算法的实现，Bouncy Castle帮我们实现了，需要引入Bouncy Castle的相关包，示例代码如下所示：

```java
// MD4加密
public static String encodeMd4(byte[] data) throws Exception {
    // 加入BouncyCastle的支持
    Security.addProvider(new BouncyCastleProvider());
    // 初始化MessageDigest
    MessageDigest md = MessageDigest.getInstance("MD4");
    // 执行摘要信息
    byte[] digest = md.digest(data);
    // 将摘要信息转换为32位的十六进制字符串
    return new String(Hex.encode(digest));
}
```

##### Commons Codec 的MD算法实现

Commons Codec 提供了 MD系列算法的消息摘要算法的实现，在使用时可以通过封装的工具类-----DigestUtils类来进行操作。DigestUtils类是对Sun提供的MessageDigest类的一次封装，提供了完整的实现方法。下面是关于MD5实现的示例：

```java
// MD5加密 返回十六进制的字符串
public static String encodeMd5HexWithCodec(byte[] data) throws Exception {
    return DigestUtils.md5Hex(data);
}

// MD5加密,返回十进制的字节数组
public static byte[] encodeMd5WithCodec(byte[] data) {
    return DigestUtils.md5(data);
}
```

> 获取完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### 应用场景

由于MD5算法具有简单快捷等特性，在实际应用中还是有很多场景中会使用，下面列举一些我们常见的场景。

##### 1、用户密码

MD5算法可以用于用户注册登录时的密码验证，不过为了增加MD5被破解的难度，可以进行加盐或乱序处理，也可以只取一半MD5的长度或者其他策略。MD5是不可逆算法，只要保证算法不变，就能和数据库中的md5相匹配。不过MD5算法不是完全安全的，还是可能被破解的，可以作为一个低安全级别的应用场景。

##### 2、文件完整性的校验

当我们在下载一个文件时，通常会在下载文件页面中显示这个文件的MD5值，在本地下载完成后可以对下载的文件进行MD5计算，并将两个MD5值进行比较，如果一致则说明文件完整没有被篡改，说明是网站的源文件。这种可以有效防止文件被黑客利用进行病毒的植入，一旦文件被篡改，MD5值就不一样了。例如，我们在Oracle官方下载jdk时通常会看到这个页面：

![md5](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7343a5bee3142ef8c980655b53d708b~tplv-k3u1fbpfcp-watermark.image)

##### 3、搜索

以某网站内容搜索为例，可以使用搜索关键词的MD5值来设置索引，例如你输入**我是开发者FTD 公众号** 和 输入**公众号 我是开发者FTD **搜索的结果是不一样的，因为这两个关键词对应的MD5值是不一样的，我们可以根据这个MD5值去定义唯一的搜索关键词。

##### 4、著作版权

当一个视频或者音频创作出来的时候它的MD5值是唯一的，若以后有翻录等版本出来，即使画质，声音，文件名都一样，但是他们的MD5值验证是不一样的，所以可以验证版权。还比如用某一账户下载的视频它的账户信息也回和视频一起MD5值操作，如果以后这个人私自传播通过MD5值就可以去数据库中查找泄露版权的情况。

##### 5、订单信息的校验

现在支付工具对大家都比较熟悉了，你有没有想过如果在支付过程中，黑客将订单拦截并修改了支付金额，是不是就可以免费或很便宜的买东西了呢？这时服务器端就可以使用MD5来校验订单的真实性，看看收到的订单是不是最初用户下单时的订单，如果发现订单被修改了，则支付失败，有效的预防了商家的损失。

#### MD5算法的不足

上面说到了MD5算法抗碰撞性的特点，因此在实际应用中MD5被认为是非常安全的，但是在2004年，中国山东大学的**王小云教授以及她的同事**在美国加州举办的密码学会议上宣布**破解了MD5算法，其实也不是真正的破解，而是非常明显的加快了寻找一对碰撞的速度**，利用她们的技术，可以在**几个小时**内就找到一对碰撞，极端情况下，甚至在**2008年计算机的计算能力**上，**几秒钟**就可以找到一对碰撞。

**因此MD5算法的安全性就引起人们的担忧，所以在对安全性要求较高的场合，不建议直接使用MD5算法。**

#### 总结

虽然MD5算法现在看来并不是绝对安全的，但是MD5算法的简单快速便捷性也是其他算法无法比拟的，MD5算法还是有很多场景在使用，大家也可以根据自己实际业务的需求来选择是否使用MD5算法。

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***