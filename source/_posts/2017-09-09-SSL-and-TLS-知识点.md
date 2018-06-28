---
layout: post
title: SSL and TLS 知识点
author: renz
date: 2017-09-09
categories: SSL
tags: [PKI, SSL]
update: 2017-10-17
---
TLS四个核心主协议：
- handshake protocol 握手协议
- change cipher spec protocol 密钥规格变更协议
- application data protocol 应用数据协议
- alert protocol 警报协议

- record协议：包括对消息的分段、压缩、消息认证和完整性保护、加密

SSL核心功能：握手、密钥交换、相互认证、保密数据传输

SSL握手的三个目的：
- 客户端与服务器就一组保护数据的算法达成一致
- 确立一组由算法使用的密钥
- 还可以选择对客户端身份进行认证

由于SSL/TLS协议自身特性（数字证书），不能被用于保护多跳端到端通信，只能保护点到点通信。

SSL/TLS协议能够提供的安全目标：
- 身份认证（数字证书）
- 机密传输（信息加密）
- 数据完整性（MAC）
- 重放保护（通过使用隐式序列号防止重放攻击）

# SSL总体流程
![SSL握手概述](/images/SSL握手概述.png)
1. 客户端将其支持的算法列表和一个用作密钥生成算法输入的随机数发送给服务器
2. 服务器选择一个加密算法，并将其和包含服务器公钥的证书发送给客户端，还包括一个用作密钥生成算法输入的随机数
3. 客户端验证服务器证书，并抽取服务器公钥。同时生成一个pre_master_secret随即密码串，然后使用服务器公钥对其进行加密，将加密的信息发送给服务器
4. 客户端和服务器根据pre_master_secret和随机数值独立计算加密密钥和MAC密钥
5. 客户端将所有握手消息MAC值加密后发送给服务器
6. 服务器将所有握手消息MAC值加密后发送给客户端


# SSL连接
![SSL连接](/images/SSL连接.png)
1. 握手消息ClientHello，用于告知服务器客服端所支持的密码套件种类、最高SSL/TLS协议版本以及压缩算法。还包括一个客户端生成的用于密钥产生的随机数，在密钥生成过程中被使用（其它有Session值，第一次握手为0；版本号）
2. 服务器返回的一系列握手消息。首先是ServerHello，包含了服务器选择的加密参数。还包括一个服务器生成的用于密钥产生的随机数，如果是第一次握手，服务器将提供给客户端一个Session值用于恢复使用已经协商好的密钥信息
3. 服务器发送Certificate，该消息是X.509证书序列，证书依序提供，从服务器证书开始，到Certificate authority或者最新的自签名证书结束。同时证书会附带携带与协商的密钥交换算法对应的密钥。客户端除了校验签名信息，还要保证证书链中的所有证书均未被吊销。
4. ServerHelloDone，表示服务器已发送在此阶段要发送的全部信息。此项告诉客户端本次Certificate后面没有可选信息，不用再等了
5. ClientKeyExchange客户端验证了服务端证书并并抽取公钥后，使用服务器公钥对生成的被称为pre_master_secret的随机密钥串加密，再通过此消息发送
6. ChangeCipherSpec告诉服务器，客户端之后的消息都是用商定的加密算法，此消息不属于握手消息，
7. Finished 发送前一阶段的所有握手消息MAC值，对握手消息校验，这样服务器可以判断使用的加密算法是否是安全商定的，而没有遭到中间人篡改或诱导
8. ChangeCipherSpec告诉客户端，服务器之后的消息都使用商定的加密算法
9. Finished

