---
title: NIST的PQC标准
layout: default

---
# NIST目前选定的PQC标准
- ML-KEM
《联邦信息处理标准》（FIPS）203 是通用加密的主要标准。其优点是加密密钥相对较小，双方可以方便地交换，而且运行速度快。该标准基于 CRYSTALS-Kyber 算法，该算法已更名为 ML-KEM，即基于模块-晶格的密钥封装机制的简称。

- ML-DSA
FIPS 204，旨在作为保护数字签名的主要标准。该标准使用 CRYSTALS-Dilithium 算法，该算法已更名为 ML-DSA，是基于模块网格的数字签名算法的简称。

- SLH-DSA
FIPS 205，也是为数字签名设计的。该标准采用 Sphincs+ 算法，已更名为 SLH-DSA，是无状态散列数字签名算法的简称。该标准基于与 ML-DSA 不同的数学方法，旨在作为 ML-DSA 出现漏洞时的备用方法。

- FN-DSA
以 FALCON 为基础的 FIPS 206 标准草案发布后，该算法将被命名为 FN-DSA，是 FFT（快速傅里叶变换） over NTRU-Lattice-Based 数字签名算法的简称。

# NIST的尚未发布的PQC标准
- FN-DSA

[<<< 回到首页](./index)