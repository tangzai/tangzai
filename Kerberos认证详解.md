# Kerberos 认证详解

## Windows基础理解

### Windows密码存储

Windows所有的用户密码都存放在SAM文件中，该文件的存储路径是：`C:\Windows\System32\config\SAM`

Windows不会直接保存文件的密文，他会将密码进行Hash计算，然后再将Hash值保存到文件中（Hash算法：NTLM Hash）



<img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516164616829.png" alt="image-20230516164616829" style="zoom:50%;" />

### NTLM Hash 介绍

#### NTLM Hash python计算代码

````py
from passlib.hash import nthash

hash = nthash.hash('admin')
print(hash)
````

==输出==

```
209c6174da490caeb422f3fa5a7ae634
```

#### NTLM Hash 计算过程

 <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516165313682.png" alt="image-20230516165313682" style="zoom:50%;" />

### Windows 认证完整流程

1. winlogin.exe 进程启动，用户输入密码
2. 用户输入密码完成，winlogin.exe将结果传给 lsass.exe 进程
3. lsass.exe 进程将密码做NT Hash计算
   1. 将得到的计算结果与SAM文件中的Hash值做匹配。匹配成功则允许登录

### NTLM协议认证流程

#### 第一步：协商

由于NTLM协议有v1和v2多个版本，所以需要客户端和服务器进行协商，具体使用哪个版本进行通信

#### 第二步：质询

+ 客户端向服务器发送用户信息（用户名）
+ 服务器收到请求，生成一个16位的随机数，被称之为“Challenge”，使用登录用户名对应的NTLM Hash值加密Challenge，生成Challenge1.并将Challenge（16为随机数）发给客户端
+ 客户端收到“Challenge”（16位随机数），计算自己密码的NTLM Hash值，并将这个值加密Challenge生成Response，然后将Response发给服务器

#### 第三步：验证

服务器收到客户端的Response，并与“Challenge1”进行比较，相同则认证通过

### NTLM v2协议的区别

+ Challenge：NTLM v1的challenge有8位，NTLM v2的Challenge为16位
+ Net-NTLM Hash：NTLM v1的主要加密算是DES，NTLM v2的主要加密算法是HMAC-MD5

### Pass The Hash（哈希传递）

哈希传递时能够在不需要账户明文密码的情况下完成认证的一个技术

#### 哈希传递-必要条件

+ 哈希传递需要被传递认证的用户名

+ 哈希传递需要被传递认证用户的NTLM Hash

  其实在知道用户名和对应的NTLM Hash的前提下，已经可以满足通过 NTLM 认证的条件了

<img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516173558260.png" alt="image-20230516173558260" style="zoom: 33%;" />

### Active Directory 活动目录功能

+ 服务器及客户端计算机管理：管理服务器及客户端计算机账户所有服务器及客户端计算机加入域管理并实施组策略

+ 用户服务：管理用户域账户、用户信急、企业通讯录（与电子邮件系统集成)、用户组管理、用户身份认证、用户授权管理等，按省实施组管理策略。

+ 资源管理：管理打印机、文件共享服务等网络资源。
  桌面配置：系统管理员可以集中的配置各种桌面配置策略，如：用户使用域中资源权限限制、界面功能的限制、应用程序执行特征限制、网络连接限制、安全配置限制等。

+ 应用系统支撑：支持财务、人事、电子邮件、企业信急门户、办公自动化、补丁管理、防病毒系统等各种应用系统。

  **活动目录的本质就是域的一个集中管理控制台**，以方便管理员管理一个中大型企业网络

### 域认证体系-Kerberos

Kerberos 是一种网络认证协议，通过密钥系统为客户机/服务器应用程序提供强大的认证功能

#### 域认证所参与的角色

+ Client

+ Server

+ KDC（Key Distribution Center） = DC

  

#### KDC的组成

AD（account database）：存储所有Client的白名单，只有存在于白名单的client才能顺利申请TGT

Authentication Service：为Client生成TGT的服务

Ticket Granting Service：为Client生成某个服务的ticket

#### 域认证流程 - 粗略流程

1. Client向Kerberos服务发送请求，请求某个Server的权限，Kerberos通过判断数据库是否存在Client的信息来判断是否允许通过，通过则返回TGT和AS给Client

   详解：

   1. 客户端向身份验证服务器（AS）发送用户名，并提供其**密码**。

   2. AS 验证用户名和密码的正确性后，生成一个**随机生成的非对称密钥**（Client-Server Key）。

   3. AS 使用客户端的密码和 Client-Server Key 进行运算，生成一个 **Session Key**。

   4. AS 将生成的 TGT 使用**客户端的密码**进行加密，并将加密后的 TGT 发送给客户端。（此步骤的加密算法是对称加密算法：DES、3DES）

   5. 客户端接收到加密后的 TGT，并使用**自己的密码解密**。（由于使用的是对称加密算法，所以可以使用自己的密码进行解密）

   6. 解密后的 TGT 包含了客户端和 TGS 的信息，以及生成的 Session Key。

      这里的TGT是使用 KDC Hash 做加密的，也就是 krbgt 的哈希

   <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516194347337.png" alt="image-20230516194347337" style="zoom: 33%;" />

   <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516194705508.png" alt="image-20230516194705508" style="zoom:33%;" />

   <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516194811082.png" alt="image-20230516194811082" style="zoom:33%;" />

2. Client得到TGT后，继续向Kerberos请求某个服务器的权限，此时Kerberos通过TGT判断客户端是否有权限访问Server，有则返回Ticket给Client

   详解：

   1. 客户端准备发送给票据授权服务器（TGS）的请求，其中包括要访问的服务的标识和请求的票据（TGT）。

   2. 客户端将请求和 TGT 封装在一个请求消息中，并使用**会话密钥（Session Key）**对请求进行加密。（这里的Session key是由上一步服务器生成并返回给客户端的）

   3. 客户端将加密后的请求消息发送给 TGS。

   4. TGS 接收到请求消息后，使用客户端的**会话密钥解密**消息，并验证票据的有效性。

   5. 如果票据有效，TGS 会为客户端生成一个服务票据（Service Ticket），其中包含客户端和服务的标识以及新的会话密钥。

   6. TGS 使用服务的密钥（Service Key）对服务票据进行加密，并将加密后的服务票据发送给客户端。（Service Ticket 是由 Key Distribution Center (KDC) 或管理员生成并配置在 KDC 中）

   7. 客户端接收到加密后的服务票据后，使用**会话密钥**解密票据，获取包含新会话密钥的票据信息。

   8. 客户端现在拥有了服务票据和新的会话密钥，可以使用该会话密钥与服务进行安全通信。

      **注：在这一步，Client是无法解密TGT的，他只能原封不动的将TGT发给服务器，再由服务器解密**

   <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516195127280.png" alt="image-20230516195127280" style="zoom:33%;" />

   <img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516195732002.png" alt="image-20230516195732002" style="zoom:33%;" />

3. Client得到Ticket后，带着Ticker向服务器请求服务

   详解：

   1. 客户端现在拥有了服务票据和新的会话密钥，可以使用该会话密钥与服务进行安全通信。
   2. 客户端发送服务票据和其他必要的请求信息给服务，以请求访问服务的资源。
   3. 服务使用服务密钥解密服务票据，验证票据的有效性，并提供相应的服务资源给客户端。

<img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230516195902049.png" alt="image-20230516195902049" style="zoom:33%;" />

#### 整个密钥交换过程解释

