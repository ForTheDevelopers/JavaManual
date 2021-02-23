hi，大家好，今天开始我们来介绍一下对称加密算法中的AES算法。

#### AES简介

AES（英语：Advanced Encryption Standard，缩写：AES），即高级加密标准，在密码学中又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。这个标准用来替代原先的DES，已经被多方分析且广为全世界所使用。经过五年的甄选流程，高级加密标准由美国国家标准与技术研究院（NIST）于2001年11月26日发布于FIPS PUB 197，并在2002年5月26日成为有效的标准。2006年，AES算法已然成为对称密钥加密中最流行的算法之一。严格来说 AES 是一种通用的加密标准，通常我们常说的 AES 算法是指 AES 标准的实现算法：Rijndael 算法。

##### Rijndael 算法

NIST 在 1997 年 09 月 12 日公开征集更高效更安全的替代 DES 加密算法，第一轮共有 15 种算法入选，其中 5 种算法入围了决赛，分别是 Rijndael、Serpent、Twofish、RC6 和 MARS。经过 3 年的验证、评测及公众讨论之后 Rijndael 算法最终入选。

Rijndael 算法是一个**分组密码算法族**，其分组长度包括 128 比特、160 比特、192 比特、224 比特、256 比特，密钥长度也包括这五种长度。 AES 标准算法是选取了**分组长度**为 128 比特，**密钥长度**为 128 比特、192 比特和 256 比特的三个版本，名称分别为 AES-128、AES-196 和 AES-256，这三个版本加密思路基本一样，只是密钥扩展算法的过程会稍有不同。

#### AES的基本结构

AES为分组密码，分组密码也就是把明文分成一组一组的，每组长度相等，每次加密一组数据，直到加密完所有组。在AES标准规范中，分组长度只能是128位，也就是说，每个分组为16个字节。密钥的长度可以使用128位、192位或256位。密钥的长度不同，推荐加密轮数也不同。如下表所示：

| 算法    | 密钥长度（bit） | 分组长度（bit） | 加密轮数 |
| ------- | --------------- | --------------- | -------- |
| AES-128 | 128             | 128             | 10       |
| AES-192 | 192             | 128             | 12       |
| AES-256 | 256             | 128             | 14       |

#### AES算法原理

AES算法主要有四种操作处理，分别是

- **密钥加法层** (也叫轮密钥加，英文Add Round Key)
- **字节代换层** (SubByte)
- **行位移层** (Shift Rows)
- **列混淆层**(Mix Column)

AES算法的明文和密钥都是由16个字节组成的数据(当然密钥还支持192位和256位的长度，这里暂不讨论)，它是按照字节的先后顺序从上到下、从左到右进行排列的。而加密出的密文读取顺序也是按照这个顺序读取的，相当于将数组还原成字符串的模样了，然后再解密的时候又是按照4x4数组处理的。AES算法在处理的轮数上只有最后一轮操作与前面的轮处理上有些许不同(最后一轮只是少了**列混淆**处理)，在轮处理开始前还单独进行了一次轮密钥加的处理。在处理轮数上，我们只考虑128位密钥的10轮处理。接下来，就开始一步步的介绍AES算法的处理流程了。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f87acb88189478b920d4f510e1ca561~tplv-k3u1fbpfcp-watermark.image)

**AES算法流程图**:

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00d18b29763d45738d3c282133f793a1~tplv-k3u1fbpfcp-watermark.image)

##### 密钥加法层 (Add Round Key)

在密钥加法层中有两个输入的参数，分别是明文和子密钥k[0]，而且这两个输入都是128位的。k[0]实际上就等同于密钥k，具体原因在密钥扩展生成中进行介绍。我们前面在介绍扩展域加减法中提到过，在扩展域中加减法操作和异或运算等价，所以这里的处理也就异常的简单了，只需要将两个输入的数据进行按字节异或操作就会得到运算的结果。图示：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c81a9719b0b45e8b044ca98868f6acd~tplv-k3u1fbpfcp-watermark.image)

##### 字节代换层 (SubByte)

字节代换层的主要功能就是让输入的数据通过S_box表完成从一个字节到另一个字节的映射，这里的S_box表是通过某种方法计算出来的，具体的计算方法在此不做详述，我们主需要知道如何使用S_box结果即可。S_box表是一个拥有256个字节元素的数组，可以将其定义为一维数组，也可以将其定义为16·16的二维数组，如果将其定义为二维数组，读取S_box数据的方法就是要将输入数据的每个字节的高四位作为第一个下标，第四位作为第二个下标，略有点麻烦。这里建议将其视作一维数组即可。逆S盒与S盒对应，用于解密时对数据处理，我们对解密时的程序处理称作逆字节代换，只是使用的代换表盒加密时不同而已。

**S盒** ：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/491b517e6a564776a28399a1d2041dbd~tplv-k3u1fbpfcp-watermark.image)

**逆S盒** ：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/989a4cc75e76497f8c725c96690ff1d7~tplv-k3u1fbpfcp-watermark.image)

