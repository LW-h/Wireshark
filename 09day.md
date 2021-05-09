## 无线网络数据包分析

> 由于无线网络和物理层的本质属性，数据链路层变得尤为重要。这给我们捕获和访问数据增加了限制

#### 一次嗅探一个信道

> 当从无线局域网【Wireless Local Area Network,WLAN】捕获流量时，最特殊的在于无线频谱是共享介质。无线通信的介质是客户端共享的空间。单个WLAN只占用802.11频谱的一部分。这允许同一个物理空间的多个系统在频谱不同的部分进行操作**无线网络的基础是美国电子和电气工程师协会【Institute of Electrical and Electronics Engineers,IEEE】**开发的802.11标准。

> 空间上的分离是通过将频谱划分为不同信道实现的。在美国，有11个信道可用【有些国家允许使用更多的信道】。因为WLAN同时只能操作一个信道，就意味着我们只能同时嗅探一个信道。传统的无线嗅探只能同时处理一个信道，但有一个例外：某些无线扫描应用程序使用‘调频’技术，可以迅速改变监听信道以收集更多数据。其中最流行的工具[Kismet](http://www.kismetwireless.net/),可以每秒跳跃10个信道，从而高效地嗅探多个信道。

###### 无线信号干扰

> 当有干扰时，无线信道不能保证空气中传输的数据是完整。无线网络有一定地抗干扰性，但并不完全可靠。因此，当捕获无线网络时，必须注意周围环境，确保没有大的干扰源，例如：大型反射面、大块坚硬物体、微波炉、2.4Ghz无线电话、厚墙面、以及高密度物体......这些都有可能导致数据包丢失、重复、损坏。同时也需要考虑到信道间地干扰。无线频谱被分成多个不同的传输信道，但因为频谱空间有限，信道有些许重叠，当你在一个信道捕获数据时，可能会捕获到另外一个信道的数据。

###### 检测和分析信号干扰

> 可以使用频谱分析仪来检测信号干扰。MetaGeek开发了一个叫Wi-Spy的产品，这是一个USB硬件产品，用于检测整个802.11频谱上的干扰。与MetaGeek的Chanalyzer软件搭配后，这个硬件可以输出图形化频谱，有助于解决无线网络问题。

#### 无线网卡模式

> 无线网卡共四个工作模式

+ 被管理模式【Managed mode】:当你的无线客户端直接与无线接入点(Wireless Access Point,WAP)连接时，就使用这个模式。在这个模式中，无线网卡的驱动程序依赖WAP管理整个通信过程
+ Ad hoc模式：当你的网络由互相直连的设备组成时。就使用这个模式。无线通信双方共同承担WAP的职责
+ 主模式【Master mode】:一些高端无线网卡还支持主模式。这个模式允许无线网卡使用特制的驱动程序和软件工作，作为其它设备的WAP
+ 监听模式【Monitor mode】:当你希望无线客户端停止发数据，专心监听空气中的数据包时，就使用监听模式。要使Wireshark捕获无线数据包，你的无线网卡和配套驱动程序必须支持监听模式(也叫RFMON模式)推荐实用ALFA 1000mW USB无线适配器

#### 在Windows上嗅探无线网络

> 即使你有支持监听模式的无线网卡，大部分基于Windows的无线网卡驱动也不允许你切换到这个模式(WinPcap也不支持这样)。你就需要一些额外的硬件

配置AirPcap

> AirPcap(Riverbed 旗下 CACE Technologies公司的产品)被设计用来突破Windows强加给无线数据包分析的限制。AirPcap像U盘一样，用于捕获流量。AirPcap使用WinPcap驱动和一个特制客户端配置工具

AirPcap控制面板配置

+ Interface:选择要捕获的设备，一些高级场景要求使用多个AirPcap设备，同时嗅探多个信道
+ Blink Led:这个按钮会使AirPcap设备上的LED指示灯闪烁。当存在多个AirPcap设备时，可以用来识别正在使用的适配器
+ Channel:可以选择希望监听的信道
+ Include 802.11FCS in Frames:默认情况下，一些系统会去掉无线数据包的最后4个校验和比特。这个被称为帧校验序列(Frame Check Sequence,FCS)的校验和用来确保数据包在传输过程中没有被损坏，一般勾选
+ Capture Type:两个选项：802.11Only和802.11+Radio.802.11 Only选项包含标准的802.11数据包头。802.11+Radio选项包含这个包头以及radiotap头部，包含额外信息，例如：数据率、频率、信号等级、噪声等级......可以观察到所有信息
+ FCS Filter:即便你没有选择Include 802.11 FCS in Frames,这个选项也可以过滤FCS认为已经损坏的数据包。使用Valid Frames即可只显示FCS认为成功接收的数据包
+ WEP Configuration:在(AirPcap Control Panel的Keys选项卡可见)允许你输入所嗅探网络的WEP。为了解解密WEP加密数据，需要输入正确的WEP密码

使用AirPcap捕获流量

> 启动Wireshark选择Capture->Options-->在Interface下选择AirPcap设备.除了Wireless Settings按钮外,其它没变.AirPcap是完全嵌入Wireshark的,因此所有配置都可以在Wireshark中修改

在Linux上嗅探无线网络

> 在linux系统嗅探只需要简单的启用无线网卡的监听模式,然后启动Wireshark即可.但是不同的无线网卡启动监听模式流程不一样.在Linux系统中,通过内置的无线扩展程序启用监听模式是常用的办法之一.你可以输入iwconfig查看信息.要将网卡设置为监听模式,必须进入ROOT用户:su[切换用户]-->iwconfig ethx mode monitor[开启]-->iwconfig ethx up[确保接口配置正常]-->iwconfig ethx channels[1-11]指定监听信道.你可以在监听过程中随意修改信道,也可以使用脚本自动修改

802.11数据包结构

> 无线数据包与有限数据包的主要不同在于额外的802.11头部,这是个第二层头部,包含数据包和传输介质有关的额外信息.802.11数据包有三类:

+ 管理:这些数据包用于在主机之间建立2层连接.
+ 控制:控制数据包允许管理数据包和数据包的发送并与拥塞管理有关
+ 数据:这些数据包包含真正的数据,也是唯一可以从无线网络转发到有限网络的数据包

在Packet List面板增加无线专用列

> 分析无线数据包之前,需要增加3个新的列

+ RSSI[for Received Signal Strength Indication]列,显示捕获数据包的射频信号强度
+ TX Rate[for Transmission Rate]列,显示捕获数据包的数据率
+ Frequency/Channel列,显示频率和信道

无线筛选

+ 筛选特定BSS ID:每个WAP都有它自己的识别名,叫做'基础服务设备识别码'[Basic Service Set Identifier].基于设备的MAC地址过滤:**wlan.bassid.eq MAC地址**
+ 筛选特定频率:radiotap.channel.freq == 2412(信道11对应的频率值)

无线安全

> 最初在无线中使用的加密技术是'有线等效加密'[Wired Equivalent Privacy,WEP]标准.但后来被发现了几个漏洞,虽然出现了新的WPA[Wi-Fi Protected Access]和WPA2技术.但还是出现了漏洞.