1. 服务器使用一串随机字符串（Client-Server Key）和Client密码进行计算，生成非对称密钥返回给客户端（Serssion Key）
2. Client 使用该 Session Key 加密TGT去请求Ticket
3. 服务器使用 Session Key 进行解密，生成 Service Key，Service Ticket 是由 Key Distribution Center (KDC) 或管理员生成并配置在 KDC 中，最后发给Client
4. 服务器使用 会话密钥（Session Key）进行解密，得到最后的 Service Key，此后客户端使用这个新密钥于服务器做安全通信

### 白银票据

#### 白银票据特点

1. 不需要域KDC进行交互（直接拿着Ticket跟服务器进行认证）

2. 需要目标服务的NTLM Hash（这个Hash是对Client计算机名的Hash）

   白银票据相当于一张门票，当hack拿到这张门票后，就自然拥有了资源的访问权限。白银票据的伪造也有对应的工具。这个后面再说

#### 白银票据伪造防御方法

1. 尽量保证服务器凭证不被窃取
2. 开启PAC(Privileged Attribute Certificate)特权属性证书保护
   功能，PAC主要是规定服务器将票据发送给kerberosi服务，由kerberosi服务验证票据是否有效。
   开启方式：将注册表中HKEY LOCAL MACHINE\SYSTEM\
   CurrentControlSet\Control\Lsa\Kerberos\Parameters
   ValidateKdcPacSignature设置为1。

### 黄金票据

#### 黄金票据特点

1. 需要于DC通信

2. 需要krbgt用户的hash

   krbgt是拥有管理员权限的系统账户，且krbgt账户的密码会定时更改

### Tickets总结

+ 黄金票据：从攻击面来看，获取krbtgti用户的hash后，可以在域中，进行持久性的隐藏，并且日志无法湘源，但是需要拿到DC权限，使用黄金票据能够在一个域环境中长时间控制整个域。

+ 从防御角度来看，需要经常更新krbtgtl的密码，才能够使得原有的票据失效。最根本的办法是不允许域管账户登录其他服务器。

+ 白银票据：从攻击面来看，伪造白银票据的难度比伪造黄金票据的难度较小，因为一个域中的服务器如果对外的话，非常容易被入侵并且容易被转储Server Hash。

+ 从防御角度来看，需要开启PAC认证，但这会降低认证效率，增加
  DC的负担，最根本的还是要加固服务器本身对外的服务。

### Windows Access Token简介

+ Vindows Token.其实叫Access Token(访问令牌)，它是一个描述进程或者线程安全上下文的一个对象。不同的用户登录计算机后都会生成一个Access Token，这个Token在用户创建进程或者线程时会被使用，不断的拷贝，这也就解释了A用户创建一个进程而该进程没有B用户的权限。
+ 种类：Access Token分为两种（主令牌、模拟令牌)
+ 一般情况下，用户双击运行一个程序，都会拷贝“explorer.exe”的
  Access Token.
+ 当用户注销后，系统将会使主令牌切换为模拟令牌，不会将令牌清除，只有在重启机器后才会清除。

#### Windows Access Token组成

+ 用户帐户的安全标识符（(SD)
+ 用户所属的组的SD
+ 用于标识当前登录会话的登录SD
+ 用户或用户组所拥有的权限列表
+ 所有者SD
+ 主要组的SD
+ 访问控制列表
+ 访问令牌的来源
+ 令牌是主要令牌还是模拟令牌
+ 限制SD的可选列表
+ 目前的模拟等级
+ 其他统计数据

#### Windows Access Toeken - SID（Security Identifiers）安全标识符

安全标识符是一个唯一的字符串，它可以代表一个账户、一个用户组、或者是一次登录。通常它还有一个SD固定列表，例如：Everyone:这种已经内置的账户，默认拥有固定的SID。（相当于计算机的身份证）
https://docs.microsoft.com/zh-cn/windows/desktop/SecAuthZ/well-known-sids
SD的表现形式：

+ 域SD-用户D

+ 计算机SD-用户ID

  SD列表都会存储在域控的AD或者计算机本地账户数据库中。

#### Windows Access Token 产生过程

每个进程创建时都会根据登录会话权限由LSA(Local Security Authority)分配一个Token，如果CreaetProcessl时自己指定了Token，LSA会用该Token,否则就用父进程Tokent的一份拷贝。

<img src="Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20230517002600769.png" alt="image-20230517002600769" style="zoom:50%;" />

## Kerberos 协议

**Kerberos协议的安全问题**

本文节选于《域渗透攻防指南》

​      Kerberos协议是由麻省理工学院提出的一种网络[身份验证](https://cloud.tencent.com/product/mfas?from_column=20065&from=20065)协议，提供了一种在开放的非安全网络中认证识别用户身份信息的方法。它旨在通过使用密钥加密技术为客户端/服务端应用程序提供强身份验证。Kerberos是西方神话中守卫地狱之门的三头犬的名字。之所以使用这个名字是因为Kerberos需要三方的共同参与才能完成一次认证流程。目前主流使用的Kerberos版本为2005年RFC4120**(https://www.rfc-editor.org/rfc/rfc4120.html)**标准定义的KerberosV5版本，Windows、Linux和Mac OS均支持Kerberos协议。

**一.**

**Kerberos基础**

在Kerberos协议中，主要有以下三个角色：

- 访问服务的客户端：Kerberos客户端是代表需要访问资源的用户进行操作的应用程序，例如打开文件、查询数据库或打印文档。每个Kerberos客户端在访问资源之前都会请求身份验证。
- 提供服务的服务端：域内提供服务的服务端，服务端都有一个独一的SPN。
- ‍提供认证服务的KDC(Key Distribution Center，密钥分发中心)：KDC密钥发行中心是一种网络服务，它向活动目录域内的用户和计算机提供会话票据和临时会话密钥，其服务帐户为krbtgt。KDC作为活动目录域服务ADDS的一部分运行在每个域控制器上。 这里说一下krbtgt帐户，该用户是在创建活动目录时系统自动创建的一个账号，其作用是KDC密钥发行中心的服务账号，其密码是系统随机生成的，无法正常登陆主机。在后面的域学习中，会经常和krbtgt帐户打交道，关于该帐户的其他信息会在后面的文章中详细讲解。

如图所示，可以看到krbtgt帐户的信息。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/6c4a989979b729662556fe897a66d4ba.png)

 Kerberos是一种基于票据Ticket的认证方式。客户端想要访问服务端的某个服务，首先需要购买服务端认可的 **ST服务票据(Service Ticket)**。也就是说，客户端在访问服务之前需要先买好票，等待服务验票之后才能访问。但是这张票并不能直接购买，需要一张 **TGT认购权证（Ticket Granting Ticket)**。也就是说，客户端在买票之前必须先获得一张TGT认购权证。**TGT认购权证** 和 **ST服务票据** 均是由KDC(密钥分发中心)发放；因为KDC是运行在域控制器上，所以说TGT认购权证和ST服务票据均是由域控发放。

Kerberos使用TCP/UDP 88端口进行认证，使用TCP/UDP 464端口进行密码重设。如图所示，可以看到域控制器AD01上开放的88和464端口。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/f245e14e6f4ad23a7e7390dc2b130313.png)

**Kerberos中一些名词的简称及含义如表所示：**

| 简称   | 全拼                                                       |
| :----- | :--------------------------------------------------------- |
| DC     | Domain Controller，域控                                    |
| krbtgt | KDC密钥发行中心服务账户                                    |
| KDC    | Key Distribution Center：密钥分发中心，由域控担任          |
| AD     | Active Directory：活动目录，里面包含域内用户数据库         |
| AS     | Authentication Service：认证服务                           |
| TGT    | Ticket Granting Ticket：TGT认购权证，由KDC的AS认证服务发放 |
| TGS    | Ticket Granting Service：票据授予服务                      |
| ST     | Service Ticket：ST服务票据，由KDC的TGS票据授予服务发放     |

