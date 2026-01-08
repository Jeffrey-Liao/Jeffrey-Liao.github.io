- **作用**
	- _Send_ blocks of data (_frames_) _between physical devices_
	- Regulate access to the physical media
- **挑战**
	- How to delineate _frames_?
	- How to _detect errors_?
	- How to perform _media access control_ (MAC)?
	- How to recover from and _avoid collisions_?

# 数据帧
- **Physical layer** determines how bits are encoded
- how to encode blocks of data
	- Packet switched networks
	- Each packet includes routing information
	- Data boundaries must be known so headers can be read
- Types
	- Byte oriented protocols
	- Bit oriented protocols
	- Clock based protocols
## Byte Oriented
#### Byte Stuffing
![[Pasted image 20251227150613.png]]
- 添加一个特殊符号（FLAG）标识有效数据的开始和结束
- 问题：如果数据中存在FLAG则会导致数据范围异常
	- Add a special DLE (Data Link Escape) character before FLAG
	- What if DLE appears in the data? Add DLE before it.
- Used by **Point-to-Point protocol**(PPP)

#### Byte Counting
![[Pasted image 20251227150859.png]]
- Sender: insert **length** of the data in bytes **at the beginning of each frame**
- Receiver: **extract the length** and read that many bytes
- 问题：如果传输包长度时出现问题，该如何解决

## Bit Oriented
#### Bit Stuffing
![[Pasted image 20251227151026.png]]
添加特殊位组合在开始处和结尾处
- 当读取到设定好的连续的多个1时，在组合后面添加一个0
- **例子**：
	- 原始数据：`11111110` → 填充后变为：`111110110`（在第一个 `11111` 后插入 0）
- 当读取时读到连续的多个1时，丢弃后面紧接着的一个0
	- 当看到 `111110` → 认为这是填充的 0，**删除这个 0**，还原原数据。
	- 当看到 `111111` → 继续看下一个比特：
	    - 如果下一个是 `0` → 即 `1111110` → 表示**帧结束**
	    - 如果下一个是 `1` → 即 `1111111` → 出错！因为正常情况下不会出现这种组合 → **丢弃整个帧**
 **缺点（Disadvantage）**
- **最坏情况下有 20% 的开销**（overhead）
    - 比如每 5 个 1 就加一个 0，相当于**每 6 个比特中有 1 个是填充的**。
    - 虽然**平均开销较小**，但在极端情况下可能达到 20%。
## Clock-based Framing
### Synchronous Optical Network-SONET
- **属于物理层（第1层）协议**。
- 一种在高速光缆上传输数据的标准化协议，广泛应用于电信网络中，支持从几 Mbps 到几十 Gbps 的传输速率。
- 它采用**同步时钟机制**进行帧同步和数据传输，确保发送端与接收端严格对齐。
-  **核心思想**：
	- 使用**固定大小的帧结构**，并依靠精确的时钟同步来实现帧定界
#### 同步传输（Synchronous）
- 所有网络节点使用**统一的高精度时钟**（通常来自原子钟或 GPS）
- 避免了传统 PDH（准同步数字体系）中的滑码（slip）问题
- 支持**直接分插低速信号**（无需完全解复用整个高速流）
#### 分层速率结构
- 基础速率单位：**STS-1（Synchronous Transport Signal level-1） = 51.84 Mbps**
- 更高速率通过**字节间插复用**（byte interleaving）实现：
    - STS-3 = 3 × STS-1 = 155.52 Mbps
    - STS-12 = 12 × STS-1 = 622.08 Mbps
    - STS-48 = 2.488 Gbps
    - STS-192 = 9.953 Gbps（接近 10 Gbps）

|STS 等级|速率|
|---|---|
|STS-1|51.84 Mbps|
|STS-768|36.7 Gbps|
每个 STS-1 帧是一个**固定长度的帧**，共 **810 字节**。
- 每帧由 9 行 × 90 列组成 → `9 × 90 = 810 bytes`
- 接收端每收到 810 字节后，就会检查是否出现**起始模式（start pattern）**，以确认帧边界
#### 编码
 **编码方式：NRZ（Non-Return-to-Zero）**
