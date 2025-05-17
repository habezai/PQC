---
title: ML-KEM
layout: default
math: mathjax
---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


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
- 公钥 (A,t), 其中  $$A \in R_q^{k×k} $$, $$s \in S_{η1}^k $$,$$e \in S_{η2}^k $$ , $$t = As + e $$,用于加密. 
    - A是均匀随机生成的kxk矩阵，
    - s是k维的小多项式向量，系数大小最多为η1; 
    - e是k维的小多项式向量，系数大小最多为η2;

- 私钥 $$s \in S_{η1}^k $$ ,用于解密

## 加密
加密 一个$$ m\in\{0,1\}^n $$
1. 接收者获取 公钥 (A,t)
2. 选择 $$ r \in _RS^k_{η_{1'}} , e_1 \in _RS^k_{η_{2'}} 且\ e_2 \in _RS^k_{η_{2'}}  $$.  η1'为η1 ，η2' 为η2
3. 计算密文(u,v)  
$$ u  = A^Tr + e_1 , \\  v  = t^Tr + e_2 + \lceil q/2 \rceil m$$

## 解密
$$
m = Round_q(v - s^Tu) \\ =Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - s^Tu) \\ =Round_q(t^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ =Round_q((As+e)^Tr + e_2 + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1)) \\ =Round_q(s^TA^Tr + e^Tr + \lceil q/2 \rceil m - (s^TA^Tr+s^Te_1))\\ =Round_q(\lceil q/2 \rceil m - e^Tr + s^Te_1) $$

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
\end{bmatrix}, \quad \\
e = \begin{bmatrix}
1 - x^{2}+x^{3} \\
 -x + x^{2}
\end{bmatrix}
$$
并且计算
$$\\
t = As + e = \begin{bmatrix}
55 + 96x + 123x^{2}+ 7x^{3}\\
32 + 27x + 127x^{2}+ 100x^{3}
\end{bmatrix}
$$
![图 1](images/ce4468d8f977be3011a70dbe8d739e1d7102c8141abb9357325c73c3c992105c.jpg) 


Alice的公钥(A,t)用于加密，私钥s用于解密


### toy example: 加密
Bob进行加密：
首先，消息编码为 多项式:
$$ 
m=0111 ↔ x+x^2+x^3
$$
，接着Bob选择 $$r, e_1, e_2$$

其中
$$
r = \begin{bmatrix}-2 + 2x + x^{2}-x^{3}\\-1 + x + x^{2}\end{bmatrix}, \quad e_1 = \begin{bmatrix}1 - 2x^{2}+x^{3}\\-1 + 2x - 2x^{2}+x^{3}\end{bmatrix}, \quad  e_2 = 2 + 2x - x^{2}+x^{3}
$$

然后计算：
$$
u = A^Tr + e_1=\begin{bmatrix}56 + 32x+77x^2 + 9x^3\\45 + 21x+2x^2+127x^3\end{bmatrix} \\ \\
v = t^Tr + e_2+ 69m = 3 + 10x + 8x^2 + 123x^3
$$
密文 c  = (u,v)

![图 3](images/d288924f5e38fd93046ab9c231f870e1dc09ccc7996470721009d8dd4a9fa0ab.jpg)  


### toy example: 解密
Alice使用她的解密密钥s，计算 $$v - s^Tu = 4 + 60x + 79x^2 + 66x^3$$
且，round之后,q/4 = 137/4=34.25，即 [-34,34] 的系数设置为0，其余[35,68]∪[-68,-34] 为1:
得到round之后的多项式 $$0 + 1x^1 + 1x^2 + 1x^3$$
于是 恢复了原消息：0111

## 安全性
声称：假设 D-MLWE 问题难以解决，简化的 Kyber-PKE 方案在选择明文攻击(chosen-plaintext attack)下具有不可区分性。

论据：
加密操作：
$$
\begin{bmatrix} u \\ v \end{bmatrix} = \begin{bmatrix} A^T \\ t^T \end{bmatrix} r + \begin{bmatrix} e_1 \\ e_2 \end{bmatrix} + \begin{bmatrix} 0 \\ \left\lceil \frac{q}{2} \right\rfloor \end{bmatrix} m $$

根据 D-MLWE 假设，$\begin{bmatrix} A^T \\ t^T \end{bmatrix}$ 与随机数无法区分。同样根据D-MLWE 假设，
$$
\begin{bmatrix} A^T \\ t^T \end{bmatrix}r + \begin{bmatrix}
e_1 \\ e_2 \end{bmatrix}= \begin{bmatrix} A^Tr + e_1 \\
t^Tr + e_2 \end{bmatrix}
$$
与随机数无法区分。

因此，从对手的角度来看，$$v$$ 似乎是 $$R_q$$ 中随机元素 \((\boldsymbol{t}^T\boldsymbol{r} + e_2)\) 与消息多项式 \(\left\lceil\frac{q}{2}\right\rceil m\) 之和 ，所以对手无法得知关于 \(m\) 的任何信息。




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