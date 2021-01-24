hi，大家好，我是开发者FTD。今天我们开始介绍非对称加密算法。非对称加密算法区别于对称加密算法的主要特点是，非对称加密算法有两个密钥：公钥 (public key) 和私钥 (private key)。公钥和私钥是一对密钥，如果用公钥对数据加密，那么只能用对应的私钥解密；相同的，如果用私钥对数据加密，只能用对应的公钥进行解密。因为加密和解密用的是不同的密钥，所以将这种加密算法称为非对称加密。

非对称加密算法的安全性好，由于有两个密钥，所以它不需要交换比较重要的私钥，只需要交换对外公开的公钥即可，它消除了用户最终交换密钥的需要。不过非对称加密算法的加解密速度要远远慢于对称加密，在某些极端情况下，甚至能比对称加密慢上1000倍。

#### RSA 算法简介

RSA 算法是1977年由罗纳德·李维斯特（Ron Rivest）、阿迪·萨莫尔（Adi Shamir）和伦纳德·阿德曼（Leonard Adleman）一起提出的。RSA 算法既能用于加密，也能用于做数字签名。

RSA 算法非常可靠，密钥越长，它就越难破解。根据已经披露的文献，目前被破解的最长RSA密钥是768个二进制位。也就是说，长度超过768位的密钥，还无法破解（至少没人公开宣布）。因此可以认为，1024位的RSA密钥基本安全，2048位的密钥极其安全。

#### 算法原理：

RSA 是目前最有影响力的公钥加密算法，该算法基于一个十分简单的数论事实：将两个大素数相乘十分容易，但想要对其乘积进行因式分解却极其困难，**因此可以将乘积公开作为加密密钥，即公钥，而两个大素数组合成私钥**。公钥是可发布的供任何人使用，私钥则为自己所有，供解密之用。

RSA的公钥、私钥的组成，以及加密、解密的公式可见于下表：

| 公钥 KU | n：两素数 p 和 q 的乘积（p 和 q 必须保密）<br />e：与（p - 1）（q - 1）互质 |
| ------- | ------------------------------------------------------------ |
| 私钥 KR | d：e^-1 （mod(p-1)(q-1)）<br />n： p 和 q 的乘积             |
| 加密    | C ≡ m^e mod n                                                |
| 解密    | m ≡ c^d mod n                                                |

大家看了这些公式估计有些懵，没关系，我们一个一个解释一下。首先我们先复习一下数学中的一些基础概念：

##### 什么是“素数”？

素数也称为“质数”，是指除了能表示为它自己和1的乘积以外，不能表示为任何其它两个整数的乘积的数字。

例如，15＝3＊5，所以15不是素数；又如，12＝6＊2＝4＊3，所以12也不是素数。又例如，13除了等于13＊1以外，不能表示为其它任何两个整数的乘积，所以13是一个素数。

##### 什么是“互质数”（或“互素数”）？

小学数学教材对互质数是这样定义的：“公约数只有1的两个数，叫做互质数。” 这里所说的“两个数”是指自然数。

判别方法主要有以下几种（不限于此）：

- 两个质数一定是互质数。例如，2与7、13与19。
- 一个质数如果不能整除另一个合数，这两个数为互质数。例如，3与10、5与 26。
- 1不是质数也不是合数，它和任何一个自然数在一起都是互质数。如1和9908。
- 相邻的两个自然数是互质数。如 15与 16。
- 相邻的两个奇数是互质数。如 49与 51。
- 大数是质数的两个数是互质数。如97与88。
- 小数是质数，大数不是小数的倍数的两个数是互质数。如 7和 16。
- 两个数都是合数（二数差又较大），小数所有的质因数，都不是大数的约数，这两个数是互质数。如357与715，357=3×7×17，而3、7和17都不是715的约数，这两个数为互质数。等等。

##### 什么是模指数运算？

我们先说下指数运算，指数是位于一个未知数的右上方，表示这个未知数相乘几次，例如：2 ^ 4 = 16；

模运算是整数运算，有一个整数m，以n为模做模运算，即m mod n。让m去被n整除，只取所得的余数作为结果，就叫做模运算。例如，10 mod 3 = 1；26 mod 6 = 2；28 mod 2 = 0 等。 

而模指数运算就是先做指数运算，取其结果后再做模运算。如
$$
5 ^ 3 \quad mod \quad 7 \quad = \quad 125 \quad mod \quad 7 \quad = \quad 6
$$
好，下面我们开始正式介绍RSA加密算法。