- 数据位直接用电平表示：
    - `1` → 高电平
    - `0` → 低电平
- 优点：简单高效，适合高速传输
- 缺点：可能产生直流分量或长时间无跳变（导致时钟恢复困难）

 **避免长串 0 或 1：XOR 与扰码（Scrambling）**
- 为了防止连续的 `0` 或 `1` 导致接收端无法提取时钟信号（因为没有电平变化），SONET 对**有效载荷（payload）** 进行处理：
    - 将 payload 与一个特殊的 **127-bit 循环序列** 进行 **XOR 操作**（即“扰码”）
    - 这个 127-bit 模式是一个**伪随机序列**（PRBS, Pseudo-Random Binary Sequence），周期性重复

# 错误检查
## 汉明距离
- The Hamming distance between two codewords is **the number of differences between corresponding bits.**
- The Hamming distance d(000, 011) is 2 because
$$
000\;nor\; 011 = 011\:(two\;1s)
$$
- The Hamming distance d(10101, 11110) is 3 because
$$
10101\;nor\; 11110 = 01011\:(three\;1s)
$$
如果不是所有 2n 个可能的码字都被使用
> 在实际应用中，为了实现错误检测或纠正，我们往往**不使用全部可能的码字**，而只是选取其中一部分。

1. 合法码字集合 = S
	 - 所有被允许使用的码字组成的集合叫做 **S**（Set of legal codewords）
2. 完整码的汉明距离（Hamming distance of the complete code）
	- 汉明距离是指两个**等长**字符串之间**对应位不同**的数量。
3. 系统的汉明距离
	- 一个编码系统的 **汉明距离** 是指其所有合法码字对之间 **最小的汉明距离**。
$$d(S) = \min_{\substack{x,y \in S \\ x \ne y}} d(x, y)$$
其中：
- $(x,y)$：码字 x 和 y 之间的汉明距离
- $x,y∈S$：x 和 y 都是合法码字
- $x\ne y$：且不相同
- $min$：取所有这样的配对中最小的那个距离
计算每对之间的汉明距离：

|码字对|比较|不同位数|汉明距离|
|---|---|---|---|
|000 vs 011|000 vs 011 → 第2、3位不同|2|2|
|000 vs 101|000 vs 101 → 第1、3位不同|2|2|
|000 vs 110|000 vs 110 → 第1、2位不同|2|2|
|011 vs 101|011 vs 101 → 第1、2位不同|2|2|
|011 vs 110|011 vs 110 → 第1、3位不同|2|2|
|101 vs 110|101 vs 110 → 第2、3位不同|2|2|

➡️ 所以该码的 **最小汉明距离** 为：$d(S)=2$


## Parity Bits
- Add extra bits to **keep the number of 1s even**
![[Pasted image 20251227154409.png]]
- Detects 1-bit errors and some 2-bit errors
- The distance of parity bits is 2
对于一个**编码方案**（如奇偶校验码），其 **最小汉明距离** 是指：**任意两个合法码字之间的最小汉明距离**。
 对于奇偶校验码：
- 所有合法码字都满足“偶校验”或“奇校验”
- 任意两个合法码字之间，**至少有 2 位不同**
## Error control
由于噪声、干扰或硬件故障等原因，数据可能会发生**比特翻转**（如 0 变成 1 或反之）
为了确保接收端能正确理解发送的数据，网络系统需要使用 **错误控制机制**。

目标：
- 检测传输中的错误
	- **With Correction（带纠错）**
		- 接收端不仅能发现错误，还能**自动纠正**错误。
		- 这种机制称为 **前向纠错（Forward Error Correction, FEC）**
	- **Without Correction（不带纠错）**
		- 接收端检测到错误后，**不尝试纠正**，而是直接丢弃该帧。
		- 然后通过**反向机制**请求重传 —— 这就是所谓的 **Backward Error Correction（后向纠错）**
- 纠正错误或请求重传
	- Without Error Detection（无错误检测）
		- 在某些应用场景中，系统**不进行错误检测**，而是直接尝试纠正错误。
		- 通常用于对**实时性要求高**、且允许一定程度失真的场合。

