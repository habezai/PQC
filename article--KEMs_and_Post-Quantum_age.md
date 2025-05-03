---
title: article.29.Jul.2022 [what's KEM and ]
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

```yaml
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

[<< 回到首页](./index)