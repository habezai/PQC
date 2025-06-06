---
title: FIPS203 中文版
layout: default

---
# 1.简介
## 1.1 目的和范围

本标准规定了基于模块格的密钥封装机制(ML‑KEM)。密钥封装机制 (KEM) 是一组算法，可用于在通过公共信道进行通信的双方之间建立共享密钥。KEM 是一种特殊类型的密钥建立方案。其他经 NIST 批准的密钥建立方案在 NIST 特别出版物 (SP) 800‑56A《使用离散对数密码术的成对密钥建立方案建议》 [^2] 和 SP 800‑56B《使用整数分解密码术的成对密钥建立方案建议》[^3] 中进行了规定。

SP 800‑56A 和 SP 800‑56B 中规定的密钥建立方案容易受到使用足够强大的量子计算机的攻击。ML‑KEM是一种已获批准的替代方案，目前人们认为它是安全的，即使面对拥有大规模容错量子计算机的对手也是如此。ML‑KEM源自CRYSTALS‑KYBER KEM [^4] 的第三轮版本，这是 NIST 后量子密码标准化项目中的一项提案。有关 ML‑KEM 和 CRYSTALS‑KYBER 之间的差异，请参阅附录C。

该标准规定了ML‑KEM方案的算法和参数集。旨在提供足够的信息以通过验证的方式实现ML‑KEM（请参阅https://csrc.nist.gov/projects/cryptographic‑module‑validation‑program）。

有关KEM 的一般定义和属性，包括在应用程序中安全使用 KEM 的要求，请参阅 SP 800‑227 [^1]。

该标准为ML‑KEM指定了三组参数，它们在安全强度和性能之间提供了不同的权衡。ML‑KEM的所有三组参数均已获批准，用于保护美国联邦政府敏感的非机密通信系统。

## 1.2 背景

如果大规模量子计算机得以实现，许多常用公钥密码系统的安全性将面临风险。这将包括密钥建立方案和数字签名方案，其安全性取决于解决整数分解和离散对数问题（在有限域和椭圆曲线上）的难度。因此，2016 年，NIST 启动了一项公开的后量子密码 (PQC) 标准化流程，以选择抗量子的公钥密码算法。共有 82 种候选算法提交给 NIST供审议。在过去的几年里，量子计算机的建设取得了稳步的进展。

经过三轮评估和分析，NIST 选择了前四种算法进行标准化。这些算法旨在在可预见的未来保护美国政府的敏感信息，包括在密码相关的量子计算机问世之后。本标准规定了所选算法CRYSTALS‑KYBER 的变体，这是一种基于格的密钥封装机制 (KEM)，由 Peter Schwabe、Roberto Avanzi、 Joppe Bos、Léo Ducas、Eike Kiltz、Tancrède Lepoint、Vadim Lyubashevsky、John Schanck、Gregor Seiler、Damien Stehlé 和 Jintai Ding [^4] 设计。在本标准中，此处指定的 KEM 将被称为 ML‑KEM，因为它基于“有错模块学习”假设。

# 2. 术语、缩略词和符号


## 2.1 术语和定义
- **approved**     
FIPS 批准和/或 NIST 推荐。  
某种算法或技术至少符合以下某个条件: 1)在FIPS或NIST建议中指定，2)在FIPS或NIST建议中采用，3)在NIST批准的安全功能列表中指定。   
> 原文: FIPS-approved and/or NIST-recommended. An algorithm or technique that is either 1) specified in a FIPS or NIST recommendation, 2) adopted in a FIPS or NIST recommendation, or 3) specified in a list of NIST-approved security functions.

- (KEM) ciphertext   
由封装行为生成的位字符串，用作解封装的输入。   
> 原文: A bit string that is produced by encapsulation and used as an input to decapsulation. 

- cryptographic module   
实现 approved 验证的加密功能（包括密钥生成）的硬件、软件和/或固件集，这些功能包含在模块的加密范畴内。   
> 原文: The set of hardware, software, and/or firmware that implements ap-proved cryptographic functions (including key generation) that are con-tained within the cryptographic boundary of the module. 

- decapsulation   
应用用 KEM 的 Decaps 算法的过程。此算法接受 KEM 的密文***ciphertext***和解封装密钥***decapsulation key***作为输入，并生成共享密钥***shared secret key***作为输出   
> 原文: The process of applying the Decaps algorithm of a KEM. This algorithm accepts a KEM ciphertext and the decapsulation key as input and pro-duces a shared secret key as output. 

- decapsulation key   
KEM 在密钥生成期间生成并在解封装过程中使用的密钥。**解封装密钥必须保持私有**，并且在不再需要后必须**销毁**。（参见第 [3.3节]）。   
> 原文: A cryptographic key produced by a KEM during key generation and used during the decapsulation process. The decapsulation key must be kept private and must be destroyed after it is no longer needed. (See Section 3.3.) 

- decryption key   
与 PKE 一起使用的密钥，用于将密文解密为明文。**解密密钥必须保持私有**，并且在不再需要后必须**销毁**。    
> 原文: A cryptographic key that is used with a PKE in order to decrypt cipher-texts into plaintexts. The decryption key must be kept private and must be destroyed after it is no longer needed. 

- destroy   
销毁，应用于密钥或其他机密数据的操作。秘密数据销毁后，无法重新覆盖有关其值的信息。   
> 原文: An action applied to a key or other piece of secret data. After a piece of secret data is destroyed, no information about its value can be re-covered. 

- encapsulation   
应用 KEM 的 Encaps 算法的过程。此算法接受封装密钥***encapsulation key***作为输入，需要私有随机数***private randomness***，并生成共享密钥***shared secret key***和关联的密文***ciphertext***作为输出。   
> 原文: The process of applying the Encaps algorithm of a KEM. This algorithm accepts the encapsulation key as input, requires private randomness, and produces a shared secret key and an associated ciphertext as out-put.