|类别|是否检测错误|是否纠正错误|实现方式|典型应用|
|---|---|---|---|---|
|**Error Detection with Correction**|✅ 是|✅ 是|FEC（前向纠错）|卫星通信、存储设备|
|**Error Detection without Correction**|✅ 是|❌ 否|ARQ（重传）|TCP、文件传输|
|**Error Correction without Detection**|❌ 否|✅ 是|插值、预测、FEC|语音/视频流、实时通信|
### 前向纠错（Forward Error Correction, FEC）
- 发送端在数据中**添加冗余信息（如校验码）**，接收端利用这些信息**自动纠正**某些错误，无需重传。
-  **优点**：
    - 不需要反馈通道（单向通信即可）
    - 实时性好，适用于卫星通信、无线广播等无法重传的场景
- **缺点**：
    - 增加了开销（冗余位）
    - 只能纠正有限数量的错误
### 检错与重传（Automatic Repeat Request, ARQ）
- 发送端**发送数据 + 校验码**；**接收端**检测是否有错误
	- 若无错误 → 回复 ACK（确认）
	- 若有错误 → 回复 NAK（否定确认），请求重传
- **优点**：
    - 实现简单
    - 可以处理任意多的错误（只要重传成功）
- **缺点**：
    - 需要双向通信（反馈通道）
    - 延迟较高（等待 ACK/NAK）

### Error-correcting codes Redundancy
- 我们不发送所有可能的比特组合，而是只发送一个**预先定义的子集**——称为 **码字（codewords）**。接收端根据是否收到合法码字来判断是否有错误。
	- 一个包含$m+r$bits的帧
		- $m$ bits数据
		- $r$ redundant/检查bits
	- 词（word）
		- 指任意长度为 `n` 的二进制序列
	- 码（code）
		- 是从所有可能的词中选出的一个**子集**，称为 **码字（codewords）**。
		- 只有这些码字被允许用于传输。

|类型|内容|
|---|---|
|**所有可能的词（Words）**|8 个：000, 001, 010, 011, 100, 101, 110, 111|
|**选中的码字（Code）**|4 个：000, 011, 101, 110|
|**发送规则**|只发送这 4 个码字|
✅ 如果收到的是码字 → 认为无错
- 如收到 `000` 或 `101` → 正常，接受
❌ 如果收到的不是码字 → 推测出错
- 如收到 `001` → 它不在码字集合中 → **检测到错误**
然后，系统可以通过以下方式处理：
- **丢弃帧**（如 ARQ）
- **尝试纠正**（如使用最近的码字）
#### 纠正
**要纠正 `t` 个错误，编码的最小汉明距离必须至少为 `2t + 1`**。
![[Pasted image 20251228072002.png]]
- **两个黑方块（x 和 y）**
	- 代表两个**合法码字（valid codewords）**
	- 比如：`000` 和 `111`
- **两个圆圈（Territory of x 和 Territory of y）**
	- 以每个合法码字为中心，画出一个“领地”（territory）
	- 半径为 `t`，表示允许最多有 `t` 个比特错误
	- 圆内的粉红点代表可能的**受污染的码字**（即含有 1 到 t 个错误的版本）
- **粉红点（corrupted codewords）**
	- 是原始码字在传输过程中发生 1~t 个比特翻转后的结果
	- 接收端如果收到这些点，会认为它们属于对应中心码字的“领地”

- **距离条件：`d_min > 2t`**
	- `d_min`：两个合法码字之间的**最小汉明距离**
	- 要保证两个领地**不重叠**，必须满足：
$$
d_{min} > 2t ⇒ d_{min} \ge 2t+1
$$

####  校验和
- 发送端计算 `Data` 字节之和 → 得到 Checksum
- 接收端重新计算相同总和，并与收到的 Checksum 比较
- 如果不一致 → 表示数据出错
- 使用补码算术（Ones-complement arithmetic）
	- 对于二进制数，每一位取反（0→1, 1→0）
	- 加法时如果产生进位，需将进位加回最低位（称为“end-around carry”）
