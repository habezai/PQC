---
title: ML-KEM
layout: default

---
## NIST 标准
- [FIPS203中文版]


## KEM 介绍
- [什么是KEM 和 IND-CCA2安全性]

## ML-KEM 原理
### 域参数 和 密钥生成
对于ML-KEM-768:
```bash
q = 3329
n = 256
k = 3
η1 = 2
η2 = 2
```
密钥生成：
公钥 (A,t), 其中  $$A \in R_q^{k×k} $$, $$s \in S_{η1}^k $$,$$e \in S_{η2}^k $$ t = As + e ,用于加密

私钥 $$s \in S_{η1}^k $$         ,用于解密

### 加密 和 解密
### 域参数 和 密钥生成


## ML-KEM 计算工具
### openssl 3.5.0 对于 ML-KEM 的应用
ML-KEM密钥对生成
```bash
openssl genpkey -algorithm ML-KEM-512 -out ML-KEM-512_priv.pem
```

ML-KEM密钥对打印
```bash
openssl pkey -in ML-KEM-512_priv.pem -noout -text
```

ML-KEM公钥导出
```bash
openssl pkey -in ML-KEM-512_priv.pem -pubout -out ML-KEM-512_pub.pem
```

公钥打印
```bash
openssl pkey -pubin -in ML-KEM-512_pub.pem -noout -text
```

### ML-KEM 密钥封装

### ML-KEM 解封装




[FIPS203中文版]:./FIPS203_CN

[什么是KEM 和 IND-CCA2安全性]:./article--KEMs_and_Post-Quantum_age.md



[<< 回到首页](./index)