##### RSA 算法描述：

1. 选择一对不同的、足够大的素数p，q。

2. 计算 n = pq。

3. 计算 f(n) = (p-1)(q-1) ，同时对p, q严加保密，不能让其他任何人知道。

4. 找一个与 f(n) 互质的数 e，且 1<e<f(n) 。

5. 计算 d，使得 de ≡ 1 mod f(n)。这个公式也可以表达为d ≡ e^-1 mod f(n)

   > 这里要解释一下，**≡** 是数论中表示同余的符号。公式中，≡ 符号的左边必须和符号右边同余，也就是两边模运算结果相同。显而易见，不管f(n)取什么值，符号右边1 mod f(n)的结果都等于1；符号的左边d与e的乘积做模运算后的结果也必须等于1。这就需要计算出d的值，让这个同余等式能够成立。

6. 公钥 KU=(e,n)，私钥 KR=(d,n)。

7. 加密时，先将明文变换成0至n-1的一个整数M。若明文较长，可先分割成适当的组，然后再进行交换。设密文为C，则加密过程为：

$$
C ≡ M^e  \quad mod\quad n
$$



​	解密过程为： 
$$
M ≡ C^d\quad mond \quad n
$$


##### RSA 算法举例：

在这里，我们无法对RSA算法的正确性作严格的数学证明，但我们可以通过一个简单的例子来理解RSA算法的工作原理。为了便于计算。在以下实例中只选取小数值的素数p，q，以及e，假设用户A需要将明文“key”通过RSA加密后传递给用户B，过程如下：

**（1）设计公私密钥(e,n)和(d,n)。**

令p=3，q=11，得出n=p×q=3×11=33；f(n)=(p-1)(q-1)=2×10=20；取e=3，（3与20互质）则e×d≡1 mod f(n)，即 3×d ≡ 1 mod 20。

d怎样取值呢？可以用试算的办法来寻找。试算结果见下表：

| d     | e x d = 3 x d | (e x d) mod (p - 1)(q - 1) = (3 x d) mod 20 |
| ----- | ------------- | ------------------------------------------- |
| 1     | 3             | 3                                           |
| 2     | 6             | 6                                           |
| 3     | 9             | 9                                           |
| 4     | 12            | 12                                          |
| 5     | 15            | 15                                          |
| 6     | 18            | 18                                          |
| 7     | 21            | 1                                           |
| **8** | **24**        | **3**                                       |
| 9     | 27            | 6                                           |

通过试算我们找到，当d=7时，e×d≡1 mod f(n)同余等式成立。因此，可令d=7。从而我们可以设计出一对公私密钥，加密密钥（公钥）为：KU =(e,n)=(3,33)，解密密钥（私钥）为：KR =(d,n)=(7,33)。

（2）英文数字化。

将明文信息数字化，并将每块两个数字分组。假定明文英文字母编码表为按字母顺序排列数值，即：

| 字母 | a    | b    | c    | d    | **e**  | f    | g    | h    | i    | j    | **k**  | l      | m    |
| ---- | ---- | ---- | ---- | ---- | ------ | ---- | ---- | ---- | ---- | ---- | ------ | ------ | ---- |
| 码值 | 01   | 02   | 03   | 04   | **05** | 06   | 07   | 08   | 09   | 10   | **11** | 12     | 13   |
| 字母 | n    | o    | p    | q    | r      | s    | t    | u    | v    | w    | x      | **y**  | z    |
| 码值 | 14   | 15   | 16   | 17   | 18     | 19   | 20   | 21   | 22   | 23   | 24     | **25** | 26   |

则得到分组后的key的明文信息为：11，05，25。

（3）明文加密

用户加密密钥(3,33) 将数字化明文分组信息加密成密文。根据公式
$$
C ≡ M^e  \quad (mod\quad n)
$$
得：

$$
11^3 \quad mod \quad 33 = 11
$$

$$
5^3 \quad mod \quad 33 = 26
$$

$$
25^3 \quad mod \quad 33 = 16
$$

因此，得到相应的密文信息为：11，26，16。

（4）密文解密。

用户B收到密文，若将其解密，只需要根据以下公式计算
$$
M ≡ C^d\quad mond \quad n
$$
，即：
$$
11^7 \quad mod \quad 33 = 11
$$

$$
26^7 \quad mod \quad 33 = 05
$$

$$
16^7 \quad mod \quad 33 = 25
$$

用户B得到明文信息为：11，05，25。根据上面的编码表将其转换为英文，我们又得到了恢复后的原文“key”。 