**加密图示**：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7717eced5a044a6a5ac8b47231dbec1~tplv-k3u1fbpfcp-watermark.image)

##### 行位移 (Shift Rows)

行位移操作最为简单，它是用来将输入数据作为一个4·4的字节矩阵进行处理的，然后将这个矩阵的字节进行位置上的置换。ShiftRows子层属于AES手动的扩散层，目的是将单个位上的变换扩散到影响整个状态当，从而达到雪崩效应。在加密时行位移处理与解密时的处理相反，我们这里将解密时的处理称作逆行位移。它之所以称作行位移，是因为它只在4·4矩阵的行间进行操作，每行4字节的数据。

**加密时：**保持矩阵的第一行不变，第二行向左移动8Bit(一个字节)、第三行向左移动2个字节、第四行向左移动3个字节。

**解密时：**保持矩阵的第一行不变，第二行向右移动8Bit(一个字节)、第三行向右移动2个字节、第四行向右移动3个字节。

**正向行位移图解**:

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a55acacb0cc4906b176f347bc977b3d~tplv-k3u1fbpfcp-watermark.image)

**逆向行位移图解**:

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce0a398f4e9c4ac691b3814322b74d95~tplv-k3u1fbpfcp-watermark.image)

##### 列混淆(MixColumn)

列混淆子层是AES算法中最为复杂的部分，属于扩散层，列混淆操作是AES算法中主要的扩散元素，它混淆了输入矩阵的每一列，使输入的每个字节都会影响到4个输出字节。行位移子层和列混淆子层的组合使得经过三轮处理以后，矩阵的每个字节都依赖于16个明文字节成可能。其中包含了矩阵乘法、伽罗瓦域内加法和乘法的相关知识。

#### AES 算法实现

随机生成AES 密钥：

```java
/**
  * 生成随机AES密钥,密钥长度128位
  *
  * @return AES明文密钥Base64字符串
  */
public static String genAesRandomKey2Base64Str() {
    return Base64.encodeBase64String(genAesRandomKey());
}

/**
  * 生成随机AES密钥,密钥长度128位
  *
  * @return AES明文密钥字节数组
  */
public static byte[] genAesRandomKey() {
    KeyGenerator keygen = null;
    try {
        keygen = KeyGenerator.getInstance(AES_ALGORITHM);
    } catch (NoSuchAlgorithmException e) {
        throw new RuntimeException("genarateRandomKey fail!", e);
    }
    SecureRandom random = new SecureRandom();
    keygen.init(random);
    Key key = keygen.generateKey();

    return key.getEncoded();
}
```

AES 加密：

```java
/**
  * AES 加密
  *
  * @param data 待加密密内容
  * @param key  加密密钥
  * @return
  */
public static byte[] encrypt(byte[] data, byte[] key) {
    if (key.length != 16) {
        throw new RuntimeException("Invalid AES key length (must be 16 bytes)");
    }
    try {
        SecretKeySpec secretKey = new SecretKeySpec(key, AES_ALGORITHM);
        // 创建密码器
        Cipher cipher = Cipher.getInstance(AES_CIPTHER);
        cipher.init(Cipher.ENCRYPT_MODE, secretKey);
        byte[] result = cipher.doFinal(data);

        return result;
    } catch (Exception e) {
        throw new RuntimeException("encrypt fail!", e);
    }
}
```

AES 解密：

```java
/**
  * AES 解密
  *
  * @param data 待解密内容
  * @param key  解密密钥
  * @return
  */
public static byte[] decrypt(byte[] data, byte[] key) {
    if (key.length != 16) {
        throw new RuntimeException("Invalid AES key length (must be 16 bytes)");
    }
    try {
        SecretKeySpec secretKey = new SecretKeySpec(key, AES_ALGORITHM);
        // 创建密码器
        Cipher cipher = Cipher.getInstance(AES_CIPTHER);
        cipher.init(Cipher.DECRYPT_MODE, secretKey);
        byte[] result = cipher.doFinal(data);

        return result;
    } catch (Exception e) {
        throw new RuntimeException("decrypt fail!", e);
    }
}
```

> 查看完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### 应用场景

由于AES算法效率较高，一般用于对效率有要求的实时数据加密通信。比如在使用 VPN 或者代理进行加密通信时，既要保证数据的保密性，又要保证不能有高的延迟，所以通常会使用AES算法。

另外在实际应用中通常AES和RSA算法结合使用，一方面确保了密钥的安全性，另一方面又保证了加密的效率。后面我们会专门详细介绍这一部分的内容。

#### 总结

AES算法由于其安全性高，加密速度快，可以处理大量加密数据等特性而被广泛使用，AES由于也属于对称加密算法，所以也涉及分组及填充模式的选择，大家可以参考上一篇文章。另外也欢迎大家关注我们的公众号，回复【加解密】获取本系列的全部源码。

##### 参考

1，[AES算法详解](https://www.cnblogs.com/chenshikun/p/11667438.html)

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***