## ClientHello
结构体：
```
struct {
 ProtocolVersion client_version;
 Random random;
 SessionlD session_id; 
  CipherSuite cipher_suites<2..2^16-1>;
 CompressionMethod compression_methods<l..2^8-1>;
} ClientHello;

struct {
 uint8 major;
 uint8 minor;
} ProtocolVersion;

struct {
 uint32 gmt_unix_time;
 opaque random_bytes[28];
} Random;

opaque SessionlD<0..32>;
uint8 CipherSuite[2];
enum {null(0),(255)} CompressionMethod; 
```
消息：
```
Handshake Protocol: Client Hello
    Handshake Type: Client Hello (1)
    Version: TLS 1.2 (0x0303)
    Random
        GMT Unix Time: Apr 13, 2053 09:32:30.000000000 �й���׼ʱ��
        Random Bytes: b383297830cf9a3e82ddd7152edff8f6d015fbd697f3a5d8...
    Session ID: d8ae81d62bd18b0cc3f1e7b8774bdd2b1e4755419ab1058e...
    Cipher Suites (14 suites)
        Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)
        Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)
        Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 (0xc02c)
        Cipher Suite: TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (0xc030)
        Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (0xc013)
        Cipher Suite: TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (0xc014)
        Cipher Suite: TLS_RSA_WITH_AES_128_GCM_SHA256 (0x009c)
        Cipher Suite: TLS_RSA_WITH_AES_256_GCM_SHA384 (0x009d)
        Cipher Suite: TLS_RSA_WITH_AES_128_CBC_SHA (0x002f)
        Cipher Suite: TLS_RSA_WITH_AES_256_CBC_SHA (0x0035)
        Cipher Suite: TLS_RSA_WITH_3DES_EDE_CBC_SHA (0x000a)
    Compression Methods (1 method)
        Compression Method: null (0)
    Extensions Length: 407
    Extension: renegotiation_info
        Renegotiation Info extension
            Renegotiation info extension length: 0
    Extension: server_name
        Server Name Indication extension
            Server Name: www.baidu.com
    Extension: signature_algorithms
        Signature Hash Algorithms (9 algorithms)
            Signature Hash Algorithm: 0x0403
                Signature Hash Algorithm Hash: SHA256 (4)
                Signature Hash Algorithm Signature: ECDSA (3)
            Signature Hash Algorithm: 0x0401
                Signature Hash Algorithm Hash: SHA256 (4)
                Signature Hash Algorithm Signature: RSA (1)
            
    Extension: elliptic_curves
        Elliptic curves (4 curves)
            Elliptic curve: Unknown (0xaaaa)
            Elliptic curve: Unknown (0x001d)
            Elliptic curve: secp256r1 (0x0017)
            Elliptic curve: secp384r1 (0x0018)
```
     
客户端在新建连接后，希望重新协商或者响应服务器发起的重新协商请求时，发送这条消息。该消息将客户端的功能和首选项传送给服务器，包含以下关键元素：
- Version 协议版本，包含客户端准备接受的最高SSL版本号
- Random 握手时客户端与服务器都会提供随机数，用于身份验证，可以防止重放攻击（**但是为什么**）
  - Client time（4byte)产生消息时的时间
  - Random bytes（28byte）随机生成，确保即使使用同一个pre_master_secret
