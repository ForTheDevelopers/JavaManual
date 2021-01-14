hi，大家好，今天我们开始介绍消息摘要算法中的HMAC（Keyed-Hashing for Message Authentication）消息认证码算法，MAC（Message Authentication Code，消息认证码算法）是含有密钥散列函数算法，兼容了MD和SHA算法的特性，并在此基础上加上了密钥，因此MAC算法也经常被称作 **HMAC** 算法。

#### MAC

在开始之前，我们先说下MAC算法。在现代的网络中，身份认证是一个经常会用到的功能，在身份认证过程中，有很多种方式可以保证用户信息的安全，而 MAC(message authentication code) 就是一种常用的方法。

消息认证码是对消息进行认证并确认其完整性的技术。通过使用发送者和接收者之间共享的密钥，就可以识别出是否存在伪装和篡改行为。

MAC 是通过【MAC 算法 + 密钥 + 要加密的信息】三个要素一起计算得出的。

同hash算法（消息摘要）相比，消息摘要只能保证消息的完整性，即该消息摘要B是这个消息A生成的。而MAC算法能够保证消息的正确性，即判断确实发的是消息A而不是消息C。

同公私钥体系相比，因为MAC的密钥在发送方和接收方是一样的，所以发送方和接收方都可以来生成MAC，而公私钥体系因为将公钥和私钥分开，所以增加了不可抵赖性。

MAC有很多实现方式，比较通用的是基于hash算法的MAC，比如今天我们要讲的HMAC算法。还有一种是基于分组密码的实现，比如OMAC，CBC-MAC and PMAC等。

#### HMAC

HMAC 算法首先它是基于信息摘要算法的。目前主要集合了MD和SHA两大系列消息摘要算法。其中MD系列的算法有HmacMD2、HmacMD4、HmacMD5三种算法；SHA系列的算法有HmacSHA1、HmacSHA224、HmacSHA256、HmacSHA384、HmacSHA512五种算法。

HMAC 算法除了需要信息摘要算法外，还需要一个密钥。HMAC的密钥可以是任何长度，如果密钥的长度超过了摘要算法信息分组的长度，则首先使用摘要算法计算密钥的摘要作为新的密钥。一般不建议使用太短的密钥，因为密钥的长度与安全强度是相关的。通常选取密钥长度不小于所选用摘要算法输出的信息摘要的长度。

#### MD 算法的对比

| 算法       | 摘要长度（bit） | 实现方                            |
| ---------- | --------------- | --------------------------------- |
| HmacMD5    | 128             | JDK、Bouncy Castle、Commons Codec |
| HmacSHA1   | 160             | JDK、Bouncy Castle、Commons Codec |
| HmacSHA224 | 224             | JDK、Bouncy Castle、Commons Codec |
| HmacSHA256 | 256             | JDK、Bouncy Castle、Commons Codec |
| HmacSHA384 | 384             | JDK、Bouncy Castle、Commons Codec |
| HmacSHA512 | 512             | JDK、Bouncy Castle、Commons Codec |

#### HMAC 算法实现

##### JDK 的 HMAC 算法实现

JDK 关于HMAC算法的实现：

