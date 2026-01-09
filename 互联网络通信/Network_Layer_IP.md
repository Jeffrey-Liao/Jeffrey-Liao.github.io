# Network Layer IP Protocol
# IPv4协议
1. **IP 数据报就像一封信** —— 它是一个**独立、自包含的单元**，不需要预先建立连接或电路。
2. **IP 是“无连接、尽力而为”的协议**，它不保证可靠交付，但确保每个包都能被单独处理和转发。
![[Pasted image 20251230211849.png]]

## 第一字内

| 字段                       | 位数    | 含义                             |
| ------------------------ | ----- | ------------------------------ |
| **Version**              | 4 bit | `4` 表示 IPv4；`6` 表示 IPv6        |
| **HLen (Header Length)** | 4 bit | 头部长度，以 **32-bit 字**为单位（即 4 字节） |
### HLen (Header Length)
四位
- 最小值：5（标准头部 20 字节）	
- 最大值：15（最多 60 字节，含 Options） 
### DSCP / ECN
 8 bit ， 原先是 TOS（Type of Service），现在用于 QoS 控制：
	- **DSCP**（Differentiated Services Code Point）：优先级标记（如 VoIP 高优先级）
	- **ECN**（Explicit Congestion Notification）：拥塞通知标志（避免丢包）
### Datagram Lengt
16 bit = 整个数据报的总长度（头 + 数据），单位：字节
	- 最大值：`2^16 - 1 = 65,535` 字节

>**IPv4 单个数据报最大只能 65,535 字节**，其中头部通常 20 字节，所以最大有效载荷约 **65,515 字节**。

原因：
- 由于 MTU 限制（如以太网 1500 字节），实际很少有这么大的包
- 超过 MTU 的包会被**分片**

---
## 第二字内

### Identifier 
 16 bit，由源主机生成，标识原始数据报 
 - 所有分片共享同一个 ID
- 用于重组时识别属于哪个原始包

### Flags 
3 bit，常用的是 M flag（More fragments）
- `M=1`：还有后续分片
- `M=0`：这是最后一个分片

### Offset
13 bit，表示该分片数据在原始数据报中的起始位置（单位：8 字节）
- 为什么是 8 字节？因为 IPv4 规定分片必须按 8 字节对齐（为了简化处理）
- 实际偏移量 = **Offset × 8**

---

## 第三字内
### TTL (Time to Live)
8 bit，每经过一个路由器减 1
- **防止无限循环的包**
- **用途**：实现 `traceroute` 命令
	- 逐步增加 TTL，观察每个跳点返回的响应
### Protocol
8 bits，用于标识上层协议类型
- `6` = TCP
- `17` = UDP
- `1` = ICMP
- `89` = OSPF
### Checksum 
16 bit，**仅对 IP 头部计算校验和**
- 用于检测头部是否损坏
- **不包括数据部分**
- 每次修改头部（如 TTL 减 1）都要重新计算
该字段在IPv6中被取消

---
## 第四字和第五字
| 名称                         | 意义                |
| -------------------------- | ----------------- |
| **Source IP Address**      | 32 bit，发送方的 IP 地址 |
| **Destination IP Address** | 32 bit，接收方的 IP 地址 |
- **理论上**，这两个地址必须在全球范围内唯一
- **现实中**，经常被违反！
    - 使用 NAT（Network Address Translation）技术
	    - NAT 设备（通常是**路由器或防火墙**）位于**私有网络与公网之间**，在数据包进出时**动态修改 IP 头部信息**。
	    1. 内网主机（如 `192.168.1.10`）发送请求到公网服务器（如 `8.8.8.8`）。
		2. NAT 设备收到包后：
		    - **将源 IP** 从 `192.168.1.10` **改为自己的公网 IP**（如 `203.0.113.5`）
		    - **记录映射关系**：`(192.168.1.10:50000) ↔ (203.0.113.5:60000)`
		3. 服务器回包给 `203.0.113.5:60000`。
		4. NAT 设备查表，将目标 IP/端口还原为 `192.168.1.10:50000`，转发给内网主机
    - 私有地址（如 `192.168.x.x`）在内部网络重复使用
    - 企业/家庭网络中多个设备共享一个公网 IP

##  Fragmentation（分片）
![[Pasted image 20260101151059.png]]