​    Kerberos协议有两个基础认证模块：**AS_REQ & AS_REP** 和 **TGS_REQ & TGS_REP** ，以及微软扩展的两个认证模块 S4U 和 PAC 。S4U是微软为了实现委派而扩展的模块，分为 S4U2Self 和 S4U2Proxy 。在Kerberos最初设计的流程里只说明了如何证明客户端的真实身份，但是并没有说明客户端是否有权限访问该服务，因为在域中不同权限的用户能够访问的资源是不同的。因此微软为了解决权限这个问题，引入了 PAC (Privilege Attribute Certificate，特权属性证书) 的概念。

  在分析AS_REQ & AS_REP 和 TGS_REQ & TGS_REP之前，我们先来看看什么是PAC。

**二**

**PAC特权属性证书**

​       PAC (Privilege Attribute Certificate，特权属性证书)，其中所包含的是各种授权信息、附加凭据信息、配置文件和策略信息等。例如用户所属的用户组， 用户所具有的权限等。在最初的RFC1510中规定的标准Kerberos认证过程中并没有PAC，微软在自己的产品中所实现的Kerberos流程加入了PAC的概念，因为在域中不同权限的用户能够访问的资源是不同的，因此微软设计PAC用来辨别用户身份和权限。

​       在一个正常的Kerberos认证流程中，KDC返回的TGT认购权证和ST服务票据中都是带有PAC的。这样做的好处就是在以后对资源的访问中， 服务端再接收到客户请求的时候不再需要借助KDC的帮助提供完整的授权信息来完成对用户权限的判断， 而只需要根据请求中所包含的PAC信息直接与本地资源的ACL相比较做出裁决。

**1**

**PAC结构**

PAC的顶部结构如下：

```javascript
typedef unsigned long ULONG;
typedef unsigned short USHORT;
typedef unsigned long64 ULONG64;
typedef unsigned char UCHAR;

typedef struct _PACTYPE {
    ULONG cBuffers;
    ULONG Version;                         
    PAC_INFO_BUFFER Buffers[1];
} PACTYPE;
```

复制

这些顶部字段的定义如下：

 **· cBuffers：**包含数组缓冲区中的条目数。

 **· Version：**版本

 **· Buffers：**包含一个PAC_INFO_BUFFER结构的数组。

如图所示，是WireShark抓包的PAC结构部分，可以看到cBuffers、Version和Buffers部分：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/d070046d4bac02ebdde21c48f444caaa-16987624626189.png)

而PAC_INFO_BUFFER结构包含了关于PAC的每个部分的信息，这部分是最重要的，结构如下：

```javascript
typedef struct _PAC_INFO_BUFFER {    ULONG ulType;    ULONG cbBufferSize;    ULONG64 Offset;} PAC_INFO_BUFFER;
```

复制

类型字段的定义如下：

 **· ulType：**包含此缓冲区中包含的数据的类型。它可能是以下之一：

​           · Logon Info (1)

​           · Client Info Type（10）

​           · UPN DNS Info (12)

​           · Sserver Cechksum (6)

​           · Privsvr Cechksum (7)

 **· cbBufferSize：**缓冲大小

 **· Offset：**缓冲偏移量

如图所示，是WireShark抓包的PAC_INFO_BUFFER结构部分。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/24eebb1eb0d8dab20b37c93d3d00264e-169876246261811.png)

**2**

**PAC凭证信息**

​    LOGON INFO类型的PAC_LOGON_INFO包含Kerberos票据客户端的凭据信息。数据本身包含在一个KERB_VALIDATION_INFO结构中，该结构是由NDR编码的。NDR编码的输出被放置在LOGON INFO类型的PAC_INFO_BUFFER结构中。如下：

```javascript
typedef struct _KERB_VALIDATION_INFO {
    FILETIME Reserved0;
    FILETIME Reserved1;
    FILETIME KickOffTime;
    FILETIME Reserved2;
    FILETIME Reserved3;
    FILETIME Reserved4;
    UNICODE_STRING Reserved5;
    UNICODE_STRING Reserved6;
    UNICODE_STRING Reserved7;
    UNICODE_STRING Reserved8;
    UNICODE_STRING Reserved9;
    UNICODE_STRING Reserved10;
    USHORT Reserved11;
    USHORT Reserved12;
    ULONG UserId;
    ULONG PrimaryGroupId;
    ULONG GroupCount;
    [size_is(GroupCount)] PGROUP_MEMBERSHIP GroupIds;
    ULONG UserFlags;
    ULONG Reserved13[4];
    UNICODE_STRING Reserved14;
    UNICODE_STRING Reserved15;
    PSID LogonDomainId;
    ULONG Reserved16[2];
    ULONG Reserved17;
    ULONG Reserved18[7];
    ULONG SidCount;
    [size_is(SidCount)] PKERB_SID_AND_ATTRIBUTES ExtraSids;
    PSID ResourceGroupDomainSid;
    ULONG ResourceGroupCount;
    [size_is(ResourceGroupCount)] PGROUP_MEMBERSHIP ResourceGroupIds;
} KERB_VALIDATION_INFO;
```

复制

主要还是关注以下几个字段：

 **· Acct Name：**该字段对应的值是用户sAMAccountName属性的值

 **· Full Name：**该字段对应的值是用户displayName属性的值

 **· User RID：**该字段对应的值是用户的RID，也就是用户SID的最后部分

 **· Group RID：**对于该字段，域用户的Group RID恒为513(也就是Domain Users的RID)，机器用户的Group RID恒为515(也就是Domain Computers的RID)，域控的Group RID恒为516(也就是Domain Controllers的RID)

 **· Num RIDS：**用户所属组的个数

 **· GroupIDS：**用户所属的所有组的RID

如图所示，是是WireShark抓包的PAC_LOGON_INFO部分。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/a815db1e4c536b4c1ebd026dcbdf8e2c-169876246261813.png)

**3**

**PAC签名**

​      **PAC中包含两个数字签名：**PAC_SERVER_CHECKSUM 和 PAC_PRIVSVR_CHECKSUM。PAC_SERVER_CHECKSUM是使用服务密钥进行签名，而PAC_PRIVSVR_CHECKSUM是使用KDC密钥进行签名。签名有两个原因。首先，存在带有服务密钥的签名，以验证此PAC由服务进行了签名。其次，带有KDC密钥的签名是为了防止不受信任的服务用无效的PAC为自己伪造票据。

​      这两个签名分别以PAC_SERVER_CHECKSUM和PAC_PRIVSVR_CHECKSUM类型的PAC_INFO_BUFFER发送。在PAC数据用于访问控制之前，必须检查PAC_SERVER_CHECKSUM签名。这将验证客户端是否知道服务的密钥。而PAC_PRIVSVR_CHECKSUM签名的验证是可选的，默认不开启。它用于验证PAC是否由KDC签发，而不是由KDC以外的具有访问服务密钥的人放入票据中。

**签名包含在以下结构中：**

```javascript
typedef struct _PAC_SIGNATURE_DATA {
    ULONG SignatureType;
    UCHAR Signature[1];     
} PAC_SIGNATURE_DATA, *PPAC_SIGNATURE_DATA;
```

复制

