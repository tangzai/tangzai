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

### 1. Kerberos基础

Kerberos实际上是一种基于票据（Ticket）的认证方式。客户端要访问服务器的某个服务，首先需要购买服务端认可的 **ST服务票据(Service Ticker)**。也就是说，客户端在访问服务之前需要预先买好票，等待服务验票之后才能入场。但是这张票并不能直接购买，需要一张 **TGT认购权证**（Ticket Granting Ticket)。也就是说，客户端在买票之前必须先获得一张TGT认购权证。**TGT认购权证** 和 **ST服务票据** 均由KDC(密钥分发中心)发售，而 KDC 又是由域控担任，所以说 TGT 和 ST 均是由域控发放。

| 简写 | 全拼                                               |
| :--- | :------------------------------------------------- |
| DC   | Domain Controller，域控                            |
| KDC  | Key Distribution Center：密钥分发中心，由域控担任  |
| AD   | Active Directory：活动目录，里面包含域内用户数据库 |
| AS   | Authentication Service：认证服务                   |
| TGT  | Ticket Granting Ticket：TGT认购权证，由AS服务发放  |
| TGS  | Ticket Granting Service：票据授予服务              |
| ST   | Service Ticket：ST服务票据，由TGS服务发放          |

这里说一下 **krbtgt** 用户，该用户是在创建域时系统自动创建的一个账号，其作用是密钥发行中心的服务账号，其密码是系统随机生成的，无法正常登陆主机。

![image-20231031210050641](Kerberos%E8%AE%A4%E8%AF%81%E8%AF%A6%E8%A7%A3.assets/image-20231031210050641.png)

Kerberos协议有两个基础认证模块：AS_REQ & TGS_REP 和 TGS_REQ & TGS_REP，以及微软扩展的两个认证模式 S4U 和 PAC，S4U是微软为了实现委派而扩展的模块，分为 S4U2Self 和 S4U2Proxy。在Kerberos最初设计的流程只说明了如何证明客户端的真实身份，但是并没有说明客户端是否有权限访问该服务，因为在域中不同权限的用户能访问的资源是不同的。因此微软为了解决权限这个问题，引入了PAC（Privilege Attribute Certificate，特权属性证书）的概念

### 2. PAC特权属性证书

PAC(Privilege Attribute Certificate，特权属性证书)。其中所包含的是各种授权信息、附加凭据信息、配置文件和策略信息等。例如用户所属的用户组，用户所具有的权限等。在最初的RFC1510中规定的标准Kerberos认证过程中并没有PAC，微软在自己的产品中所实现的Kerberos流程加入了PAC的概念，因为在域中不同权限的用户能够访问的资源是不同的，因此微软设计PAC用来判别用户身份和权限

在一个正常的Kerberos认证流程中，KSC返回的TGT认购权证和ST服务票据中都说带有PAC的，这样做的好处就是以后对资源的访问中，服务端再接收到客户请求的时候不需要借助KDC的帮助提供完整的授权信息来完成对用户权限的判断，而只需要根据请求中包含的PAC信息直接与本地资源的ACL相比叫做出裁决