**每个网络有不同的 MTU（Maximum Transmission Unit）**
- 因此，路径上的**最小 MTU** 决定了**整个路径能传输的最大包大小**。
- 分片发生在**中间路由器**
- 重组只在**最终接收端**完成
- 不能跨路由器重组

| 步骤  | 描述                                    |
| --- | ------------------------------------- |
| 1   | 若数据报大于下一跳 MTU，则由**路由器**将其分割成多个较小的片段   |
| 2   | 每个片段都有自己的 IP 头部（但共享原数据报的 ID）          |
| 3   | 所有片段独立传输，**顺序可能不同**                   |
| 4   | 目标主机收到后，根据 ID、Offset 和 M flag 重组原始数据报 |

**举例：**
- 发送方：MTU = 4000
- 第一段网络：MTU = 2000
- 第二段网络：MTU = 1500
- 原始数据报大小：**3800 字节数据 + 20 字节头部 = 3820 字节**
- 3820 > 2000 → 必须分片！
- 每个分片都有**独立的 IP 头部**
- 两个分片**共用相同的 Identifier**
- Frag1 的 M=1，Frag2 的 M=0
### 第一次分片（MTU=2000）

| 分片    | 长度   | M   | Offset | 数据大小                 |
| ----- | ---- | --- | ------ | -------------------- |
| Frag1 | 2000 | 1   | 0      | 1980 字节（减去TCP报头20字节） |
| Frag2 | 1840 | 0   | 1980   | 1820 字节              |

### 第二次分片（MTU=1500）

Frag1（2000 字节）进入 MTU=1500 的链路 → 又要分片！

| 新分片       | 长度   | M   | Offset | 数据大小    |
| --------- | ---- | --- | ------ | ------- |
| New Frag1 | 1500 | 1   | 0      | 1480 字节 |
| New Frag2 | 520  | 0   | 1480   | 500 字节  |
Frag2（1840 字节）也需分片：

|新分片|长度|M|Offset|数据大小|
|---|---|---|---|---|
|New Frag3|1500|1|1980|1480 字节|
|New Frag4|360|0|3460|340 字节|

### 重组（Fragment Reassembly）
目标主机根据以下信息重组：

|条件|作用|
|---|---|
|**Identifier**|找到所有属于同一原始包的分片|
|**Offset**|排序分片的位置|
|**M flag**|判断是否是最后一个分片|

> ✅ 重组后恢复原始 3800 字节数据

**IPv4 的分片是为兼容异构网络而设计的妥协方案；IPv6 通过 PMTU 发现机制实现了更高效、安全的传输模型。**

![[Pasted image 20260101152042.png]]

| 分片  | Length | M Flag | Offset          | Data Size | 说明              |
| --- | ------ | ------ | --------------- | --------- | --------------- |
| 1   | 1500   | 1      | 0               | 1480      | 首个分片，M=1 表示还有后续 |
| 2   | 520    | 1      | 1480            | 500       | 中间分片            |
| 3   | 1500   | 1      | (1480+500)1980  | 1480      | 后续分片            |
| 4   | 360    | 0      | (1980+1480)3460 | 340       | 最后一个分片，M=0      |
- 每个分片都有独立的 IP 头部（20 字节）
- 所有分片共享相同的 **Identifier**
- 数据总长度 = `340 + 1480 + 500 + 1480 = 3800` 字节

#### Reassembly at Destination
> ✅ **分片重组只在最终接收端完成**
- 路由器**不会**重组分片
- 每个分片独立转发，可能走不同路径
- 目标主机负责按顺序拼接数据

#### 如何计算原始数据大小？
1. 收到分片 → 检查 Identifier 是否相同
2. 按 Offset 排序
3. 存入缓冲区
4. 等待 M=0 分片到达
5. 拼接所有分片 → 得到原始数据
6. 释放内存

#### Out-of-order fragments
分片可能乱序到达（因走不同路径）
- 必须等待所有分片才能重组

#### Duplicate fragments
- 可能重复发送（如重传、路由环路）
- 需要检测并丢弃重复项

#### Missing fragments
- 某些分片丢失 → 整个数据报无法重组
- 通常导致整个数据报被丢弃
- 上层协议（如 TCP）会重传

