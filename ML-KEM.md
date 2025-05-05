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
- 公钥 (A,t), 其中  $$A \in R_q^{k×k} $$, $$s \in S_{η1}^k $$,$$e \in S_{η2}^k $$ , $$t = As + e $$,用于加密. A是均匀随机生成的kxk矩阵，s是k维的小多项式向量，系数大小最多为η1; e是k维的小多项式向量，系数大小最多为η2;

- 私钥 $$s \in S_{η1}^k $$         ,用于解密

## 加密
加密 一个$$ m\in\{0,1\}^n $$
1. 接收者获取 公钥 (A,t)
2. 选择 $$ r \in _RS^k_{η_{1'}} , e_1 \in _RS^k_{η_{2'}} 且\ e_2 \in _RS^k_{η_{2'}}  $$.  η1'为81 ，η2' 为82
3. 计算密文(u,v)  $$ u  = A^Tr + e_1 , \\  v  = t^Tr + e_2 + \lceil q/2 \rceil m$$

## 解密
$$\begin{align} m &= Round_q(v - s^Tu) \\ &=Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - s^Tu) \\ &=Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ &=Round_q((As+e)^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ &=Round_q(s^TA^Tr + e^Tr + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1))\\ &=Round_q(\lceil q/2 \rceil m - e^Tr + s^Te_1)  \end{align}$$
其中 $$size(e^Tr) =  nη_2η_{1'} = 256*2*2 =  1024 $$,
其中 $$size(s^Te_1) = nη_1η_{2'} = 256*2*2 =  1024 $$

##  Toy example - Kyber PKE (参数设置小只是为了方便展示)
 Domain parameters: 
✦ Key generation: Alice selects:  
$$q =137\\ n=4\\ k=2\\ η_1=2\\ η_2=2$$
$$A = \begin{bmatrix}
21 + 57x + 78x^{2}+43x^{3}&126 + 122x + 19x^{2}+125x^{3}\\
111 + 9x + 63x^{2}+33x^{3}&105 + 61x + 71x^{2}+64x^{3}
\end{bmatrix} $$
$$
s = \begin{bmatrix}
1 + 2x - x^{2}+2x^{3} \\
 -x + 2x^{3}
\end{bmatrix}, \quad
e = \begin{bmatrix}
1 - x^{2}+x^{3} \\
 -x + x^{2}
\end{bmatrix}
$$
并且计算


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