```java
// 获取 HMAC Key
public static byte[] getHmacKey(String algorithm) {
    try {
        // 1、创建密钥生成器
        KeyGenerator keyGenerator = KeyGenerator.getInstance(algorithm);
        // 2、产生密钥
        SecretKey secretKey = keyGenerator.generateKey();
        // 3、获取密钥
        byte[] key = secretKey.getEncoded();
        // 4、返回密钥
        return key;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}

// HMAC 加密
public static String encryptHmac(byte[] data, byte[] key, String algorithm) {
    try {
        // 1、还原密钥
        SecretKey secretKey = new SecretKeySpec(key, algorithm);
        // 2、创建MAC对象
        Mac mac = Mac.getInstance(algorithm);
        // 3、设置密钥
        mac.init(secretKey);
        // 4、数据加密
        byte[] bytes = mac.doFinal(data);
        // 5、生成数据
        String rs = encodeHex(bytes);
        // 6、返回十六进制加密数据
        return rs;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

##### Bouncy Castle 的HMAC 算法实现

Bouncy Castle 关于HMAC算法的实现：

```java
// 获取 HMAC Key
public static byte[] getHmacKey(String algorithm) {
    try {
        // 1、创建密钥生成器
        KeyGenerator keyGenerator = KeyGenerator.getInstance(algorithm);
        // 2、产生密钥
        SecretKey secretKey = keyGenerator.generateKey();
        // 3、获取密钥
        byte[] key = secretKey.getEncoded();
		// 4、返回密钥
        return key;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}

// HMAC 加密
public static String encryptHmac(byte[] data, byte[] key, String algorithm) {
    HMac hmac = generateHmacByAlgorithm(algorithm);
    KeyParameter keyParameter = new KeyParameter(key);
    hmac.init(keyParameter);
    hmac.update(data, 0, data.length);
    byte[] rsData = new byte[hmac.getMacSize()];
    hmac.doFinal(rsData, 0);

    return Hex.toHexString(rsData);
}
```

##### Commons Codec 的HMAC 算法实现

Commons Codec 提供的 HMAC 算法实现：

```java
// 获取 HMAC Key
public static byte[] getHmacKey(String algorithm) {
    try {
        // 1、创建密钥生成器
        KeyGenerator keyGenerator = KeyGenerator.getInstance(algorithm);
        // 2、产生密钥
        SecretKey secretKey = keyGenerator.generateKey();
        // 3、获取密钥
        byte[] key = secretKey.getEncoded();
		// 4、返回密钥
        return key;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}

// HMAC 加密
public static String encryptHmac(byte[] data, byte[] key, String algorithm) {
    Mac mac = HmacUtils.getInitializedMac(HmacAlgorithms.HMAC_MD5, key);
    return Hex.encodeHexString(mac.doFinal(data));
}
```

> 获取完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### HMAC 算法的应用

##### 1、可以验证对端用户的合法性

HMAC算法的一个典型应用是用在“挑战/响应”（Challenge/Response）身份认证中，认证流程如下：

(1) 先由客户端向服务器发出一个验证请求，（假设是浏览器的GET请求）。

(2) 服务器接到此请求后生成一个随机数并通过网络传输给客户端（此为挑战）。

(3) 客户端将收到的随机数与自己的密钥进行HMAC运算并得到一个结果作为认证证据返回给服务器（此为响应）。

(4) 与此同时，服务器也使用该随机数与存储在服务器数据库中的该客户密钥进行HMAC运算，如果服务器的运算结果与客户端传回的响应结果相同，则认为客户端是一个合法用户 。

在这个过程中，可能遭到安全攻击的是服务器发送的随机数和客户端返回的HMAC结果，而对于截获了这两个值的黑客而言这两个值是没有意义的，随机值的引入使HMAC只在当前会话中有效，大大增强了安全性和实用性。

##### 2、发消息给对方（或从对方接收消息）

比如你和对方共享了一个密钥K，现在你要发消息给对方，既要保证消息没有被篡改（完整性），又要能证明信息确实是你本人发的（源认证），那么就把原信息和密钥K经 HMAC 计算的值一起发过去。对方接到之后，使用自己手中的密钥K和原消息计算一下HMAC的值，如果和你发送的HMAC一致，那么可以认为这个消息既没有被篡改也没有冒充。

#### 普通散列算法和HMAC算法的区别

普通散列算法就是通过hash对要输出的数据进行摘要，接收到数据时，再同样对源数据进行散列，与给定的散列值比较，看收到的数据与计算的hash值是否一致就可以了。

通常来说，传输的数据和散列值是不同的渠道给出的，比如网页上显示MD5或SHA的散列值，但是下载链接是某个镜像网站的，这并不影响你下载到的文件的正确性。

如果要通过同一个渠道发送数据和散列值的话（比如消息认证码），就要考虑数据和散列值是否会同时被篡改的问题，如果第三方修改了数据，然后进行MD5散列，并一块发给接收方，接收方并不能察觉到数据被篡改。

而HMAC算法就可以用一把发送方和接收方都有的密钥key进行计算，而没有这个密钥key的第三方是无法计算出正确的散列值的，这样就可以防止数据的来源方被篡改。

#### 总结

HMAC算法目前的应用场景相对还比较少，通常会直接使用加密算法来保证来源方的正确性，不过HMAC在特定场景下还是有一定用途的，大家可以根据自己的业务特点来选择使用哪种加密算法。另外也欢迎大家关注我们的公众号，回复【加解密】获取本系列的全部源码。

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***