这些字段定义如下：

 **· SignatureType：**此字段包含用于创建签名的校验和的类型，校验和必须是一个键控的校验和。

 **· Signature：**此字段由一个包含校验和数据的字节数组组成。字节的长度可以由包装PAC_INFO_BUFFER结构来决定。

 如图所示，是PAC中的签名部分，可以看到PAC_SERVER_CHECKSUM和PAC_PRIVSVR_CHECKSUM。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/781b37892d0178c58a374133a85ed298.png)

**4**

**KDC验证PAC**

​      我们注意到，PAC中是有两个签名的：PAC_SERVER_CHECKSUM 和 PAC_PRIVSVR_CHECKSUM。一个是使用服务密钥(PAC_SERVER_CHECKSUM)进行签名，另一个使用KDC密钥(PAC_PRIVSVR_CHECKSUM)进行签名。当服务端收到客户端发来的AP-REQ消息时，只能校验PAC_SERVER_CHECKSUM签名，而并不能校验PAC_PRIVSVR_CHECKSUM签名。因此，正常来说如果需要校验PAC_PRIVSVR_CHECKSUM签名的话，服务端还需要将客户端发来的ST服务票据中的PAC签名发给KDC进行校验。

​       但是，由于大部分服务默认并没有KDC验证PAC这一步(需要将目标服务主机配置为验证KDC PAC签名，默认未开启)，因此服务端就无需将ST服务票据中的PAC签名发给KDC校验了。这也是白银票据攻击能成功的前提，因为如果配置了需要验证PAC_PRIVSVR_CHECKSUM签名的话，服务端会将这个PAC的数字签名以KRB_VERIFY_PAC的消息通过RPC协议发送给KDC，KDC再将验证这个PAC的数字签名的结果以RPC返回码的形式发送给服务端，服务端就可以根据这个返回结果判断PAC的真实性和有效性了。 因此如果目标服务主机配置了要校验PAC_PRIVSVR_CHECKSUM签名的话，就算攻击者拥有服务密钥，可以制作ST服务票据，也不能伪造KDC的PAC_PRIVSVR_CHECKSUM签名，自然就无法通过KDC的签名校验了。

​    那么如何配置服务主机开启KDC签名校验呢，根据微软官方文档的描述，若要开启KDC校验PAC，需要有以下条件：

 **· 应用程序具有SeTcbPrivilege权限。**SeTcbPrivilege权限允许为用户帐户分配“作为操作系统的一部分”。本地系统、网络服务和本地服务帐户都是由windows定义的服务用户帐户。每个帐户都有一组特定的特权。

 **· 应用程序是一个服务，验证KDC PAC签名的注册表项被设置为1，默认为0。**修改方法如下：

​    1.启动注册表编辑器regedit.exe

​    2.找到以下子键：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters

​    3.添加一个ValidateKdcPacSignature的键值(DWORD类型)。该值为0时，不会进行KDC PAC校验。该值为1时，会进行KDC PAC校验。因此可以将该值设置为1启用KDC PAC校验。

对于验证KDC PAC签名这个注册表键值，有以下几点注意事项：

 **·** 如果服务端并非一个服务程序，而是一个普通应用程序，它将不受以上注册表的影响，而总是进行KDC PAC校验。

 **·** 如果服务端并非一个程序，而是一个驱动，其认证过程在系统内核内完成，它将不受以上注册表的影响，而永不进行PAC校验。

 **·** 使用以上注册表项，需要在Windows Server 2003 SP2或更新的操作系统。

 **·** 在运行Windows Server 2008或更新操作系统的服务器上，该注册表项的值缺省为0(默认没有该ValidateKdcPacSignature键值)，也就是不进行KDC PAC校验。

**注：**需要说明的是，注册在本地系统帐户下的服务无论如何配置，都不会触发KDC验证PAC签名。也就是说譬如SMB、CIFS、HOST等服务无论如何都不会触发KDC验证PAC签名。

**那么为什么默认情况下，KDC都不会验证PAC的签名呢？**

​       执行KDC验证PAC的话，意味着在响应时间和带宽使用方面的成本。它需要带宽使用来在应用服务器和KDC之间传输请求和响应。这可能会导致大容量应用程序服务器中出现一些性能问题。在这样的环境中，用户身份验证可能会导致额外的网络延迟和大量的流量。因此，默认情况下，KDC不验证PAC签名。

**5**

**PAC在kerberos中的优缺点**

​    那么PAC的存在究竟给我们的验证过程带来了哪些优点，亦或是缺点呢？

​    正如上面所提到的那样，PAC的引入其实带来了很多的优点。客户端在访问网络资源的时候服务端不再需要向KDC查询授权信息， 而是直接在本地进行PAC信息与ACL的比较。从而节约了网络资源。

如图所示， 在没有PAC的情况下，Server与KDC之间必须进行用户授权信息的查询与返回 ：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/1bfb2d26b4ad14706e8563c455bf050e.png)

当引入PAC之后则变成了如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/1ac3939b9ee0c3662e067631e481dbb6.png)

​       但是，PAC的引入并不是百利而无一害的，PAC在用户的认证阶段引入会导致认证耗时过长。Windows Kerberos客户端会通过RPC调用KDC上的函数来验证PAC信息，这时候用户会观察到在服务器端与KDC之间的RPC包流量的增加。而另一方面， 由于PAC是微软特有的一个特性，所以启用了PAC的域中将不支持装有其他操作系统的服务器， 制约了域配置的灵活性。并且在2014年，由于PAC的安全性导致产生了一个域内极其严重的提权漏洞MS14-068(在后面的文章中我们会介绍这个漏洞)。

**三**

**Kerberos实验**

​    为了更直观的分析Kerberos协议，接下来我们用普通域帐户xie/hack使用impacket工具请求win10机器的cifs服务票据 ，然后远程SMB连接，在该过程中使用WireShark进行抓包。

**实验环境如下：**

用户(xie/hack)：10.211.55.2

域内主机(Win10)： 10.211.55.16

域控(AD01)：10.211.55.4

impacket使用命令如下：

```javascript
#使用hack账号密码请求win10的cifs服务的ST服务票据
python3 getST.py -dc-ip 10.211.55.4 -spn cifs/win10.xie.com xie.com/hack:P@ss1234

#导入该ST服务票据
export KRB5CCNAME=hack.ccache

#使用smb远程连接win10
python3 smbexec.py -no-pass -k win10.xie.com
```

复制

如图所示，可以看到在请求了服务票据后，成功远程SMB连接win10机器。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/95f51e284c1ecaf85345f755a0eb1179.png)

在这个过程中，我们使用WireShark抓包，来进一步的分析Kerberos协议。如图所示，是该过程的抓包图：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/2626b6dacd8e1b94d9695008a89a90af.png)

整个Kerberos认证流程如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/6282eaafea1fec93a7c402b5c72ddb78.png)

下面我们来具体分析Kerberos认证流程的每个步骤：

**四**

**AS-REQ & AS-REP**

我们先来看看AS-REQ&AS-REP请求部分，也就是WireShark抓的第一、二个包，如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/536f8876c2dc1299a0fb156ab4973dd6.png)

如图所示，是一个简易的AS-REQ&AS-REP请求过程图，便于我们直观的了解AS-REQ&AS-REP请求过程。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/707ec40620de60dd4f6f55e60f42e3e1.png)

​      下面让我们具体的分析下AS-REQ&AS-REP过程中的数据包细节：

​      首先，我们来看看客户端是如何获得TGT认购权证的。TGT认购权证是由KDC的 AS（Authentication Service） 认证服务发放的。

**1**

**AS-REQ请求包分析**