### Fragmentation Concepts
分片的目的：
- **让 IP 协议能够在各种 MTU 不同的网络之间无缝通信，同时保持网络的异构性和去中心化。**
#### Decentralized and heterogeneous
- 每个网络可以自由选择自己的 MTU（如以太网 1500，Wi-Fi 1500，PPP 576）
- 无需全局统一标准
- 体现了 DARPA 设计原则：“允许异构网络共存”

#### Connectionless datagram protocol
- 每个分片都携带**完整**的源/目标地址和路由信息
- 分片可以**独立传输**，甚至走不同路径
- **不依赖**预先建立的连接或电路
- 这正是 **“尽力而为”（Best-effort）网络** 的体现

#### Best effort network
- 路由器或接收端**可能静默丢弃**分片
    - 如缓冲区满、MTU 不足、TTL 超时
- **不需要通知发送方**
    - 无反馈机制
    - 错误处理交给上层协议（如 TCP）

#### Most work is done at the endpoints
- 分片由**路由器**执行（当需要时）
- 但**重组**完全由**目标主机**完成
- 源主机也可能参与（如 PMTU 发现）

### Fragmentation in Reality
1. Fragmentation is **expensive**
	- Memory and CPU overhead for datagram reconstruction
	- Want to **avoid fragmentation if possible**
2. MTU discovery protocol
	- Send a packet with “don’t fragment” bit set
	- Keep **decreasing message length until one arrives**
	- **May get “can’t fragment” error from a router**, which will explicitly state the supported MTU
3. Router handling of fragments
	- **Fast**, specialized hardware handles the common case
	- **Dedicated**, general purpose CPU just for handling fragments
### ARP（Address Resolution Protocol，地址解析协议）
是 TCP/IP 协议栈中一个**关键的底层协议**，工作在 **数据链路层（Layer 2）和网络层（Layer 3）之间**，其核心作用是：
主机向**整个局域网广播**一个 ARP 请求帧
- **ARP 载荷**：
    - 发送方 IP：`192.168.1.5`
    - 发送方 MAC：`AA:BB:CC:DD:EE:FF`
    - 目标 IP：`192.168.1.10`
    - 目标 MAC：`00:00:00:00:00:00`（未知，填全 0）
- 只有 IP 为 `192.168.1.10` 的主机会响应。
- 它向请求方**单播**发送 ARP 回复

#### 更新 ARP 缓存
- 请求方收到回复后，将 `(192.168.1.10 → 11:22:33:44:55:66)` 存入 ARP 缓存。
- 后续通信可直接使用该 MAC 地址，无需再广播。

> **通过已知的 IP 地址，动态获取对应的 MAC 地址（物理地址）**，以便在局域网（LAN）中正确封装和传输以太网帧。
# IPv4地址空间危机
Problem: the IPv4 address space is too small
 - $2^{32} = 4,294,967,296$possible addresses
- Less than one IP per person

Parts of the world have already run out of addresses

|Region|Regional  Internet Registry (RIR)|Exhaustion Date|
|---|---|---|
|Asia/Pacific|APNIC|April 19, 2011|
|Europe/Middle East|RIPE|September 14, 2012|
|North America|ARIN|13 Jan 2015 (Projected)|
|South America|LACNIC|13 Jan 2015 (Projected)|
|Africa|AFRINIC|17 Jan 2022(Projected)|


# IPv6
First introduced in 1998
- 虽然正式标准化于1998年（RFC 2460），但实际部署缓慢
- 目标是解决 IPv4 地址耗尽问题
## 特性
### 地址
- 128-bit addresses
- $4.8 * 10^{28}$ addresses per person

### 地址格式
IPv6 地址由 **8 组 16 位十六进制值组成**，用冒号 `:` 分隔。
`2001:0db8:0000:0000:0000:ff00:0042:8329`
##### 简化规则：
**省略前导零**
- 每组中的前导零可以省略
- 如 `0000` → `0`
- 所以变成：`2001:0db8:0:0:0:ff00:42:8329` 
**连续零组用双冒号 `::` 替代**
- 只能使用一次
- 如 `0000:0000:0000:0000` → `::`
- 所以最终简化为：`2001:0db8::ff00:42:8329` 