通过实例，我们可以很清楚的看清楚RSA算法的一个原理。

当然，实际在加密过程中要比这复杂得多，由于RSA算法的公钥私钥的长度（模长度）要到1024位甚至2048位才能保证安全，因此，p、q、e的选取、公钥私钥的生成，加密解密模指数运算都有一定的计算程序，需要仰仗计算机高速完成。

##### RSA 算法工作流程

A 要把信息发给 B 为例，确定角色：A 为加密者，B 为解密者。

首先由 B 随机确定一个 KEY，称之为私钥，将这个 KEY 始终保存在机器 B 中而不发出来；

然后，由这个 KEY 计算出另一个 KEY，称之为公钥。这个公钥的特性是几乎不可能通过它自身计算出生成它的私钥。

接下来通过网络把这个公钥传给 A，A 收到公钥后，利用公钥对信息加密，并把密文通过网络发送到 B。

最后 B 利用已知的私钥，就能对密文进行解密了。

以上就是 RSA 算法的工作流程。

##### RSA 算法加密速度

由于进行的都是大数计算，使得 RSA 最快的情况也比 DES 慢上好几倍，无论是软件还是硬件实现。速度一直是 RSA 的缺陷。一般来说只用于少量数据加密。RSA 的速度是对应同样安全级别的对称密码算法的1/1000左右。

比起 DES 和其它对称算法来说，RSA 要慢得多。实际上一般使用一种对称算法来加密信息，然后用 RSA 来加密比较短的公钥，然后将用 RSA 加密的公钥和用对称算法加密的消息发送给接收方。

这样一来对随机数的要求就更高了，尤其对产生对称密码的要求非常高，否则的话可以越过 RSA 来直接攻击对称密码。

##### 公钥传递安全

和其它加密过程一样，对 RSA 来说分配公钥的过程是非常重要的。分配公钥的过程必须能够抵挡中间人攻击。

假设 A 交给 B 一个公钥，并使 B 相信这是A 的公钥，并且 C 可以截下 A 和 B 之间的信息传递，那么 C 可以将自己的公钥传给 B，B 以为这是 A 的公钥。C 可以将所有 B 传递给 A 的消息截下来，将这个消息用自己的密钥解密，读这个消息，然后将这个消息再用 A 的公钥加密后传给 A。理论上 A 和 B 都不会发现 C 在偷听它们的消息，今天人们一般用数字认证来防止这样的攻击。

#### RSA算法优缺点

##### 优点：

1. 不需要进行密钥传递，提高了安全性
2. 可以进行数字签名认证

##### 缺点：

1. 加密解密效率不高，一般只适用于处理小量数据（如：密钥）
2. 容易遭受小指数攻击

#### RSA 算法实现

RSA密钥生成：

```java
/**
  * 生成RSA 公私钥,并存储到字符串数组中
  *
  * @return
  * @throws NoSuchAlgorithmException
  */
public static String[] genRSAKeys2Str() throws NoSuchAlgorithmException {
    // 生成密匙对
    KeyPair kp = generateKeyPairs();
    //得到公钥
    Key publicKey = kp.getPublic();
    // 得到私钥
    Key privateKey = kp.getPrivate();
    // 得到公私钥字节数组
    byte[] publicKeyBytes = publicKey.getEncoded();
    byte[] privateKeyBytes = privateKey.getEncoded();
    // 得到base64编码后的公私钥字符串
    String publicKeyStr = Base64.encodeBase64String(publicKeyBytes);
    String privateKeyStr = Base64.encodeBase64String(privateKeyBytes);

    String[] rsaKeys = new String[2];
    rsaKeys[0] = publicKeyStr;
    rsaKeys[1] = privateKeyStr;

    return rsaKeys;
}

/**
  * 生成密匙对 KeyPairs
  *
  * @return KeyPair keyPair
  * @throws NoSuchAlgorithmException
  */
private static KeyPair generateKeyPairs() throws NoSuchAlgorithmException {
    // RSA算法要求有一个可信任的随机数源
    SecureRandom sr = new SecureRandom();
    // 为RSA算法创建一个KeyPairGenerator对象
    KeyPairGenerator kpg = KeyPairGenerator.getInstance(RSA_ALGORITHM);
    // 利用上面的随机数据源初始化这个KeyPairGenerator对象
    kpg.initialize(RSA_KEY_SIZE, sr);

    return kpg.generateKeyPair();
}
```

公钥加密：