​      **AS-REQ：**当域内某个用户想要访问域内某个服务时，于是输入用户名和密码，本机就会向KDC的AS认证服务发送一个AS-REQ认证请求。该请求包中包含如下信息：

 **· 请求的用户名(cname)。**

 **· 域名(realm)。**

 **· Authenticator：**一个抽象的概念，代表一个验证。这里是用户密钥加密的时间戳。

 **· 请求的服务名(sname)：**AS-REQ这个阶段请求的服务都是krbtgt。

 **· 加密类型(etype)。**

 **· 以及一些其他信息：**如版本号，消息类型，票据有效时间，是否包含PAC，协商选项等。

如图所示，是AS-REQ请求包的详细：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/6dfdc298f69cffbf8b94c34a6ab4f7ad.png)

下面我们来看看AS-REQ请求包中每个字段的含义，如下所示：

```javascript
pvno: kerberos版本号,这里为5
msg-type: 消息类型, AS_REQ 对应的是 krb-as-req(10)
padata：主要是一些认证信息，每个认证消息有type和value。
  PA-DATA PA-ENC-TIMESTAMP：这个是预认证，就是用用户Hash加密时间戳，作为value发送给KDC的AS服务。然后KDC从活动目录中查询出用户的hash，使用用户Hash进行解密，获得时间戳，如果能解密，且时间戳在一定的范围内，则证明认证通过。由于是使用用户密码Hash加密的时间戳，所以也就造成了哈希传递攻击
    padata-type: padata类型,这里是 pA-ENC-TIMESTAMP(2)
      padata-vaule: 加密后的值
        etype:  加密类型，这里是 eTYPE-AES256-CTS-HMAC-SHA1-96(18)
        cipher: 密钥
  PA-DATA PA-PAC-REQUEST：这个是启用PAC支持的扩展。这里的value对应的值为True或False,KDC根据include的值来确定返回的票据中是否需要携带PAC。
    padata-type: padata类型,这里是pA-PAC-REQUEST(128)
      padata-vaule: padata的值
        include-pac: 是否包含PAC,这里为True,说明要PAC
req-body：请求body
    padding:填充,这里为0
    kdc-options:用于与KDC协商一些选项设置
      reserved
    forwardable
     forwarded
     proxiable
     proxy
     allow-postdate
     postdated
     unused7
     renewable
     unused9
     unused10
     opt-hardware-auth
     unused12
     unused13
     constrained-delegation
     canonicalize 
     request-anonymous
     unused17
     unused18
     unused19
     unused20
     unused21
     unused22
     unused23
     unused24
     unused25
     disable-transited-check
     renewable-ok
     enc-tkt-in-skey
     unused29
     renew
     validate
    cname：请求的用户名,这个用户名存在和不存在，返回的包有差异，因此可以用于枚举域内用户名。并且当用户名存在，密码正确和错误时，返回包也不一样，因此也可以进行密码喷洒。
      name-type:名字类型，这里是KRB5-NT-PRINCIPAL(1)
      cnmae-string:名字，也就是请求的用户名
        CNameString: 请求的用户名，这里为 hack
    realm:域名，这里为XIE.COM
    sname：请求的服务，包含type和Value。在AS-REQ里面sname始终为krbtgt
      name-type:名字类型，这里是KRB5-NT-PRINCIPAL(1)
      sname-string: krbtgt用户的信息,这里有2个items
        SNameString: 这里是krbtgt用户名
        SNameString: 这里是域名XIE.COM
    till:到期时间
    rtime：也是到期时间
    nonce：随机生成的一个数
    etype：加密类型
      ENCTYPE: eTYPE-AES256-CTS-HMAC-SHA1-96(18)
```

复制

​    我们着重讲一下PA-DATA PA-ENC-TIMESTAMP字段，该字段是用于预认证。

​    在AS-REQ请求包中，只有PA-DATA PA-ENC-TIMESTAMP部分是加密的，这一部分属于预认证，我们称这部分为Authenticator。

​    如图所示，在impacket/krb5/kerberosv5.py中可以看到使用用户的密码Hash或用户的密码AES Key来加密时间戳。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/037e9267462fecba517a62bf27468672.png)

​    对WireShark抓取的流量进行解密，如图所示，可以看到这里是使用hack用户的密钥来解密该值，如下的patimestamp和pauses是解密后的值。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/44fce3220791ba715d4ee717a73b6b29.png)

**2**

**AS-REP回复包分析**

​       **AS-REP：**当KDC的AS认证服务接收到客户端发来的AS-REQ请求后，从活动目录数据库中取出该用户的密钥，然后用该密钥对请求包中的Authenticator预认证部分进行解密，如果解密成功，并且时间戳在有效的范围内，则证明请求者提供的用户密钥正确。

KDC的AS认证服务在成功认证客户端的身份之后，发送AS-REP响应包给客户端。AS-REP响应包中主要包括如下信息：

 **· 请求的用户名(cname)。**

 **· 域名(crealm)。**

 **· TGT认购权证：**包含明文的版本号，域名，请求的服务名，以及加密部分enc-part。加密部分用krbtgt密钥加密。加密部分包含Logon Session Key、用户名、域名、认证时间、认证到期时间和authorization-data。authorization-data中包含最重要的PAC特权属性证书(包含用户的RID，用户所在组的RID) 等。

 **· enc_Logon Session Key：**使用用户密钥加密Logon Session Key后的值，其作用是用于确保客户端和KDC下阶段之间通信安全。也就是AS-REP中最外层的enc-part。

 **· 以及一些其他信息：**如版本号，消息类型等。

如图所示，是AS-REP回复包的详细：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/317598375c8ebd660246d7ac72c5c3b6.png)

**下面我们来看看AS-REP回复包中每个字段的含义，如下所示：**

```javascript
pvno:kerberos版本号,这里为5
msg-type:消息类型,AS_REP对应的是 krb-as-rep(11)
padata：主要是一些认证信息。一个列表，包含若干个认证消息用于认证
  PA-DATA PA-ENCTYPE-INFO2
    padata-type: padata的类型，这里是 pA-ETYPE-INFO2(19)
    padata-value: 加密后的值
      ETYPE-INFO2-ENTRY
         etype: eTYPE-AES256-CTS-HMAC-SHA1-96(18)
         salt: 盐值，这里是 XIE.COMhack
crealm: 域名,这里是XIE.COM
cname：请求的用户名
  name-type: 用户名类型，这里是 kRB5-NT-PRINCIPAL(1)
  cname-string: 1item
    CNameString: hack
ticket：TGT认购权证
  tkt-vno: TGT版本号，这里为5
  realm: 域名，这里是 XIE.COM
  sname: 服务用户名，这里是krbtgt 密码分发中心服务账号
    name-type: KRB5-NT-SRV-INST(2)
    sname-string: 2items
      SNameString: krbtgt
      SNameString: XIE.COM
  enc-part: TGT票据中的加密部分，这部门是用krbtgt的密码Hash加密的。因此如果我们拥有krbtgt的hash就可以自己制作一个ticket，这就造成了黄金票据攻击
    etype: 加密类型，这里是 eTYPE-AES256-CTS-HMAC-SHA1-96(18)
    kvno: 版本号，这里为2
    cipher：加密后的值
enc-part：Login session key，这部分是用请求的用户密码Hash加密的，作为下阶段的认证密钥。
  etype: eTYPE-AES256-CTS-HMAC-SHA1-96(18)
  kvno: 版本号，这里为3
  cipher：加密后的值
```

复制

​       AS-REP返回包中最重要的就是TGT认购权证和加密的Logon Session Key了。TGT认购权证中加密部分是使用krbtgt密钥加密的，而Logon Session Key是使用请求的用户密钥加密的。