- 对某些错误不敏感（Not resilient to errors）
	- 接收端无法发现这种**比特翻转交换**的错误。
- 广泛用于TCP，UDP，和IP协议中

#### CRC Cyclic Redundancy Check，循环冗余校验
将数据视为一个**二进制多项式**，然后用一个**预定义的生成多项式（generator polynomial）** 去除它，得到的**余数**就是 CRC 校验码。
1. **选择一个生成多项式 G(x)**
	- CRC-8: `x⁸ + x² + x + 1` → `100000111`
	- CRC-16 (IBM): `x¹⁶ + x¹⁵ + x² + 1`
	- CRC-32 (Ethernet, ZIP, PNG):  
    `x³² + x²⁶ + x²³ + x²² + x¹⁶ + x¹² + x¹¹ + x¹⁰ + x⁸ + x⁷ + x⁵ + x⁴ + x² + x + 1`
2. **在原始数据后附加 r 个0**
	- `r = degree(G)`（生成多项式的最高次幂）
3.  **用模 2 除法（XOR）计算余数**
	- 将扩展后的数据除以 G(x)，取余数
4. **将余数替换掉末尾的 r 个 0**
	- 发送：`[原始数据][CRC校验码]`
![[Pasted image 20251228073947.png]]

| 错误类型       | CRC 能否检测？                                                                                                 |
| ---------- | --------------------------------------------------------------------------------------------------------- |
| 单比特错误      | ✅ 能（只要 G(x) 有至少两个非零项）                                                                                     |
| 双比特错误      | ✅ 大多数能（取决于 G(x)）                                                                                          |
| 奇数个错误      | ✅ 能（如果 G(x) 包含因子 x+1）                                                                                     |
| 突发错误（连续错误） | ✅ 非常强！  <br>• 若突发长度 ≤ r → 100% 检测  <br>• 若突发长度 = $r+1$ → 检测概率 = $1−2^{−r}$ <br>• 更长突发 → 检测概率 ≈ $1−2^{−r}$ |

| 方法                | 检错能力          | 开销         | 速度      | 适用场景         |
| ----------------- | ------------- | ---------- | ------- | ------------ |
| **奇偶校验**          | 弱（仅单比特）       | 1 bit      | 极快      | 内存、简单串口      |
| **校验和（Checksum）** | 中等            | 16 bits    | 快       | TCP/IP（历史原因） |
| **CRC**           | **强**（尤其突发错误） | 16~32 bits | 快（硬件加速） | 网络、存储、嵌入式    |
| **哈希（如 SHA）**     | 极强（防篡改）       | 160+ bits  | 慢       | 安全、完整性验证     |

#### 进行纠错检查的优劣
**Cons:**
- Error free transmission **cannot be guaranteed**
- **Not all applications want this functionality**
- Error checking adds CPU and packet size overhead
- **Error recovery requires buffering**

**Pros:**
- Potentially **better performance** than **app-level error checking**

**Data link error checking in practice**
- Most useful over lossy links
- Wifi, cellular, satellite

###  Backward error correction
基于 **自动重传请求（ARQ, Automatic Repeat reQuest）** 的错误控制策略。它依赖于接收方检测错误并通知发送方重传。
1. **错误检测在接收端完成**
- 接收方使用校验机制（如 CRC、奇偶校验等）检查每个帧是否出错。
- 如果发现错误 → 不接受该帧
- 如果正确 → 接受并向上层传递

 2. **发送方持续重传直到成功**
- 发送方发送一个帧后，等待确认（ACK）
- 若未收到 ACK 或收到 NAK（否定确认），则**重新发送该帧**
- 重复此过程，直到对方正确接收

## Model of Frame Transmission（帧传输模型）
![[Pasted image 20251228123408.png]]

![[Pasted image 20251228120946.png]]

- **Frame 2**：完全丢失（可能因噪声或缓冲溢出）
- **Frame 4**：内容混乱（garbled），即发生了比特错误
- **Frame 1, 3, 5**：正常到达
1. **接收方**通过 ARQ 机制通知发送方：
    - 要求重传 Frame 2 和 Frame 4
