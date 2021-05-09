## 安全领域数据包分析

> 当入侵分析师检查来自可疑入侵者的网络流量，或取证人员调查恶意软件在主机上的感染程度时，都会使用到数据包分析的方法。将涉及网络侦察、重定向恶意软件的流量

#### 网络侦察

> 攻击者采取的第一步行动就是深入研究目标系统。也叫做‘网络踩点’。通过公开或私密的渠道完成。之后攻击者就可以扫描指定目标。扫描的目的：1.确定目标是否在线并可到达；2.告诉攻击者目标开放了哪些端口。

###### SYN扫描

> 首先对系统做TCP SYN扫描，又称为隐秘扫描/半开扫描。最常见扫描类型

+ 快速可靠
+ 在所有平台上都很准确，与TCP协议栈的实现无关
+ 比其它扫描技术更安静，不易被发现	TCP/SYN扫描依赖于三次握手过程，可以确定目标主机的哪些端口是开放的。**攻击者发送TCP SYN数据包到受害者的端口，就像在这些端口上建立用于正常通信的连接似的。如果受害者服务正在监听的端口收到了SYN数据包，它将向攻击者回复一个TCP/SYN/ACK数据包，这样攻击者就知道这个端口是开放的。**如果攻击者没有收到SYN/ACK数据包，收到RST数据包表示端口关闭，当没有收到任何信息。可能是被中间设备过滤或传输中丢包。推荐扫描工具：[NMAP](http://www.nmap.com/download.html),是Fyodor创立的网络扫描程序。常见的扫描会话过程：【1】.5个数据包，攻击者的SYN数据包-->受害者SYN/ACK数据包-->受害者3个重传SYN/ACK；【2】.2个数据包，一个攻击者初始SYN数据包-->受害者RST数据包；【3】.一个数据包，意味着受害者主机并没有响应初始SYN包。

#### 操作系统指纹术

> 了解目标操作系统对攻击者有极大价值。这将确保攻击者实施具有针对性的攻击手段。同时这个信息也有助于攻击者成功进入系统之后，在目标文件系统找到关键文件和目录

> 操作系统指纹术是指在没有物理接触的情况下，来确定机器运行的操作系统的一组技术。主要分为两类：

+ 被动式
+ 主动式

被动式指纹技术

> 被动式指纹技术是通过目标机器回送的数据包的某些域来确定目标使用的操作系统。你只监听目标主机发送的数据包，但并不主动向目标发送任何流量。对黑客而言，这是最理想的技术，非常隐秘

> 由于RFC文件定义的协议并未规定全部技术参数的值，虽然TCP、UDP、IP头部各个域都有特定的含义，但并没有定义这些域的默认值。这意味这不同操作系统实现的TCP/IP协议栈都必须为这些域定义它自己的默认值。[p0f](https://lcamtuf.coredump.cx/p0f3/)工具使用了操作系统指纹识别技术。该工具分析捕获数据包的相关域，然后输出可能的操作系统。你不仅可以了解操作系统架构，有时甚至能了解适当的版本号或者补丁级别

主动式指纹技术

> 它是指攻击者主动向受害者发送特意构造的数据包以引起响应。然后从响应包获取操作系统版本，由于这种方法直接与受害者通信，所以并不隐秘，但是效率非常高效。Nmap使用的主动式操作系统指纹技术十分复杂。想系统了解可以看<<Nmap Network Scanning>>,这本书是Nmap扫描器作者Gordon 'Fyodor' Lyon所著

###### 漏洞利用

> 攻击者已近研究并侦察好目标，并发现了利用的漏洞，期望从此进入系统。漏洞利用程序在线路上传输信息时通常都编码成不可识别，以避免网络IDS发现。对于尝试防御网络未知威胁的人而言，基于恶意流量样本创建流量签名是非常重要的一步.

#### ARP缓存中毒攻击

> ARP缓存中毒攻击是网络工程师实用的工具。但它也是个非常致命的中间人攻击【man-in-the-middle,MITM】方法。在MITM攻击中，攻击者重定向两台主机间的流量，试图在传输过程中对流量进行拦截或修改。MITM攻击方式多种，包括会话劫持、DNS欺骗以及SSL劫持

> ARP缓存中毒攻击之所以有效，是因为特意构造的ARP数据包使两台主机相信它们是在进行互相通信，而实际它们却是与中间转发数据包的第三方通信 。

> 一位入侵分析师分析IDS报警流量时应遵循的步骤：

+ 查看报警触发它的签名
+ 在恰当的上下文中确定签名确实在流量中
+ 查看流量，找出攻击者对受害者动了什么手脚
+ 在受害者泄露更多敏感信息之前控制局面