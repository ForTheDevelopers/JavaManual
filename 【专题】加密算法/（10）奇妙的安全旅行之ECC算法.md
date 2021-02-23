hi，大家好，我是开发者FTD。今天我们来介绍一下非对称加密算法的ECC算法。

#### ECC 算法简介

ECC 是 Elliptic Curves Cryptography 的缩写，意为椭圆曲线密码编码学。和RSA算法一样，ECC算法也属于公开密钥算法。最初由 Koblitz 和 Miller 两人于1985年提出，其数学基础是利用椭圆曲线上的有理点构成Abel加法群上椭圆离散对数的计算困难性。

ECC 算法的数学理论非常深奥和复杂，在工程应用中比较难于实现，但它的单位安全强度相对较高，它的破译或求解难度基本上是指数级的，黑客很难用通常使用的暴力破解的方法来破解。RSA算法的特点之一是数学原理相对简单，在工程应用中比较易于实现，但它的单位安全强度相对较低。因此，ECC算法的可以用较少的计算能力提供比RSA加密算法更高的安全强度，有效地解决了“提高安全强度必须增加密钥长度”的工程实现问题。

#### ECC 算法工作原理

近年来，人们对ECC的认识已经不再处于研究阶段，开始逐步进入实际应用，如国家密码管理局颁布的SM2算法就是基于ECC算法的。下面我们来认识一下ECC的工作原理。

##### 密码学中的椭圆曲线 

**定义** 

在有限域 Fp 中定义一个椭圆曲线，常用
$$
y^2 = x^3 + ax + b
$$
Fp 中只有p个元素，p为素数 

Fp 中，
$$
a+b≡c (mod \quad p)，a×b≡c (mod \quad p)，a/b≡c (mod \quad p)
$$

$$
4a^3 + 27b^2 ≠0 (mod\quad p)
$$

a，b是小于p的非负整数 

x，y 属于0到p-1间的证书，曲线标记为 Ep（a，b） 

阶：椭圆曲线上一点P，存在正整数n，使得nP=O∞，则n为P的阶，若n不存在，则P是无限阶的，有限域上定义的椭圆曲线上所有点的阶都存在。 

##### 椭圆曲线难题 

$$
K = kG
$$

其中K,G为Ep（a,b）上的点，k为小于n的整数，n是点G的阶，给定k和G，计算K容易，但是给定K和G，求k就很难了！ 

因此，设K为公钥，k为私钥，G为基点。  

##### ECC 算法加密过程 

1. A选定一条椭圆曲线Ep（a,b），并取曲线上一点作为基点G 

2. A选择一个私钥k，并生成公钥K=kG 

3. A将Ep（a,b）和k，G发送给B 

4. B收到后将明文编码到Ep（a,b）上一点M，并产生一个随机数r 

5. B计算点C1=M+rK，C2=rG 

6. B将C1，C2传给A 

7. A计算C1-kC2=M+rkG-krG=M 

8. A对M解码得到明文 

攻击者只能得到Ep（a,b），G，K，C1，C2，没有k就无法得到M。  

##### ECC 算法签名验签流程 

1. A选定一条椭圆曲线Ep（a，b），并取曲线上一点作为基点G 

2. A选择一个私钥k，并生成公钥K=kG 

3. A产生一个随机数r，计算R(x,y)=rG 

4. A计算Hash=SHA(M)，M‘=M(modp) 

5. A计算S=（Hash+M'k）/r(modp) 

6. B获得S和M'，Ep(a,b)，K，R(x,y) 

7. B计算Hash=SHA(M)，M'=M(modp)

8. B计算R'=（Hash*G+M'*K）/S=(Hash*G+M'*kG)*r/(Hash+M'k)=rG=R（x,y），若R'=R，则验签成功。 

以上加解密和签名验签流程只是一个例子，具体应用时可以利用K=kG这一特性变幻出多种加解密方式。

#### ECC 算法实现

定义椭圆曲线上的点(x,y)：

