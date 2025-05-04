---
title: what's KEM and IND-CCA2 security[article.29.Jul.2022]
layout: default
---

原文链接：
> KEM 和后量子时代
<br>[KEMs and Post-Quantum age](https://words.filippo.io/dispatches/post-quantum-age/)


## 什么是KEM及其IND-CCA2安全性？

### KEM简介

**Key Encapsulation Method（密钥封装方法，简称KEM）** 是以下API的实现：

- `KeyGen() -> 公钥, 私钥`
- `Encapsulate(公钥) -> 密文, 共享密钥`
- `Decapsulate(私钥, 密文) -> 共享密钥`

你可能已经熟悉的一种实现KEM的方法是通过**椭圆曲线Diffie-Hellman（ECDH）**。在ECDH中，密文通常被称为对端(the peer)或临时共享部分(ephemeral share)。

**ECDH实现示例：**

```txt
KeyGen(): 
    私钥d = 随机标量()
    公钥Q = 标量乘(私钥d, 生成元G) = dG

Encapsulate(公钥):
    临时值r = 随机标量()
    密文ciphertext = 标量乘(临时值r, 生成元G) = rG
    s = 标量乘(临时值r, 公钥Q) = rQ = rdG
    共享密钥shared key = KDF(s || 公钥Q || 密文ciphertext)

Decapsulate(私钥d ，密文ciphertext):
    s = 标量乘(私钥d, 密文ciphertext) = d × ciphertext = drG
    共享密钥shared key = KDF(s || 公钥Q || 密文ciphertext)
```

你也可以用​​RSA​​实现KEM，实际上这比RSA-OAEP以及特别是RSA PKCS #1 v1.5加密更简单、更安全且更容易证明。

RSA-KEM实现示例：​​
```txt
KeyGen():
    p, q = 随机素数(), 随机素数()
    公钥 = n = p * q
    e = 65537
    私钥 = d = e⁻¹ mod λ(n)

Encapsulate(n):
    m = 随机(2, n)
    密文 = m ^ e mod n
    共享密钥 = KDF(m || n || 密文)

Decapsulate(d, 密文):
    m = 密文 ^ d mod n
    共享密钥 = KDF(m || n || 密文)
```

### IND-CCA2安全性

​​IND-CCA2​​ 意味着提供“针对自适应选择密文攻击的密文不可区分性”。
`ciphertext INDistinguishability against adaptive Chosen Ciphertext Attacks`
更具体地说，这意味着你 **可以重复使用公钥** ，而不必为每次解封装生成一个新的公钥。相比之下，仅对 **选择明文攻击安全（CPA 安全）** 的方案，攻击者无法访问解密预言机。实际上，这要求每个公钥只能使用一次，限制了其主要用于交互协议。

**CCA安全(CCA-secure)** 的KEM是一个非常多功能的工具，让我们能够在后量子世界中完成许多（尽管不是全部）我们在前量子时代习惯做的事情。

​​与（EC）DH的主要区别在于​​：KEM是非对称的，你不能将公钥用作密文，反之亦然。以下是可以用CCA安全的KEM实现的功能：

- ​​与对称加密结合​​：在离线KEM-DEM公钥加密方案中使用，如ECDHE转换为ECIES：发送方对接收方的公钥进行封装，使用共享密钥通过对称AEAD加密消息，并发送KEM密文和加密后的消息。

- ​​构建认证密钥交换​​：并行运行KEM三次（或两次，仅认证一方）：
一次使用临时公钥ephemeral public key以确保前向保密性，
一次使用一方的长期公钥long term public key，
一次使用另一方的长期公钥other peer's long term public key。
将三个共享密钥进行哈希处理，并使用结果密钥认证双方。

- ​​缓存和重用公钥​​：在类似ECDHE的临时密钥交换中缓存和重用公钥（在TLS中你可能希望或不希望这样做——使用ECDH时，我们几乎集体放弃了这一点，因为如果“公钥”只使用一次，许多实现错误将无法被利用）。

​​Kyber​​ 被设计为一个CPA安全的PKE核心，通过标准构造转变为CCA安全的KEM，但只有CCA安全的KEM被指定为公开的原始操作。这是廉价的——只需在KEM解封装步骤中添加少量哈希调用和一个PKE封装——并且是一个非常好的主意：我们不需要两个名称非常相似但安全性差异很大的原始操作，而是可以在各处使用更安全的那个。
我希望实现尊重设计的初衷，不暴露CPA安全的PKE。Kyber的另一个我欣赏的鲁棒性细节是它通过设计将转录（密文和公钥）哈希到密钥中。当从ECDH制作KEM时，需要将共享密钥、公钥和对端共享/密文输入到KDF中以获得贡献性KEM。Kyber为你完成了这一点。虽然对CCA安全不是必需的，但贡献性行为可以防止一些实际攻击，并应允许将公钥用作认证密钥。将此属性嵌入设计和测试向量是确保实现需要时不会忘记它的正确方法。

[<< 回到首页](./index)