2. **发送方**重新发送这两个帧
3. 直到所有帧都被正确接收为止

# 基础链路层协议
## Simplex Stop-and-Wait Protocol
- 当A发送数据时，等待B的确认（ACK）后再继续发送后续数据
- After receiving the message B sends an ACK back to the sender.
- A **retransmits** the message **until it receives an ACK** from B
- If the ACK arrived, the next message may be sent.
**发送者：**
![[Pasted image 20251228123928.png]]
**接收者：**
![[Pasted image 20251228123940.png]]
![[Pasted image 20251228124147.png]]

![[Pasted image 20251228124211.png]]

Simple, but **inefficient** for _long distance_ and _high speed applications_.
We can use _sliding-window_ technique to _improve the efficiency_.
传输问题
![[Pasted image 20251228124349.png]]
## Alternate Bit Protocol
是一种最简单、经典的 **自动重传请求（ARQ, Automatic Repeat reQuest）协议**，用于在不可靠的通信信道上实现 **可靠的数据传输**。它属于 **停等协议（Stop-and-Wait ARQ）** 的一种具体实现。
>A reliable data transport over a noisy channel

- A be the **sender**
- B be the **receiver**
- ABP 通过一个 **1 位的序列号（0 或 1）** 来区分连续发送的数据帧，并要求接收方对每个帧进行确认（ACK）。
- 发送方只有在收到正确确认后，才发送下一个新帧；否则就重传当前帧。
### 帧结构
每个数据帧包含：
- 数据
- 1 位序列号（SN = 0 或 1）
每个确认帧（ACK）也包含：
- 1 位确认号（AN = 0 或 1），表示“期望收到的下一个帧的序列号”
- 现在期待 n+1（模 2）

### 发送方行为
- 初始状态：`next_frame_to_send = 0`
- 发送帧时，附带当前 `next_frame_to_send` 作为序列号
- 启动定时器，等待 ACK
- 如果：
    - 收到 **正确的 ACK**（即 ACK 号 = 当前发送的序列号）→  
        - 切换序列号（0 ↔ 1），准备发送下一帧
    - **超时未收到 ACK** 或 **收到错误/重复 ACK** →  
        - **重传当前帧**（不改变序列号）
###  接收方行为
- 维护 `expected_seq_num`（初始为 0）
- 收到帧后：
    - 如果帧的序列号 == `expected_seq_num` →  
        -  接受该帧，提交给上层  
        -  发送 **ACK(expected_seq_num)**  
        - `expected_seq_num = 1 - expected_seq_num`（切换）
    - 如果序列号 ≠ 期望值（即重复帧）→  
        -  **丢弃该帧**  
        -  **重发上次的 ACK**（避免发送方误以为丢失）

| 问题类型          | ABP 是否能处理？ | 说明              |
| ------------- | ---------- | --------------- |
| **数据帧丢失**     | ✅          | 超时重传            |
| **ACK 丢失**    | ✅          | 接收方对重复帧重发 ACK   |
| **帧损坏（比特错误）** | ✅          | 通过 CRC/校验和检测并丢弃 |
| **帧乱序**       | ❌（但不会发生）   | 因为是停等协议，一次只发一帧  |
| **重复帧**       | ✅          | 接收方通过序列号识别并丢弃   |
缺点：
- **效率低**：每次只能发一帧，必须等待 ACK（尤其在高延迟链路如卫星通信中）
- **吞吐量受限**：信道利用率 = 帧传输时间+RTT帧传输时间​
![[Pasted image 20251228144730.png]]
### 频道利用率
$\eta=\frac{T_{packet}}{(T_{packet}+d+T_{ack}+d)}$ 
其中：
$T_{packet}$: The **time needed** for the **transmission of a frame**
$(T_{packet}+d+T_{ack}+d)$: The **time elapsed** until the next frame can be transmitted

- **If the propagation delay is large, the ABP is not efficient.**
![[Pasted image 20251228150041.png]]
- The sender transmit frames continuously one after another
	- More frames are sent out, but not acknowledged.
	- Pipeline technique