​    下面我们通过解密WireShark来看看TGT认购权证和Logon Session Key中到底包含哪些内容。

**1**

**TGT认购权证**

​       AS-REP响应包中的ticket便是TGT认购权证了。TGT认购权证中包含一些明文显示的信息，如版本号tkt-vno、域名realm、请求的服务名sname。但是TGT认购权证中最重要的还是加密部分，加密部分是使用krbtgt帐户密钥加密的。加密部分主要包含的内容有Logon Session Key、请求的用户名cname、域名crealm、认证时间authtime、认证到期时间endtime、authorization-data等信息。

最重要的还是authorization-data部分，这部分中包含客户端的身份权限等信息，这些信息包含在PAC中。

​    如图所示，是TGT认购权证：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/618385dc60125118c51bad3ec63c852d.png)

​    我们来看看TGT认购权证中authorization-data字段下代表用户身份权限的PAC是啥样的。我们对PAC进行解密，只查看PAC的凭证信息部分PAC_LOGON_INFO。

 如图所示，最主要的还是通过User RID和Group RID来辨别用户权限的。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/46a441262d60172ed50a7e82c0ad2f3f.png)

​    **KDC生成PAC的过程如下：**KDC在收到客户端发来的AS-REQ请求后，从请求中取出cname字段，然后查询活动目录数据库，找到sAMAccountName属性为cname字段的值的用户，用该用户的身份生成一个对应的PAC。

**2**

**Logon Session Key**

​      AS-REP响应包最外层的那部分便是加密的Login session Key了，其作用是用于确保客户端和KDC下阶段之间通信安全，它使用请求的用户密钥加密。

​      我们对最外层的enc-part部分进行解密，如图所示，可以看到是使用hack用户的密钥对其进行解密的。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/74090313198a495284f8bcda3558a974.png)

**解密结果如下：**主要包含的内容是认证时间authtime、认证到期时间endtime、域名srealm、请求的服务名sname、协商标志flags等一些信息。需要说明的是，在TGT认购权证中也包含Logon Session Key。

**五**

**TGS-REQ&TGS-REP**

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/170fab9339863bef0f463d385c84daaf.svg+xml)

​    我们再来看看TGS-REQ&TGS-REP请求部分，也就是WireShark抓的第三、四个包，如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/265c474b0ed8fca262e36d21953f523d.png)

​    如图所示，是一个简易的TGS-REQ&TGS-REP请求过程图，便于我们直观的了解TGS-REQ&TGS-REP请求过程。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/885418e9d580f9fc4591bce247952bbb.png)

​    **下面让我们具体的分析下TGS-REQ&TGS-REP过程中的数据包细节：**

​      客户端再收到KDC的AS-REP回复后，使用用户密钥解密enc_Logon Session Key(也就是最外层的enc-part)，得到Logon Session Key，并且也拿到了TGT认购权证。之后它会在本地缓存此 TGT认购权证 和 Logon Session Key。现在客户端需要凭借这张TGT认购凭证向KDC购买相应的ST服务票据（Service Ticket）。ST服务票据是KDC的另一个服务 TGS（Ticket Granting Service）票据授予服务发放的。在这个阶段，微软引入了两个扩展子协议 S4u2self 和 S4u2Proxy(当委派的时候，才用的到，我们会在后面的4.5章中详细介绍)。

**1**

**TGS-REQ请求包分析**

​     **TGS-REQ：**客户端拿着上一步获得的TGT认购权证发起TGS-REQ请求，向KDC购买针对指定服务的ST服务票据，该请求主要包含如下信息：

 **· 域名(realm)。**

 **· 请求的服务名(sname)。**

 **· TGT认购权证。**

 **· Authenticator：**一个抽象的概念，代表一个验证。这里使用Logon Session Key加密的时间戳。

**· 加密类型(etype)。**

 **· 以及一些其他信息：**如版本号，消息类型，协商选项，票据到期时间等。

如图所示，是TGS-REQ请求包的详细：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/756c139db9e410e82a5e7d365493471e.png)

下面我们来看看TGS-REQ请求包中每个字段的含义，如下所示：

```javascript
pvno:kerberos版本号,这里为5
msg-type:消息类型,TGS_REQ对应的是 krb-tgs-req(12)
padata：padata中包含ap_req,这个是TGS_REQ必须携带的部分,这部分会携带AS_REP里面获取到的TGT认购权证和使用原始的Logon Session Key加密的时间戳。还有可能会有PA_FOR_USER，类型是S4U2SELF,是一个唯一的标识符，该标识符指示用户的身份，该标识符由用户名和域名组成。S4U2Proxy必须扩展PA_FOR_USER结构，指定服务代表某个用户去请求针对服务自身的kerberos服务票据。还有可能会有PA_PAC_OPTIONS，类型是PA_PAC_OPTIONS。S4U2Proxy必须扩展PA-PAC-OPTIONS结构。如果是基于资源的约束委派，就需要指定Resource-based Constrained Delegation位。
  padata-type: padata类型，这里是 pA-TGS-REQ(1)
    padata-value: padata的值
    ap-req：这个是TGS_REQ必须携带的部分
      pvn0:5
        msg-type:krb-ap-req(14)
        padding:0
        ap-options:00000000
        reserved: False
        use-session-key: False
        mutual-required: False
        ticket AS-REP响应包中返回的TGT认购权证
            tkt-vno:5
            realm: XIE.COM
            sname
              name-type: kRB5-NT-SRV-PRINCIPAL(1)
              sname-string:2 items
                SNameString: krbtgt
                SNameString: XIE.COM
            enc-part
              etype: eTYPE-AES256-CTS-HMAC-SHA1-96 (18)
              kvno: 2
              cipher: 加密后的值
         authenticator: 原始Logon Session Key加密的时间戳，用于保证会话安全
           etype: eTYPE-AES256-CTS-HMAC-SHA1-96 (18)
            cipher: 加密后的值
req-body：请求body
    padding:这里为0
    kdc-options:用于与KDC约定一些选项设置
        reserved
    forwardable
     forwarded
     proxiable
     proxy
     allow-postdate
     postdated
     unused7
     renewable
     unused9
     unused10
     opt-hardware-auth
     unused12
     unused13
     constrained-delegation
     canonicalize 
     request-anonymous
     unused17
     unused18
     unused19
     unused20
     unused21
     unused22
     unused23
     unused24
     unused25
     disable-transited-check
     renewable-ok
     enc-tkt-in-skey
     unused29
     renew
     validate
    realm:域名，这里为XIE.COM
    sname：要请求的服务名
      name-type: KRB5-NT-SRV-INST(2)
      sname-string: 2 items
        SNameString: cifs
        SNameString: win10.xie.com
    till:到期时间，rubeus和kekeo都是20370913024805Z，这个可以作为特征来检测工具。
    nonce：随机生成的一个数。
    etype：加密类型
      ENCTYPE: eTYPE-ARCFOUR-HMAC-MD5(23)
      ENCTYPE: eTYPE-DES3-CBC-SHA1(16)
      ENCTYPE: eTYPE-DES-CBC-MD5 (3)
      ENCTYPE: eTYPE-AES256-CTS-HMAC-SHA1-96(18)
```

复制

 我们着重讲一下ap-req中的authenticator字段，该字段主要用于后阶段的会话安全认证。

​    为了确保后阶段的会话安全，TGS-REQ中ap-req中的authenticator字段的值是用上一步AS-REP中返回的Logon Session Key加密的时间戳，如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/6fddc81c04461aff7369d9c47f880e22.png)

