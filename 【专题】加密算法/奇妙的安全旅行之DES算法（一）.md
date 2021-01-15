hi，大家好，从今天开始我们介绍加密算法中的对称加密算法。相信大家看了前几篇文章，已经对摘要算法的使用已经有了比较深的认识，摘要算法由于其算法特性，只能单向加密，无法对数据反向解密，这时对称加密算法就出现了。对称算法主要有DES、3DES 和 AES，今天我们重点介绍一下对称加密算法中的DES 算法。

#### DES 算法介绍

DES (Data Encryption Standard)算法是世界上最常用的加密算法。在很长时间内，许多人心目中“密码生成”与DES一直是个同义词。尽管后来有个叫Electronic Frontier Foundation的组织造了台价值22万的机器尝试破解DES加密的数据，直到现在DES和它的变种3DES（三重数据加密算法）仍然在政府和银行中被广泛使用。

> 注：在2001年AES算法成为了DES算法的替代品。

##### 国家标准局催生了DES

1973年5月，在尼克松任期，美国国家标准局下发了红头文件，征求加密算法来保护传输过程中的数据。国家标准局等了很久一直没有人投标，一直到1974年8月6日，尼克松卸任前三天，IBM才拿出了自己家开发的一套代号LUCIFER（金星）的东西。美国安全局评估后，在1977年7月15日采用了LUCIFER的一个变种作为数据加密标准DES。

DES很快被非数字媒体采用，比如电话线中的信号加密。在那些年里，国际香料组织IFF曾用DES来加密那些用电话线传输的秘密配方。（"With Data Encryption, Scents Are Safe at IFF," *Computerworld* 14, No. 21, 95 (1980)）

同时，作为政府之后第二大急需加密的银行业也将DES作为广泛应用的标准，美国国家标准协会ANSI制定了整个银行业的加密规范。1980年采用的ANSI X3.92指定了DES算法的应用。

#### DES 算法特点

- DES加密算法属于对称加密，加密与解密用的是同一个密钥。

- DES加密算法要求密钥必须是8个字节，即64bit长度；有效秘钥长度为56位，每个字节第8位用作奇偶校验。

- 因为密钥是byte[8]，代表字符串也可以是非可见的字节，可以与Base64编码算法一起使用。

- 加密、解密都需要通过字节数组作为数据和密钥进行处理。
- DES 算法是两种加密技术的组合：混乱和扩散。先替代后置换。
- 易于实现，DES 算法只是使用了标准的算术和逻辑运算，其作用的数最多也只有64 位，因此用70年代末期的硬件技术很容易实现。

#### DES 算法的优缺点

##### 优点：

- 效率高，算法简单，系统开销小

- 适合加密大量数据

- 明文长度和密文长度相等

##### 缺点：

- 需要以安全方式进行秘钥交换

- 秘钥管理复杂

DES算法具有很高的安全性，到目前为止，除了用穷举搜索法对DES算法进行攻击外，还没有发现更有效的办法。而56位长的密钥的穷举空间为256，这意味着如果一台计算机的速度是每一秒种检测一百万个密钥，则它搜索完全部密钥就需要将近2285年的时间，可见，这是难以实现的，当然，随著科学技术的发展，当出现超高速计算机后，我们可考虑把DES密钥的长度再增长一些，以此来达到更高的保密程度。

#### DES 加密原理：

DES 使用一个 56 位的密钥以及附加的 8 位奇偶校验位，产生最大 64 位的分组大小。这是一个迭代的分组密码，使用称为 Feistel 的技术，其中将加密的文本块分成两半。使用子密钥对其中一半应用循环功能，然后将输出与另一半进行"异或"运算；接着交换这两半，这一过程会继续下去，但最后一个循环不交换。DES 使用 16 个循环，使用异或，置换，代换，移位操作四种基本运算。

##### DES的加密过程

1. 64位密钥经子密钥产生算法产生出16个48位子密钥：K1,K2,...,K16，分别供第1次，第2次，...，第16次加密迭代使用。

2. 64位明文首先经过初始置换IP，将数据打乱重新排列并分成左右两半，左边32位构成L0，右边32位构成R0。

3. 第i次加密迭代：由轮函数f实现子密钥Ki对Ri-1的加密，结果为32位的数据组f ( Ri-1 , Ki )。f ( Ri-1 , Ki )再与Li-1模2相加，又得到一个32位的数据组Li-1 ⊕ f ( Ri-1 , Ki )。以Li ⊕ f ( Ri-1 , Ki )作为下一次加密迭代的Ri，以Ri-1作为下一次加密迭代的Li ( i = 1,2,...,16)。