```java
class Pare {
    long x;
    long y;

    public Pare() {
        super();
    }

    public Pare(long x, long y) {
        super();

        this.x = x;
        this.y = y;
    }

    //加法
    public Pare add(Pare pare) {
        if (this.x == Integer.MAX_VALUE) {//为无穷大时O+P=P
            return pare;
        }
        Pare res = new Pare();
        if (this.y == pare.y && this.x == pare.x) {//相等时
            long d = moddivision(3 * this.x * this.x + EccUtil.e.a, EccUtil.e.p, 2 * this.y);

            res.x = d * d - 2 * this.x;
            res.x = mod(res.x, EccUtil.e.p);

            res.y = d * (this.x - res.x) - this.y;
            res.y = mod(res.y, EccUtil.e.p);
        } else if (pare.x - this.x != 0) {
            long d = moddivision(pare.y - this.y, EccUtil.e.p, pare.x - this.x);
            res.x = d * d - this.x - pare.x;
            res.x = mod(res.x, EccUtil.e.p);

            res.y = d * (this.x - res.x) - this.y;
            res.y = mod(res.y, EccUtil.e.p);
        } else {//P Q互逆,返回无穷大
            res.x = Integer.MAX_VALUE;
            res.y = Integer.MAX_VALUE;
        }

        return res;
    }

    //减法
    public Pare less(Pare p) {
        p.y *= -1;
        return add(p);
    }

    //乘法
    public Pare multiply(long num) {
        Pare p = new Pare(this.x, this.y);
        for (long i = 1; i < num; i++) {
            p = p.add(this);
        }
        return p;
    }

    //求余,解决负号问题
    public long mod(long a, long b) {
        a = a % b;
        while (a < 0) {
            a += b;
        }
        return a;
    }

    //求余取商(a mod b)/c
    /*public long moddivision(long a, long b, long c) {
			a = mod(a,b);
			while(a%c != 0) {
				a += b;
			}
			a = a/c;
			return a;
		}*/
    public long moddivision(long a, long b, long c) {
        a = mod(a, b);
        c = mod(c, b);
        a = a * EccMath.exgcd(c, b);
        return mod(a, b);
    }

    @Override
    public String toString() {
        return EccTools.obox(EccTools.long2hexStr(this.x), 4) + " " + EccTools.obox(EccTools.long2hexStr(this.y), 4);
    }
}
```

加密：

```java
//加密
public Message encryption(Pare g, Pare pbk, Pare word) {
    pbk = g.multiply(privatekey);//公钥
    int d = new Random().nextInt(1024);//随机数
    Pare dg = g.multiply(d);
    Pare dp = pbk.multiply(d);
    Pare send = word.add(dp);
    return new Message(dg, send);
}
```

解密：

```java
//解密
public Pare decryption(Message m) {
    Pare pab = m.pa.multiply(this.privatekey);
    Pare result = m.pb.less(pab);
    return result;
}
```

> 查看完整代码请访问：
>
> [https://github.com/ForTheDevelopers/JavaSecurity](https://github.com/ForTheDevelopers/JavaSecurity)

#### ECC 算法优势：

- **更适合于移动互联网**：ECC 加密算法的密钥长度很短(256位)，意味着占用更少的存储空间，更低的CPU开销和占用更少的带宽。目前手机已经越来越普及，随着越来越多的用户使用移动设备来完成各种网上活动，ECC 加密算法为移动互联网安全提供更好的客户体验。

- **更好的安全性**：ECC 加密算法提供更强的保护，比目前的其他加密算法能更好的防止攻击，使你的网站和基础设施比用传统的加密方法更安全，为移动互联网安全提供更好的保障。

- **更好的性能**： ECC 加密算法可以用较短的密钥长度来提供更好的安全。例如，256 位的 ECC 密钥加密强度等同于 3072 位 RSA 密钥的水平(目前普通使用的 RSA 密钥长度是 2048 位)。其结果是你以更低的计算能力代价得到了更高的安全性。经国外有关权威机构测试，在 Apache 和 IIS 服务器采用 ECC 算法，Web服务器响应时间比 RSA 快十几倍。

- **更大的IT投资回报**：ECC 可帮助保护您的基础设施的投资，提供更高的安全性，并快速处理爆炸增长的移动设备的安全连接。 ECC 的密钥长度增加速度比其他的加密方法都慢(一般按 128 位增长，而 RSA则是倍数增长，如：1024 – 2048 - 4096)，将延长您现有硬件的使用寿命，让您的投资带来更大的回报。

#### 总结

由于ECC加密算法在安全性、实现代价和应用效率上较RSA算法都有明显的优势，目前已经被多家国际标准组织所接受，ECC加密算法替代RSA加密算法，成为行业或组织的公钥密码标准的趋势已经形成，并已有国家(美国、日本、韩国和欧洲一些国家)在国家密码标准中采用了ECC算法体系。我国的国密算法SM2就是基于ECC算法的。

本文初步介绍了ECC算法的基本原理和实现步骤，由于本人水平有限，如有纰漏，还请指正。

##### 参考

1，[ECC算法介绍](https://www.chinassl.net/ecc/n641.html)

2，[解读ECC加密算法](https://blog.csdn.net/xsp0721/article/details/41890795)