如图所示，在impacket/krb5/kerberosv5.py可以看到使用如下加密方式使用Logon Session Key加密的时间戳。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/9b953826ed752785757f090c74cf9ccb.png)

**2**

**TGS-REP回复包分析**

​      **TGS-REP：**KDC的TGS服务接收到TGS-REQ请求之后。首先使用krbtgt密钥解密TGT认购权证中加密部分得到Logon Session key和PAC等信息，如果能解密成功则说明该TGT认购权证是KDC颁发的。然后验证PAC的签名，如果签名正确，则证明PAC未经过篡改。然后使用Logon Session Key解密Authenticator得到时间戳等信息，如果能够解密成功，并且票据时间在范围内，则验证了会话的安全性。在完成上述的检测后，KDC的TGS服务完成了对客户端的认证，TGS服务发送响应包给客户端。响应包中主要包括如下信息：

 **· 请求的用户名(cname)**

 **· 域名(crealm)**

 **· ST服务票据：**包含明文的版本号，域名，请求的服务名，以及加密部分enc-part，加密部分用服务密钥加密。加密部分包含用户名、域名、认证时间、认证到期时间、Service Session key和authorization-data。authorization-data中包含最重要的PAC特权属性证书(包含用户的RID，用户所在的组的RDI) 等。

 **· enc_Service Session key：**使用Logon Session key加密的Service Session key，其作用是用于确保客户端和KDC下阶段之间通信安全。

 **· 以及一些其他信息：**如版本号、消息类型等。

​     **注：**这里需要说明的是，TGS-REP这步中KDC并不会验证客户端是否有权限访问服务端。因此，这一步不管用户有没有访问服务的权限，只要TGT正确，均会返回ST服务票据，这也是kerberoasting能利用的原因，任何一个域内用户，都可以请求域内任何一个服务的ST服务票据。

如图所示，是TGS-REP回复包的详细：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/083374e15bf921305db53ce8b8d47fc2.png)

下面我们来看看TGS-REP回复包中每个字段的含义，如下所示：

```javascript
pvno:kerberos版本号,这里为5
msg-type:消息类型,TGS_REP对应的是 krb-tgs-rep(13)
crealm: 域名,这里是XIE.COM
cname：请求的用户名
  name-type: 名称类型，这里为 KRB5-NT-PRINCIPAL(1)
  cname-string: 1 item
    CNameString: hack
ticket：即ST服务票据
  tkt-vno: 服务票据版本号，这里为5
  realm: 域名,这里是XIE.COM
  sname:
    name-type: KRB5-NT-SRV-HST(3)
    sname-string: 2 items
      SNameString: cifs
      SNameString: win10.xie.com
  enc-part: 这部分是用服务的密钥加密的
    etype: 加密类型，eTYPE-AES256-CTS-HMAC-SHA1-96(18)
    kvno: 版本号，这里为3
    cipher：加密后的值
enc-part：这部分是用原始的Logon Session Key加密的。里面最重要的字段是Service session key，作为下阶段的认证密钥。
  etype: 加密类型eTYPE-AES256-CTS-HMAC-SHA1-96(18)
  cipher: 加密后的值
```

复制

​      TGS-REP返回包中最重要的就是ST服务票据和Service Session key了。ST服务票据中加密部分是使用服务密钥加密的，而Service Session key是使用Logon Session Key加密的。

下    面我们通过解密WireShark来看看ST服务票据和Service Session key中到底包含哪些内容。

**1**

**ST服务票据**

​      TGS-REP响应包中的ticket便是ST服务票据了。ST服务票据中包含明文显示的信息，如版本号tkt-vno、域名realm、请求的服务名sname。但是ST服务票据中最重要的还是加密部分，加密部分是使用服务密钥加密的。加密部分主要包含的内容有Server Session Key、请求的用户名cname、域名crealm、认证时间authtime、认证到期时间endtime、authorization-data等信息。最重要的还是authorization-data部分，这部分中包含客户端的身份权限等信息，这些信息包含在PAC中。

​    **如图所示，是ST服务票据：**

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/dd8acfc5d56f6048998104a895969049.png)

​    我们来看看ST服务票据中authorization-data字段下代表用户身份权限的PAC是啥样的。我们对PAC进行解密，只查看PAC的凭证信息部分PAC_LOGON_INFO。

​    如图所示，最主要的还是通过User RID和Group RID来辨别用户权限的。可以看到，ST服务票据中的PAC和TGT认购权证中的PAC是一致的。在正常的非S4u2Self请求的TGS过程中，KDC在ST服务票据中的PAC是直接拷贝TGT票据中的PAC。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/b7534437dce1e92a63488e7284a4e454.png)

**2**

**Service Session Key**

​    TGS-REP响应包最外层的那部分便是Service Session Key了，其作用是用于确保客户端和KDC下阶段之间通信安全，它使用Logon Session Key加密。

​    如图所示，对其进行解密，它主要包含的内容是认证时间authtime、认证到期时间endtime、域名srealm、请求的服务名sname、协商标志flags等一些信息。需要说明的是，在ST服务票据中也包含Service Session Key。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/ccf4ac9c8d523fe41cf9eb4b76725c74.png)

**六**

**如何双向认证？**

**(AP-REQ&AP-REP)**

​      客户端在收到KDC返回的TGS-REP消息，从中取出ST服务票据后，就准备要开始申请访问服务了。

​    由于我们是通过SMB协议远程连接的，因此AP-REQ&AP-REP消息是放在SMB协议中。如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/a4ead13518b47f019f7678504e7a37f1.png)

​     **注：**通过impacket远程连接服务默认是不需要验证提供服务的服务端的，因此这里没有AP-REP回复。

​    如图所示，是一个简易的AP-REQ&AP-REP请求过程图，便于我们直观的了解AP-REQ&AP-REP请求过程。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/3964c36ebb35f1b433531843cac9a082.png)

**1**

**AP-REQ请求包分析**

​    **AP-REQ：**客户端接收到KDC的TGS回复后，通过缓存的Logon Session Key解密enc_Service Session key得到Service Session Key，同时它也拿到了ST(Service Ticket)服务票据。Serivce Session Key 和 ST服务票据会被客户端缓存。

客户端访问指定服务时，将发起AP-REQ请求，该请求主要包含如下的内容：

 **· ST服务票据(ticket)**

 **· Authenticator：**一个抽象的概念，代表一个验证。这里指Serivce Session Key加密的时间戳

 **· 以及一些其他信息：**如版本号、消息类型，协商选项等

如图所示，是AP-REQ请求包的详细：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/f1aebeceb15e428e6cfcb13f055d0172.png)

下面我们来看看AP-REQ请求包中每个字段的含义，如下所示：

```javascript
pvno:kerberos版本号,这里为5
msg-type:消息类型, AP_REQ 对应的是 krb-ap-req(14)
Padding:填充,这里为0
ap-options: 一些协商选项
  reserved
    use-session-key
    mutual-required  该选项代表客户端是否希望验证提供服务的服务端
ticket: ST服务票据
    tkt-vno: 版本号，这里为5
    realm: XIE.COM
    sname:服务名
        name-type: KRB5-NT-SRV-INST(2)
        sname-string: 2 item2
            SNameString: cifs
            SNameString: win10.xie.com
    enc-part: ST服务票据中加密部分
        etype:加密类型，eTYPE-AES256-CTS-HMAC-SHA1-96(18)
        kvno: 版本号，这里是2
        cipher:加密后的值
authenticator: Serivce Session Key加密的时间戳
    etype：加密类型
    cipher：加密后的值
```

复制

**2**

**AP-REP回复包分析**