- encapsulation key   
KEM 在密钥生成过程中生成并在封装过程中使用的加密密钥。***encapsulation key***可以公开。（参见第[3.3节]）。   
> 原文: A cryptographic key produced by a KEM during key generation and used during the encapsulation process. The encapsulation key can be made public. (See Section 3.3.) 

- encryption key     
与PKE一起使用的加密密钥，用于将明文加密为密文。***encryption key***可以公开。   
> 原文: A cryptographic key that is used with a PKE in order to encrypt plaintexts into ciphertexts. The encryption key can be made public. 

- equivalent process   
如果在向每个进程输入相同的值 （作为输入参数、作为进程期间可用的值，或两者兼而有之） 时产生相同的输出，则两个进程是等效的。   
> Two processes are equivalent if the same output is produced when the same values are input to each process (either as input parameters, as values made available during the process, or both). 

- fresh random value   
由随机位生成器生成且以前未使用过的输出。   
> An output that was produced by a random bit generator and has not been previously used.


- hash function     
A function on bit strings in which the length of the output is fixed. Approved hash functions (such as those specified in FIPS 180 [^5] and FIPS 202 [^6]) are designed to satisfy the following properties: 
    - 1. (One-way) It is computationally infeasible to find any input that maps to any new pre-specified output.
    - 2. (Collision-resistant) It is computationally infeasible to find any two distinct inputs that map to the same output.   


- input checking   
Examination of a potential input to an algorithm for the purpose of determining whether it conforms to certain requirements.      

- key   
A bit string that is used in conjunction with a cryptographic algorithm, such as the encapsulation and decapsulation keys (of a KEM), the shared secret key (produced by a KEM), and the encryption and decryption keys (of a PKE). (See Section 3.3.)    


- key-encapsulation mechanism (KEM)    
A set of three cryptographic algorithms (KeyGen, Encaps, and Decaps) that can be used by two parties to establish a shared secret key over a public channel.   

- key establishment   
 A procedure that results in secret keying material that is shared among different parties.  

- key pair    
A set of two keys with the property that one key can be made public while the other key must be kept private. In this standard, this could refer to either the (encapsulation key, decapsulation key) key pair of a KEM or the (encryption key, decryption key) key pair of a PKE.   

- little-endian    
The property of a byte string having its bytes positioned in order of increasing significance. In particular, the leftmost (first) byte is the least significant, and the rightmost (last) byte is the most significant. The term “little-endian” may also be applied in the same manner to bit strings (e.g., the 8-bit string 11010001 corresponds to the byte 20 +21 +23 +27 = 139).  
 
- party    
An individual person, organization, device, or process. In this specifica-tion, there are two parties (e.g., Party A and Party B, or Alice and Bob) who jointly perform the key establishment process using a KEM.  

- pseudorandom   
A process (or data produced by a process) is said to be pseudorandom when the outcome is deterministic yet also appears random as long as the internal action of the process is hidden from observation. For cryptographic purposes, “effectively random” means “computationally indistinguishable from random within the limits of the intended security strength.”   

- public channel    
A communication channel between two parties. Such a channel can be observed and possibly also corrupted by third parties.

- public-key encryption scheme (PKE)     
A set of three cryptographic algorithms (KeyGen, Encrypt, and Decrypt) that can be used by two parties to send secret data over a public channel. Also known as an asymmetric encryption scheme. 

- shared secret     
A secret value that has been computed during a key-establishment scheme, is known by both participants, and is used as input to a keyderivation method to produce keying material. 

- shared secret key     
A shared secret that can be used directly as a cryptographic key in symmetric-key cryptography. It does not require additional key derivation. The shared secret key must be kept private and must be destroyed when no longer needed. 

- security category    
A number associated with the security strength of a post-quantum cryptographic algorithm, as specified by NIST (see [^7]). 

- security strength    
A number associated with the amount of work (i.e., the number of operations) that is required to break a cryptographic algorithm or system. 

- **shall**    
Used to indicate a requirement of this standard. 

- **should**    
Used to indicate a strong recommendation but not a requirement of this standard. Ignoring the recommendation could lead to undesirable results.


## 2.2 缩略词
| Acronyms | Description |
| ----------- | ----------- |
|AES | Advanced Encryption Standard |
|CBD |Centered Binomial Distribution |
|FIPS| Federal Information Processing Standard |
|KEM| Key-Encapsulation Mechanism |
|LWE| Learning with Errors |
|MLWE| Module Learning with Errors |
|NIST| National Institute of Standards and Technology |
|NISTIR| NIST Interagency or Internal Report|
|NTT| Number-Theoretic Transform |
|PKE| Public-Key Encryption |
|PQC| Post-Quantum Cryptography |
|PRF| Pseudorandom Function |
|RBG| Random Bit Generator |
|SHA| Secure Hash Algorithm|
|SHAKE| Secure Hash Algorithm KECCAK |
|SP| Special Publication |
|XOF| Extendable-Output Function|

1  
12  
2  
1
2  
1  
2  
1
2  
1  
2
1  
2
1  
2
1   
2
1  
2
1  
2
1  
2
1  
2
1
2  
1
2
12  
1
2  
1
2  
1
2    
1
2  
1  
2  
1
2  
1
2  
1  
2
2  
1  
2
1  
2
1  




## 3.3 ML‑KEM实施的要求


[^1]:
[^2]: 
[^3]:
[^4]:
[^5]:
[^6]:
[^7]:
[^8]:
[^9]:
[^10]:
[^11]:
[^12]:
[^13]:
[^14]:
[^15]:
[3.3节]: #33-mlkem实施的要求


[<< 回到首页](./index)
