---
title: ML-KEM
layout: default
math: katex
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
- 公钥 (A,t), 其中  $$A \in R_q^{k×k} $$, $$s \in S_{η1}^k $$,$$e \in S_{η2}^k $$ , $$t = As + e $$,用于加密

- 私钥 $$s \in S_{η1}^k $$         ,用于解密

## 加密
加密 一个$$ m\in\{0,1\}^n $$
1. 接收者获取 公钥 (A,t)
2. 选择 $$ r \in _RS^k_{η_{1'}} , e_1 \in _RS^k_{η_{2'}} 且\ e_2 \in _RS^k_{η_{2'}}  $$.  η1'为81 ，η2' 为82
3. 计算密文(u,v)  $$ u  = A^Tr + e_1 , \\  v  = t^Tr + e_2 + \lceil q/2 \rceil m$$

## 解密
$$\begin{align*} m &= Round_q(v - s^Tu) \\ &=Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - s^Tu) \\ &=Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ &=Round_q((As+e)^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ &=Round_q(s^TA^Tr + e^Tr + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1))\\ &=Round_q(\lceil q/2 \rceil m - e^Tr + s^Te_1)  \end{align*}$$
其中 $$size(e^Tr) =  nη_2η_{1'} = 256*2*81 $$,
其中 $$size(s^Te_1) = nη_1η_{2'} = 256*2*81 $$
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