```java
/**
  * RSA 通过【公钥】加密
  *
  * @param pubKey
  * @param data
  * @return
  * @throws Exception
  */
public static byte[] encryptByPubKey(byte[] pubKey, byte[] data) throws Exception {
    // 构建key
    Key rsaPubKey = buildPubKey(pubKey);
    // 加密
    return encrypt(rsaPubKey, data);
}

// 生成KeySpec，注意公私钥使用的生成类不同
private static Key buildPubKey(byte[] pubKey) throws Exception {
    // 创建KeySpec
    X509EncodedKeySpec keySpec = new X509EncodedKeySpec(pubKey);
    KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM);

    return keyFactory.generatePublic(keySpec);
}

// 加密方法
private static byte[] encrypt(Key key, byte[] data) throws Exception {
    Cipher cipher = Cipher.getInstance(RSA_CIPTHER);
    cipher.init(Cipher.ENCRYPT_MODE, key);
    byte[] encryptData = cipher.doFinal(data);

    return encryptData;
}
```

私钥解密：

```java
/**
  * RSA 使用【私钥】解密
  *
  * @param priKey      密钥
  * @param encryptData 密文数据
  * @return 明文字节数组
  * @throws Exception 解密过程中的异常信息
  */
public static byte[] decryptByPriKey(byte[] priKey, byte[] encryptData)
    throws Exception {

    Key rsaPriKey = buildPriKey(priKey);

    // 返回解密数据
    return decrypt(rsaPriKey, encryptData);
}

// 生成KeySpec，注意公私钥使用的生成类不同
private static Key buildPriKey(byte[] priKey) throws Exception {
    // 创建KeySpec
    PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(priKey);
    KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM);

    return keyFactory.generatePrivate(keySpec);
}

// 解密
private static byte[] decrypt(Key key, byte[] encryptData) throws Exception {
    Cipher cipher = Cipher.getInstance(RSA_CIPTHER);
    cipher.init(Cipher.DECRYPT_MODE, key);
    byte[] decryptData = cipher.doFinal(encryptData);

    // 返回解密数据
    return decryptData;
}
```

> 查看完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### RSA 算法应用场景

**(1) 信息加密**

收信者是唯一能够解开加密信息的人，因此收信者手里的必须是私钥。发信者手里的是公钥，其它人知道公钥没有关系，因为其它人发来的信息对收信者没有意义。

**(2) 登录认证**

客户端需要将认证标识传送给服务器，此认证标识 (可能是一个随机数) 其它客户端可以知道，因此需要用私钥加密，客户端保存的是私钥。服务器端保存的是公钥，其它服务器知道公钥没有关系，因为客户端不需要登录其它服务器。

**(3) 数字签名**

数字签名是为了表明信息没有受到伪造，确实是信息拥有者发出来的，附在信息原文的后面。就像手写的签名一样，具有不可抵赖性和简洁性。

简洁性：对信息原文做哈希运算，得到消息摘要，信息越短加密的耗时越少。

不可抵赖性：信息拥有者要保证签名的唯一性，必须是唯一能够加密消息摘要的人，因此必须用私钥加密 (就像字迹他人无法学会一样)，得到签名。如果用公钥，那每个人都可以伪造签名了。

**(4) 数字证书**

问题起源：对1和3，发信者怎么知道从网上获取的公钥就是真的？没有遭受中间人攻击？

这样就需要第三方机构来保证公钥的合法性，这个第三方机构就是 **CA (Certificate Authority)**，证书中心。

CA 用自己的私钥对信息原文所有者发布的公钥和相关信息进行加密，得出的内容就是数字证书。

信息原文的所有者以后发布信息时，除了带上自己的签名，还带上数字证书，就可以保证信息不被篡改了。信息的接收者先用 CA给的公钥解出信息所有者的公钥，这样可以保证信息所有者的公钥是真正的公钥，然后就能通过该公钥证明数字签名是否真实了。

#### 总结

RSA 算法是目前使用最为广泛的非对称加密算法，你可以在各种对安全要求比较高的场景中看到它的身影，所以掌握RSA 算法是我们工作中比不缺的一项技能。

***创作不易，如果大家喜欢本文，欢迎点赞，转发，你的关注是我们继续前进的动力^_^***

##### 参考：

1，[RSA算法理解](https://www.cnblogs.com/chbo/p/10384049.html)

2，[加解密篇 - 非对称加密算法 (RSA、DSA、ECC、DH)](https://blog.csdn.net/u014294681/article/details/86705999)