## Sliding Window Protocol
1. Similar to ABP
2. But allow **multiple** **frames** to transmit
	- 发送方和接收方各自维护一个 **窗口（window）**
	- 窗口内的帧可以被发送（或接收），窗口外的不能
	- 随着 ACK 到达，窗口“滑动”向前，释放旧位置，纳入新帧
3. Each outgoing frame **contains a seq. number** from 0 to $2^n-1$.
	- So it fits in an n-bit field
	- ABP uses n=1
4. Each ACK carries the sequence number of the next expected frame by the receiver
![[Pasted image 20251228151416.png]]

###  1. **系统 A（Source System A）——发送方**
- 拥有一个 **发送窗口（Send Window）**
- 可以发送窗口内的帧
- 帧编号从 0 到 7（共 8 个序号，用 3 位二进制表示）
###  2. **系统 B（Destination System B）——接收方**
- 拥有一个 **接收窗口（Receive Window）**
- 接收并确认已收到的帧
- 同样使用 0~7 的序号
####  第一步：初始状态
- 发送方 A 发送帧 F0, F1, F2（序号 0, 1, 2）
- 这些帧被依次传送到接收方 B
- 接收方 B 成功接收到 0, 1, 2

#### 第二步：接收方发送 RR3
- 接收方 B 发送 **RR3**（Ready to Receive frame 3）
- 表示：“我已经收到了所有帧直到帧 2，现在准备接收帧 3”
- 这是一个 **接收就绪（Receiver Ready）** 信号，也叫 **ACK(3)**

#### 第三步：发送方继续发送
- 发送方 A 收到 RR3 → 知道前 3 个帧已被正确接收
- 发送方窗口滑动 → 新增帧 F3 (seq=3), F4 (seq=4), F5 (seq=5)
- 继续发送 F3, F4, F5

#### 第四步：接收方处理新帧
- 接收方 B 接收到 F3, F4, F5
- 检查顺序是否正确
- 若无误，则将这些帧放入缓冲区，并等待完整交付

#### 第五步：发送方收到 RR4
- 接收方 B 发送 **RR4**
- 表示：“我已收到帧 3，准备好接收帧 4”
- 实际上是确认帧 3 已成功接收

| 类型                        | 全称     | 特点                                     |
| ------------------------- | ------ | -------------------------------------- |
| **Go-Back-N (GBN)**       | 回退 N 步 | 接收方只按序接收；出错则丢弃后续所有帧，发送方重传从出错帧开始的所有未确认帧 |
| **Selective Repeat (SR)** | 选择性重传  | 接收方可缓存乱序帧；只重传出错或丢失的特定帧                 |
### Go-Back-N
 工作原理
- **发送窗口大小**：`W_s ≤ 2^n - 1`（n 为序列号位数）
- **接收窗口大小**：`W_r = 1`（只能按序接收）
- 超过发送窗口大小会导致**序列号混淆（sequence number ambiguity）**，从而引发**协议无法正确区分新旧数据帧**的问题，最终可能导致**数据被错误地接收或丢弃**。
1. 发送方可连续发送最多 `W_s` 个帧（无需等待 ACK）
2. 接收方：
    - 若收到**期望的帧** → 接受，并发送 ACK
    - 若收到**乱序或错误帧** → **直接丢弃**，并重发**最后一个正确 ACK**
3. 发送方：
    - 收到 ACK → 滑动窗口
    - **超时** → 重传**整个窗口内所有未确认的帧**
![[Pasted image 20251228152305.png]]
### Selective Repeat (SR) 协议
- **发送窗口大小**：`W_s ≤ 2^{n-1}`
- **接收窗口大小**：`W_r = W_s`
- 接收方可**缓存乱序但正确的帧**
1. 发送方连续发送窗口内帧
2. 接收方：
    - 若帧在窗口内且正确 → **缓存**，并发送 **该帧的 ACK**
    - 若帧重复或超出窗口 → 丢弃
3. 发送方：
    - 收到某个帧的 ACK → 标记该帧为已确认
    - **仅重传超时的单个帧**（不是全部）
![[Pasted image 20251228152316.png]]

