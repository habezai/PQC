---
title: 首页
layout: home
---
# 首页

这是一个分享PQC后量子密码学[^1]概念的网站，主要内容为介绍PQC的基本概念，以及NIST的PQC标准。

计算机与互联网领域广泛使用的公钥加密算法均基于三个计算难题：整数分解问题、离散对数问题或椭圆曲线离散对数问题。然而，这些难题均可使用量子计算机并应用Shor算法破解[^2][^3]，或是比Shor算法更快，需求量子比特更少的其他算法破解[^4]。

# 文章列表
- 标准相关
    [NIST的PQC标准]
    [ML-KEM]
    [ML-DSA]

- 原理相关
    [前置数学知识]
    [Kyber]
    [Dilithium]



[NIST的PQC标准]:./NIST的PQC标准
[ML-KEM]:./ML-KEM
[ML-DSA]:./ML-DSA
[前置数学知识]:./前置数学知识
[Kyber]:./Kyber
[Dilithium]:./Dilithium

[^1]: 后量子密码学（英语：Post-quantum cryptography，缩写：PQC），又称为防量子、量子安全、抗量子计算，是密码学的一个研究领域，专门研究能够抵抗量子计算机进行密码分析攻击的加密算法（特别是公钥加密算法）。
[^2]: Peter W. Shor. Polynomial-Time Algorithms for Prime Factorization and Discrete Logarithms on a Quantum Computer. SIAM Journal on Computing. 1997, 26 (5): 1484–1509. [arXiv:quant-ph/9508027](https://arxiv.org/abs/quant-ph/9508027)
[^3]: Daniel J. Bernstein. [Introduction to post-quantum cryptography.](http://www.pqcrypto.org/www.springer.com/cda/content/document/cda_downloaddocument/9783540887010-c1.pdf) Post-Quantum Cryptography. 2009 [2021-02-08].