​     **AP-REP：**这一步是可选的，当客户端希望验证提供服务的服务端时(也就是AP-REQ请求中mutual-required协商选项为True)，服务端返回AP-REP消息。服务端收到客户端发来的AP-REQ消息后，通过服务密钥解密ST服务票据得到Service Session Key和PAC等信息，然后用Service Session Key 解密 Authenticator得到时间戳。如果能解密成功且时间戳在有效范围内，则验证了客户端的身份。验证了客户端身份后，服务端从ST服务票据中取出PAC中代表用户身份权限信息的数据，然后与请求的服务ACL做对比，生成相应的访问令牌。同时，服务端会检查AP-REQ请求中mutual-required协商选项是否为True，如果为True的话，说明客户端想验证服务端的身份。此时，服务端会用Service Session Key加密时间戳作为Authenticator，在AP-REP响应包中发送给客户端进行验证。

​    如果mutual-required选项为False的话，服务端会根据访问令牌的权限决定是否返回相应的服务给客户端。

​    **注：**由于impacket默认是不需要验证服务端身份的，因此如图所示是其他请求方式的AP-REP回复包截图。

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/1602313efb710c0cb5030b3bff6a8cd1.png)

AP-REP响应包中主要包括如下信息：

 **· 版本号**

 **· 消息类型**

 **· enc-part：**使用Serivce Session Key加密的时间戳。

如下是AP-REP回复包的详细：

```javascript
pvno：5
msg-type：消息类型, AP_REP 对应的是 krb-ap-rep(15)
enc-part：Serivce Session Key加密的时间戳
  etype：加密类型
    cipher：加密后的值
```

复制

**七**

**S4u2Self&S4u2Proxy**

​      为了在Kerberos协议层面对约束性委派的支持，微软对Kerberos协议扩展了两个自协议 S4u2self(Service for User to Self) 和 S4u2Proxy (Service for User to Proxy )。S4u2self 可以代表任意用户请求针对自身的服务票据；S4u2Proxy可以用上一步获得的ST服务票据以用户的名义请求针对其它指定服务的ST服务票据。

执行如下命令，machine$机器用户模拟administrator身份访问自身服务。

```javascript
python3 getST.py -dc-ip AD01.xie.com xie.com/machine\$:root -spn cifs/ad01.xie.com -impersonate administrator 
```

复制

如图所示，machine$机器账号以S4u2Self协议模拟administrator身份访问自身服务**。**

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/1b6baed490d5e59252ab0a846f979f1a.png)

**1**

**S4u2Self**

​    和正常的TGS-REQ请求包相比，S4u2Self协议的TGS-REQ请求包会多一个PA-DATA pA-TGS-USER，name为要模拟的用户。

并且sname也是请求的服务自身。如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/a23baa0b04dced1be6b3e46ff5530742.png)

**2**

**S4u2Proxy**

​    和正常的TGS-REQ请求包相比，S4u2Proxy协议的TGS-REQ请求包会增加一个additional-tickets字段，该字段的内容就是上一步利用S4u2Self请求的ST服务票据。如图所示：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/757131ef34e42dea61e57aa0d329b3c3.png)

**八**

**Kerberos协议的安全问题**

如图所示，是Kerberos协议各阶段容易产生的安全问题：

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/4ca94b33505e32adf8f545d6b812c45f.png)

​    **在AS-REQ请求阶段**，是用用户密码Hash或AES Key加密的时间戳。因此当只获得了用户密码Hash时，也可以发起AS-REQ请求，所以也就造成了**PTH哈希传递攻击**；当只获得用户密码的AES Key时，也可以发起AS-REQ请求，所以也就造成了**PTK密钥传递攻击**。

​      而 AS-REQ 请求包中 cname 字段的值代表用户名，这个值存在和不存在，返回的包有差异，所以可以用于枚举域内用户名，这种攻击方式被称为 **域内用户枚举攻击** (当未获取到有效域用户权限时，可以使用这个方法枚举域内用户)。并且当用户名存在，密码正确和密码错误时，返回的包也不一样，所以可以进行用户名密码爆破。但是在实战中，[渗透测试](https://cloud.tencent.com/product/wpt?from_column=20065&from=20065)人员通常都会使用一种被称为 **密码喷洒（Password Spraying）**的攻击方式来进行测试和攻击。对密码进行喷洒式的攻击，这个叫法很形象，因为它属于自动化密码猜测的一种。这种针对所有用户的自动密码喷洒通常是为了避免帐户被锁定，因为针对同一个用户的连续密码猜测会导致帐户被锁定。所以只有对所有用户同时执行特定的密码登录尝试，才能增加破解的概率，消除帐户被锁定的概率。普通的爆破就是用户名固定，爆破密码，但是密码喷洒是用固定的密码去跑所有的用户名。

​    **在 AS-REP 阶段**，由于返回的 TGT 认购权证是由 krbtgt 用户的密码Hash加密的，因此如果我们拥有 krbtgt 的密码 hash 就可以自己制作一个TGT认购权证，这种攻击方式被称为**黄金票据攻击**。同样，在TGS-REP阶段，TGS_REP里面的ST服务票据是使用服务的hash进行加密的，如果我们拥有服务的hash，就可以签发任意用户的ST服务票据，这个票据也被称为白银票据，这种攻击方式被称为**白银票据攻击**。相较于黄金票据，白银票据使用要访问服务的hash，而不是krbtgt的hash。

​    **在AS-REP阶段**，Login session key是用用户密码 Hash 加密的。对于域用户，如果设置了“Do not require Kerberos preauthentication”不需要预认证选项，此时攻击者向域控制器的 88 端口发送 AS_REQ 请求，此时域控不会做任何验证就将 TGT认购权证 和 该用户Hash加密的Login Session Key返回。因此，攻击者就可以对获取到的 用户Hash加密的Login Session Key进行离线破解，如果破解成功，就能得到该用户的密码明文，这种攻击方式被称为 **AS-REP Roasting攻击**。

​    **在TGS-REP阶段**，由于ST服务票据是用服务Hash加密的。因此，如果我们能获取到ST服务票据，就可以对该ST服务票据进行利息破解，得到服务的Hash，这种攻击方式被称为**Kerberoasting攻击**。这个问题存在的另外一个因素是因为用户向KDC发起TGS_REQ请求，不管用户对服务有没有访问权限，只要TGT认购权证正确，那么KDC都会返回ST服务票据。其实AS_REQ里面的服务就是krbtgt，也就是说这个攻击方式同样可以用于爆破AS_REP里面的TGT认购权证，但是之所以没见到这种攻击方式是因为krbtgt的密码是随机生成的，爆破不出来。

### 总结

认证流程：

1. 客户端向AS服务器发送AS-REQ请求TGT
2. AS服务器验证客户端的合法性，是则返回TGT和Logon Session Key
3. 客户端拿着Logon Session Key来加密时间戳并且将TGT一起发给TGS服务器来请求服务
4. TGS服务器通过解密时间戳与验证TGT来验证客户端合法性，并通过PAC来校验客户端对于该服务的具体权限，最后返回ST服务票据和 Service Session key，（Service Session key是使用Logon Session Key加密的，用于后续通信的加密）
5. 客户端拿着ST来申请具体的服务

![img](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/20200330220851341.png)

黄金票据：如果黑客知道 krbtgt 的密码 hash 就可以伪造TGT来申请ST（服务票据），获取任意服务的权限

白银票据：如果黑客获取了域内某个用户的账号以及NTLM哈希，并且知道该用户对FTP服务拥有访问权限，就可通过该用户的 NTLM hash 伪造白银票据来访问FTP服务