4. 按照上一步的规则进行16次加密迭代。

5. 第16次加密迭代结束后，以R16为左，L16为右，合并产生一个64位的数据组。再经过逆初始置换IP-1，将数据重新排列，便得到64位密文。

##### DES的解密过程

1. 64位密钥经子密钥产生算法产生出16个48位子密钥：K1,K2,...,K16，分别供第1次，第2次，...，第16次解密迭代使用。

2. 64位密文首先经过初始置换IP，将数据打乱重新排列并分成左右两半，左边32位构成R16，右边32位构成L16。

3. 第17-i次解密迭代：由轮函数f实现子密钥Ki对Li的解密，结果为32位的数据组f ( Li , Ki )。f ( Li , Ki )再与Ri模2相加，又得到一个32位的数据组Ri ⊕ f ( Li , Ki )。以Ri ⊕ f ( Li , Ki )作为下一次解密迭代的Li-1，以Li作为下一次解密迭代的Li-1 ( i = 16,15,...,1)。

4. 按照上一步的规则进行16次解密迭代。

5. 第16次解密迭代结束后，以L0为左，R0为右，合并产生一个64位的数据组。再经过逆初始置换IP-1，将数据重新排列，便得到64位明文。

#### DES 算法实现

对称加密算法会有不同的分组模式和填充方式，这里我们使用的分组模式是ECB，填充模式为NoPadding，目前这种方式是比较常见的应用场景，实现代码示例如下：

```java
private static final String DES_ALGORITHM = "DES";

private static final String DES_CIPHER = "DES/ECB/NoPadding";

// des 加密
public static byte[] encryptDES(String data, String key) throws Exception {
    // 生成SecretKey对象
    SecretKey secretKey = desKeyGenerator(key);
    // Cipher对象实际完成加密操作
    Cipher cipher = Cipher.getInstance(DES_CIPHER);
    // 用密匙初始化Cipher对象
    cipher.init(Cipher.ENCRYPT_MODE, secretKey);
    // 执行加密操作
    return cipher.doFinal(data.getBytes());
}

// des 解密
public static byte[] decryptDES(byte[] data, String key) throws Exception {
    // 生成SecretKey对象
    SecretKey secretKey = desKeyGenerator(key);
    // Cipher对象实际完成解密操作
    Cipher cipher = Cipher.getInstance(DES_CIPHER);
    // 用密匙初始化Cipher对象
    cipher.init(Cipher.DECRYPT_MODE, secretKey);
    // 执行解密操作
    return cipher.doFinal(data);
}

/**
  * DES Key 生成方法
  *
  * @param key
  * @return
*/
private static SecretKey desKeyGenerator(String key) {
    try {
        // 创建一个DESKeySpec对象
        DESKeySpec desKey = new DESKeySpec(key.getBytes());
        // 创建一个密匙工厂
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance(DES_ALGORITHM);
        // 将DESKeySpec对象转换成SecretKey对象
        return keyFactory.generateSecret(desKey);
    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
    } catch (InvalidKeySpecException e) {
        e.printStackTrace();
    }

    return null;
}

/**
  * Nopadding填充时,明文不是8位的倍数时需补足，这里补位0x00
  *
  * @param b
  * @return
  */
private static byte[] fillZero(byte[] b) {
    int len = b.length;

    //data不足8位以0补足8位
    if (b.length % 8 != 0) {
        len = b.length - b.length % 8 + 8;
    } else {
        return b;
    }
    byte[] needData = null;
    needData = new byte[len];
    for (int i = 0; i < len; i++) {
        needData[i] = 0x00;
    }
    System.arraycopy(b, 0, needData, 0, b.length);

    return needData;
}
```

> 查看完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### 应用场景

DES算法最常用的场景是银行业，如银行卡收单，信用卡持卡人的PIN的加密传输，IC卡与POS间的双向认证、金融交易数据包的MAC校验等，均用到DES算法。另外，在POS、ATM、磁卡及智能卡(IC卡)、加油站、高速公路收费站等领域，DES算法也被广泛应用，以此来实现关键数据的保密。

#### 总结

上面介绍了一下DES 算法的和DES的实现，3DES算法是DES的变种，并不是一种新的算法，这里就不做过多介绍了，实现方式可以去下载完整源码查看。关于对称加密算法我们还没有完整的讨论对称加密中的分组模式以及填充模式，虽然代码实现中也涉及了分组和填充，下一节我们会详细介绍这部分内容。

DES算法虽然有被破解的可能，但是目前依然在一些金融系统中被广泛使用，尤其工作是在金融方面的小伙伴们，也是需要大家去了解和掌握的，以便于工作中遇到时可以有的放矢。

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***