### 本地回环地址
**`::1`** —— IPv6 中的本地回环地址
- 所有 128 位中，127 位是 0，最后一位是 1
- 即：`0000:0000:0000:0000:0000:0000:0000:0001`
- 指向本机自身

## 报头格式
| 字段                           | 位数      | 说明                                            |
| ---------------------------- | ------- | --------------------------------------------- |
| **Version**                  | 4 bit   | 值为 6，标识 IPv6                                  |
| **Traffic Class / DSCP/ECN** | 8 bit   | 类似 IPv4 的 TOS，用于 QoS 控制                       |
| **Flow Label**               | 20 bit  | 新增字段，用于标识特定流量流（如实时视频）                         |
| **Payload Length**           | 16 bit  | 数据部分长度（不含头部），单位字节                             |
| **Next Header**              | 8 bit   | 类似 IPv4 的 Protocol 字段，指示下一个头（如 TCP、UDP、ESP 等） |
| **Hop Limit**                | 8 bit   | 类似 TTL，每跳减 1，超时则丢弃                            |
| **Source IP Address**        | 128 bit | 发送方地址                                         |
| **Destination IP Address**   | 128 bit | 接收方地址                                         |
320 位 = 40 字节（固定长度）

### 与v4的区别
| 字段                            | 原因                          |
| ----------------------------- | --------------------------- |
| **Header Length**             | 固定 40 字节 → 不再需要             |
| **Checksum**                  | 在现代链路中已不必要（物理层更可靠），且增加路由器负担 |
| **Identifier, Flags, Offset** | **IPv6 不支持中间路由器分片**         |
- 分片任务交给**源主机**完成
- 使用 **PMTU Discovery（Path MTU Discovery）** 技术避免分片
	1. 源主机发送一个 DF（Don’t Fragment）标志的数据包
	2. 若路径上某链路 MTU 不足，路由器返回 ICMP “Packet Too Big” 消息
	3. 源主机调整 MSS，重新发送较小包

## Performance Improvements
### No checksums to verify
- IPv6 头部**没有校验和字段**
- 依赖链路层（如以太网）或传输层（TCP/UDP）做错误检测
- **减少路由器处理开销**，提升转发速度

### No need for routers to handle fragmentation
- 路由器不再负责分片
- 所有分片操作由**源主机**完成
- 减少中间节点的计算负担

### Simplified routing table design
- IPv6 地址空间巨大（$2^{128}$）
- 但支持**层次化聚合**（Hierarchical Aggregation）
- 允许大规模子网划分（如 `/64` 子网）
	- 标准子网大小为 $2^{64}$ 个地址（即 `/64`）
	- 可容纳整个局域网（LAN）甚至城市级网络
	- 支持自动配置和无状态地址分配（SLAAC）

### Simplified auto-configuration
- IPv6 支持 **Neighbor Discovery Protocol (NDP)**，取代 ARP
- 主机可通过 NDP 自动发现邻居、获取网络 ID
- 支持 **Stateless Address Autoconfiguration (SLAAC)**
    - 主机根据网络前缀 + 随机生成的接口 ID 构造自己的 IPv6 地址
	    - 网络前缀：`2001:db8::/64`
		- 接口 ID：`fe80::1a2b:3c4d`
		- 自动生成地址：`2001:db8::1a2b:3c4d`
    - 不需要 DHCP 服务器（可选）


## IPv6 的额外功能
### Source Routing（源路由）
**主机指定数据包应走的路径**
- 在传统 IP 中，路由由中间路由器决定
- 源路由允许发送方在数据包中嵌入**显式路径列表**
- 路由器按此路径转发，而非自主选择
**实现方式：**
- 使用 **Routing Header（路由头）** 扩展头
- IPv6 支持两种类型：Type 0 和 Type 2
- 类型 0 已弃用，类型 2 用于移动 IPv6
### Mobile IP（移动 IP）
**主机可以在不同网络间移动时保持 IP 地址不变**
- 核心思想：**IP 地址 = 身份标识**，不应随位置改变
- 移动节点（MN）拥有一个**永久地址**（Home Address）
- 当它连接到外地网络时，获得一个**临时地址**（Care-of Address）
**工作流程：**
1. MN 移动到新网络 → 获取 CoA
2. 通知家乡代理（Home Agent）自己的当前位置
3. 家乡代理截获发往 MN 的数据包
4. 将数据包封装并发送到 CoA
5. MN 接收后解封装，继续处理