- Session ID客户端用来指示希望重复使用前一次连接是的加密密钥资料
- Ciper Suites 密码套件，指定服务器的认证算法、密钥交换算法、批量加密算法和摘要算法（消息完整性）。可参考(ssl握手协议中的CipherSuite)[http://blog.csdn.net/dog250/article/details/5750992] ，baidu一篇正好是公司前辈的文章
- Compression Methods 客户端可以提交一个或多个支持压缩的方法。默认的压缩方法是null
- Extensions 扩展额外数据


## ServerHello
将服务器选择的连接参数传回客户端
结构与ClientHello类似：
```
Handshake Protocol: Server Hello
    Version: TLS 1.2 (0x0303)
    Random
        GMT Unix Time: Sep  9, 2017 14:38:09.000000000 �й���׼ʱ��
        Random Bytes: d3fe2fbdbbe626fc09b691158652d6808239d05b7b9ed0a3...
    Session ID: d8ae81d62bd18b0cc3f1e7b8774bdd2b1e4755419ab1058e...
    Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)
    Compression Method: null (0)
    Extension: Application Layer Protocol Negotiation
        ALPN Protocol
            ALPN Next Protocol: http/1.1
```
服务器也提供了一个随机值，用它连同客户端提供的随机值，以及pre_master_secret一起产生密钥资料。
## Certificate
X.509证书序列
## ServerHelloDone
是一条空消息，表明服务器已经发送了在此阶段要发送的全部消息
## ClientKeyExchange
携带客户端为密钥交换提供的所有信息
结构体：
```
struct {
 select (KeyExchangeAIgorithm) {
 case rsa: EncryptedPreMasterSecret;
 case diffie_hellman: DiffieHellmanClientPublicValue;
 } exchange_keys;
} ClientKeyExchange;
struct {
 ProtocolVersion client_version;
 opaque random[46];
} PreMasterSecret;
struct {
 public-key-encrypted PreMasterSecret pre_master_secret;
} EncryptedPreMasterSecret;
enum { implicit, explicit } PublicValueEncoding;
struct {
 select (PublicValueEncoding) {
 case implicit: struct {};
 case explicit: opaque DH_Yc<I..2^16-1 >;
 } dh_public;
} DiffieHellmanClientPublicValue; 
```
## ChangeCipherSpec（不属于握手消息）
指示发送实现已经切换好的算法和密钥资料，之后发送的消息将使用算法加以保护
## Finished
结构体：
```
SSLv3:
struct {
 opaque md5_hash[16];
 opaque sha_hash[20];
}Finished;
TLS:
struct {
 opaque verify_data[12];
} Finished; 
```
每一方向另一方发送协商后的主密码与连结起来的握手消息的摘要信息，另一方将其与本地计算得出的摘要对比。

```
md5_hash = MD5(master_secret + pad2 + MD5(handshake_messages + Sender + master_secret + pad1) );
```
第一遍调用MD5的输入：所有握手消息、Sender常量、master_secret、填充字节（pad1为字节0x36重复48次所形成的字符串）

第二遍调用MD5的输入：主密码、填充字节（pad2为字节0x5c重复48次所形成的字符串）、第一遍的输出

Finished另一个有趣的地点在于第二个发送Finished的一方将前一个Finished的消息计算在摘要值之内的可能性，如下图所示：
![Finished](/images/SSL-handshark-Finished.png)
这样当服务器计算握手消息的时候会多计算两个握手消息，必然不会匹配。解决的办法是在处理Finished消息之前再创建一份摘要对象的拷贝。

# SSL记录协议
![SSL记录层协议](/images/SSL记录层.png)

SSL记录层协议将数据流分割成片段加密传输

记录头包含三种信息：内容类型、长度、SSL版本

结构体：
```
struct {
 ContentType type;
 ProtocolVersion version;
 uint16 length;
} RecordHeader;
enum {
 change_cipher_spec(20), alert(21), handshake(22),
 application_data(23), (255)
} ContentType;
struct {
 uint8 major;
 uint8 minor;
} ProtocolVersion;
```
## 内容类型
SSL支持四种内容类型：application_data、alert、handshark、change_cipher_spec

alert用于报告各种类型的错误

handshark用于承载握手信息

change_cipher_spec表示加密及认证的改变，指示使用新的密钥

## 长度
让接收方知道要从线路上读取多少字节消息
## 版本号

# 会话恢复
先看与百度的握手过程：
```
459	22.284378	192.168.41.110	115.239.211.112	TLSv1.2	571	Client Hello
461	22.294188	115.239.211.112	192.168.41.110	TLSv1.2	150	Server Hello
462	22.294210	115.239.211.112	192.168.41.110	TLSv1.2	60	Change Cipher Spec
464	22.295709	115.239.211.112	192.168.41.110	TLSv1.2	99	Hello Request, Hello Request
465	22.295942	192.168.41.110	115.239.211.112	TLSv1.2	105	Change Cipher Spec, Hello Request, Hello Request
468	22.299020	192.168.41.110	115.239.211.112	TLSv1.2	82	Application Data
...
```
这里由于ClientHello提供了一个Session，使用了前一次SSL握手协商好的加密密钥信息，因此当客户端接收到ServerHello后就发送ChangeCipherSpec通知服务端后面的消息将使用之前的加密密钥加密

可以发现省略了Certificate，ClientKeyExchange，并不涉及到密钥及加密算法交换等信息，而且此后的消息都是通过加密密钥发送的，如果攻击者冒充服务端，自然无法解开收到的消息，这解释了为什么客户端没有验证服务端身份。

恢复SSL会话流程：
![SSL-Session](/images/SSL-Session.png)
# 客户端身份验证
服务器通过发送CertificateRequest消息请求对客户端进行身份验证，客户端接受后发送自己的Certificate消息，再发送CertificateVerify消息证明自己拥有对应的私钥。

服务器将告诉客户端，我接受哪些证书和签名算法，或者接受哪些证书颁发机构：
```
struct {
    ClientCertificateType certificate_types;
    SignatureAndHashAlgorithm supported_signature_algorithms;
    DistinguishedName certificate_authorities;
} CertificateRequest;
enum{
 rsa_sign(1),dss_sign(2),rsa_fixed_dh(3),dss_fixed_dh(4),
 (255)
}ClientCertificateType;
opaque DistinguishedName<l..2^16-1>;
```
客户端将发送一条到这一步为止的所有握手消息的签名，以此证明自己拥有的私钥与之前发送的客户端证书中的公钥匹配：
```
struct {
    Signature handshark_message_signature;
} CertificateVerify;
```
若是你对我的私钥与证书中CA机构确认的公钥有怀疑，可以自己进行计算签名值对比（我觉得一定会的）

# 临时RSA
ServerKeyExchange（在ServerHelloDone之前）服务器使用该消息传输经过签名的512bit RSA密钥，客户端收到该消息后，验证临时密钥上的服务器签名，并使用其加密pre_master_secret
# 再握手（Rehandshake）
建立SSL连接之后再进行一次握手，客户端发送一条ClientHello，如果服务器同意，发送空的HelloRequest握手消息，客户端发送ClientHello作为响应。如果服务器不同意，发送一条no_renegotiation警示。
# 密钥交换算法的区别
密钥交换的目的是计算**预主密钥**（premaster secret），这个值是组成**主密钥**（master secret）的来源。
## TLS-RSA
PreMasterSecret由客户端指定，并用RSA公钥加密发送给服务器。
## TLS-DH
双方各自提交一个证书包含DH公开值，服务器端提交证书包含DH公开值。
## TLS-DHE
基于DHE的TLS握手中有ServerKeyExchange消息。DH参数和它的数字签名均被包含在消息中，握手过程中交换参数的认证就是通过数字签名实现，支持的签名算法包括RSA和DSS。
## 附：Diffie-Hellman密钥交换
抛开算法的细节（因为我不懂），DH密钥交换需要6个参数：
- 域参数（服务器选取）
  - dh_p
  - dh_g
- 客户端
  - dh_Ys
  - dh_Yc
- 服务器
  - dh_Ys
  - dh_Yc
  
协商过程中客户端和服务器相互发送其中一个参数（dh_Ys和dh_Yc）到对端

服务器发送ServerDHparams:
```
struct {
    opaque dh_p;
    opaque dh_g;
    opaque dh_Ys;
} ServerDHParams;
```
客户端相应并发送其公开参数（dh_Yc）:
```
struct {
    select (PublicValueEncoding) {
        case implicit:
        case explicit:
            opaque dh_Yc;
    } dh_public;
}ClientDiffHellmanPublic;
```
# 密钥生成
Pseudo-random Function（PRF，伪随机函数）：秘密值扩展、密钥生成

工作原理如图
![](/images/PRF.png)
PRF基于两个hash函数：MD5和SHA-1；

有三个输入：
- Secret，例如PreMasterSecret，在使用时被分为长度相同的两半：S1和S2，跟别作为P_MD5和P_SHA-1的输入。
- Label标志服
- Seed种子值（客户端随机数+服务器随机数）

SSL/TLS协议密钥的生成过程：
![](/images/SSL-key.png)

