### Privacy Extensions（隐私扩展）
**随机生成主机标识符以增强隐私保护**
- IPv6 地址通常由网络前缀 + 接口 ID 构成
- 若接口 ID 使用 MAC 地址（如 EUI-64），则暴露硬件信息
- **临时地址（Temporary Addresses）**
    - 主机定期生成随机的接口 ID
    - 每个地址有生命周期（如几天）
    - 自动替换旧地址
```
fe80::aabb:ccdd:eeff:1234   ← 固定（基于 MAC）
2fe80::1a2b:3c4d:5e6f:7890   ← 随机（隐私扩展）
```

### Jumbograms（巨帧）
**支持高达 4GB 的数据报**
- IPv6 允许**超大分组**（Jumbo Frames），最大可达 $2^{32}−1$ 字节 ≈ 4GB
- 但需双方都支持 Jumbo Frame 才能使用
- 通常用于科研、高性能计算、大规模文件传输

限制：
- 大多数网络仍受限于 MTU（如 1500 字节）
- 不能跨不支持的链路传输
- 实际应用较少，主要用于特定场景

实现方式：
- 使用 **Jumbo Payload Option** 在扩展头中指定长度
- 必须设置 `Hop Limit` 为 1（防止无限传播）

### 部署的挑战
![[Pasted image 20260101163513.png]]
#### Switching to IPv6 is a whole-Internet upgrade
- 所有设备必须更新：
    - **所有路由器**
    - **所有主机**（PC、手机、服务器）
    - **所有中间件**：ICMPv6、DHCPv6、DNSv6
- 任何一环缺失都会导致通信失败
#### Progress is slow
- 数据显示：
    - **2013年**：Google 流量中仅 **0.94%** 是 IPv6
    - **今天**（约 2024 年）：约为 **2.5%**
- 虽然有所增长，但仍远低于预期
原因：
- 缺乏紧迫感（NAT 延缓了 IPv4 耗尽）
- 迁移成本高（需要重新配置网络）
- 企业担心兼容性问题

### Transitioning to IPv6
| 层级                         | 状态                      |
| -------------------------- | ----------------------- |
| **家庭网络**（Home Network）     | 多数支持 IPv6（路由器、Wi-Fi、手机） |
| **企业网络**（Business Network） | 也普遍支持 IPv6              |
| **互联网核心**（Core Internet）   | 仍以 IPv4 为主，难以大规模升级      |
- IPv4 核心网络**无法直接路由 IPv6 数据包**
- 即使两端支持 IPv6，也无法通信
- **必须采用过渡技术**才能实现互通

### Transition Technologies
#### 隧道技术（Tunneling）
> **Use tunnels to encapsulate and route IPv6 packets over the IPv4 Internet**

- 将 IPv6 数据包**封装**在 IPv4 数据包内
- 作为“乘客”穿越 IPv4 网络
- 到达目的地后再解封装

#### 常见过渡技术
1. **6to4**
	- 使用 `2002::/16` 前缀，将 IPv6 地址映射为 IPv4 地址
		- 例如：`2002:c0a8:101::1` 对应 `192.168.1.1`
		- 适用于小型组织或个人用户
2.  **IPv6 Rapid Deployment (6rd)**
	- 由运营商提供，基于 6to4 的改进版
		- 更高效，支持大规模部署
		- 常用于电信网络
3. **Teredo**
	- 专为 NAT 环境设计
		- 允许 IPv6 客户端穿过 NAT 设备
		- 使用 UDP 封装 IPv6 包
		- 已逐渐被淘汰（因性能差、安全性低）
4. **ISATAP**
	- 用于企业内部网络
		- 将 IPv6 数据包封装在 IPv4 上，供 LAN 内部使用
5. **Dual Stack**
	- 最佳方案：同时运行 IPv4 和 IPv6
		- 设备双栈，自动选择最优路径
6. **NAT64 / DNS64**
	- 让 IPv6 客户端访问 IPv4 服务器
	- 通过翻译将 IPv6 地址转为 IPv4 地址
最终目标是**全面双栈（Dual Stack）或纯 IPv6 网络**