# Data Link Layer-MAC
# Communication channels and piggybacking
1. Simplex（单工）
	- **定义**：通信只能在一个方向上进行。
	- **例子**：电视广播、无线电广播（观众只能接收，不能发送）。
2. Half-duplex（半双工）
	- **定义**：可以双向通信，但同一时间只能在一个方向上传输数据，不能同时收发。
	- **例子**：对讲机（说话时不能听，听的时候不能说）
3. Full-duplex（全双工）
	- **定义**：可以同时在两个方向上传输数据。
	- **例子**：电话通话、现代以太网（Ethernet）连接。
**之前的协议假设**
- 上层（如网络层）使用的是**单工信道**（simplex channel）。
- 物理层使用的是**半双工或全双工信道**（(half-)duplex channel）。
- 这意味着：上层只能单向发送数据，而底层支持双向传输。

当上层也具备双向通信能力时，有以下两种方式处理数据和确认信息（ACK）：
1. 分开传输
	- 数据包和确认信息分别在两个方向上传输。
	- 比如：A 发送数据给 B，B 回复 ACK 给 A，各自独立传输。
2. 使用 Piggybacking（捎带）
	- **定义**：将确认信息“附带”在反方向发送的数据包的头部中，从而减少单独发送 ACK 的开销。
	- **具体做法**：
	    - 当一方要发送数据时，如果它之前收到了对方的数据，就可以把对该数据的确认（ACK）放在自己发送的数据包的**头部**里一并发送回去。

## 以太网帧
![[Pasted image 20251228201018.png]]

| 字段                             | 长度（字节）    | 说明                                                                                                                                            |
| ------------------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **前导码（Preamble）**              | 7         | `10101010...` 交替比特，用于接收方同步时钟（**物理层处理，通常不计入帧内**）                                                                                               |
| **帧起始定界符（SFD）**                | 1         | `10101011`，表示帧开始（**也属物理层，常与前导码合并为 8 字节同步序列**）                                                                                                 |
| **目的 MAC 地址（Destination MAC）** | 6         | 接收设备的硬件地址（48 位）                                                                                                                               |
| **源 MAC 地址（Source MAC）**       | 6         | 发送设备的硬件地址（48 位）                                                                                                                               |
| **类型（Type）**                   | 2         | 指明上层协议类型（如 `0x0800` = IPv4，`0x0806` = ARP，`0x86DD` = IPv6）                                                                                    |
| **数据（Payload）**                | 46 ~ 1500 | 上层协议数据（如 IP 包）。**最小 46 字节**（不足需填充），**最大 1500 字节（MTU）**                                                                                        |
| **帧校验序列（FCS）**                 | 4         | CRC-32 校验码，用于检测传输错误<br><br>用于 **校验整个以太网帧的内容**，**不包括前导码（Preamble）和帧起始定界符（SFD）**，但**包括目的 MAC 地址、源 MAC 地址、长度/类型字段、数据（Payload）以及可选的填充（Padding）**。 |
|                                |           |                                                                                                                                               |
# MAC和传输协议
## What is Media Access?
- **Ethernet** and Wifi **are** both **multi-access technologies**（**以太网（Ethernet）** 和 **WiFi** 都采用**共享广播介质**）
    - **物理层特性**：所有设备连接在同一个物理媒介上
    - **广播性质**：一个节点发送的数据，所有其他节点都能接收到
    - **冲突域**：在同一个冲突域中的所有设备共享带宽
- Simultaneous transmissions cause **collisions**(冲突)
	- This destroys the data
- **Media Access Control** (MAC) protocols are required
	- **Rules** on how to share the medium
	- Strategies for **detecting**, **avoiding**, and **recovering** from **collisions**

## MAC 协议的主要功能

1. **介质访问控制**  
    决定设备何时可以访问共享信道。
2. **帧封装与寻址**  
    在数据帧中添加 **源 MAC 地址** 和 **目的 MAC 地址**（48 位硬件地址）。
3. **差错检测**  
    通过 FCS（帧校验序列）检测传输错误（虽然不纠错，但可丢弃错误帧）。
4. **冲突处理（针对某些协议）**  
    如 CSMA/CD 中的冲突检测与重传机制。

## Strategies for Media Access
1. Channel **partitioning**
	- Divide the **resource into small pieces**
	- Allocate each piece to one host
		- Example: Time Division Multi-Access (TDMA) cellular
		- Example: Frequency Division Multi-Access (FDMA) cellular
2. Taking turns
	- Tightly coordinate shared access to avoid collisions
		- Example: Token ring networks

### Channel Partitioning
1. Frequency Division Multiplexing **频分多路复用**
	 - 将物理信道的总带宽划分为多个不同的**频率子信道**，每个用户或设备占用一个固定的频率范围进行通信。
2. Time Division Multiplexing **时分多路复用**
	- 时间划分为固定的时间片（time slots），每个用户轮流在自己的时间片内发送数据。
		- 所有用户共享同一频率信道。
		- 通信是**按时间顺序交替进行**的。
		- 每个用户获得周期性的时间片来发送数据。

 - **局限性**：
	- **灵活性差**：无法适应突发性或变化的数据流量。
	- **资源浪费**：当某个用户不活跃时，其分配的带宽仍被保留，造成浪费。
	- **不适合现代网络**：现代互联网应用（如视频、网页浏览）具有高度动态性和突发性，因此 FDM 和 TDM 已逐渐被更灵活的技术（如统计复用、包交换）取代。

### 突发性流量
- If there’s huge difference between the **peak rate** and the **mean (or average) rate**
- 数据传输在时间上**不均匀**、**间歇性集中爆发**的流量模式。
	- 大部分时间几乎没有数据发送（空闲期），
	- 偶尔短时间内产生大量数据（突发期）。
![[Pasted image 20251228204943.png]]
### Static Channel Allocation
在通信开始前，为每个用户**预先固定分配**一定的信道资源（如频率带宽、时间片、时隙等），在整个通信过程中**不随实际需求变化**。
- 频分多路复用（FDM）
- 时分多路复用（TDM）

|场景|说明|
|---|---|
|**用户有数据要发，但分配的资源不足**|突发流量可能超过预分配带宽，导致**排队、延迟、丢包**。|
|**用户没有数据要发，但资源仍被占用**|分配的频段或时间槽空闲，**其他活跃用户无法使用**，造成**带宽浪费**。|

- A source has **data rate** of **p** bps
- The **capacity** of the link is **C** bps
- The **delay** is **T**
- Dividing the channel into N static **subchannels**
	- With sending rate $p/N$ bps and capacity $C/N$
	- The delay is $N*T$

### 局域网和城域网中的动态信道分配(Dynamic Channel Allocation in LANs and MANs)
1. **Station Model（站点模型）**
	   - 网络中有 **N 个终端/主机**（terminals/hosts），它们都连接到同一个共享通信信道。
    - 每个主机都会**随机生成数据帧（frame）**，用于发送数据。
    - 这是一个理想化的随机流量模型，用来模拟真实网络中突发、不可预测的数据产生行为。
2. **Single Channel Assumption（单信道假设）**
	- 用于传统以太网（Ethernet）、Wi-Fi 等广播式网络。
	 - 所有主机共用**一条单一的通信信道**（如以太网总线、无线信道等）。
	- 所有通信都在这条信道上进行，没有独立的专用线路。
	- **特点**：
		- 主机之间**共享带宽**。
	    - 需要一种机制来决定谁何时可以使用信道。
3. **Collision Assumption（碰撞假设）**
	-  如果两个或多个主机**同时传输帧**，它们的信号会在信道上**重叠**，导致接收端收到的是混乱、无法解析的信号
	- 这种现象称为 **collision（碰撞）**。
	- 发生碰撞后，所有相关帧都会损坏，必须重新发送
	- **影响**：
	    - 降低网络吞吐量。
	    - 增加延迟。
	    - 必须设计机制检测并处理碰撞。
4. **Continuous Time VS Slotted Time（连续时间 vs 分时槽时间）**
	- **连续时间（Continuous Time）**：
	    - 时间是连续的，任何时刻都可以开始传输。
	    - 更接近现实，但分析复杂。
	    - 例如：CSMA/CD 协议就是基于连续时间模型。
	- **分时槽时间（Slotted Time）**：
	    - 将时间划分为固定长度的“时隙”（slot）。
	    - 只能在时隙开始时发送数据。
	    - 简化了分析，便于理论建模。
	    - 例如：**Slotted ALOHA** 协议就采用这种模型。
5. **Carrier Sense VS No Carrier Sense（载波侦听 vs 无载波侦听）**
	- **Carrier Sense（载波侦听）**：
	    - 在发送前，先**监听信道是否空闲**。
	    - 如果信道忙，则等待；如果空闲，则尝试发送。
	    - 目的是减少碰撞概率。
	    - 示例：**CSMA（Carrier Sense Multiple Access）** 协议。
	- **No Carrier Sense（无载波侦听）**：
	    - 不检查信道状态，直接发送数据。
	    - 容易发生碰撞。
	    - 示例：**ALOHA** 协议（纯 ALOHA 或 slotted ALOHA）。

### Efficiency measurement 
1. Throughput (S)
	- Number of packets/frames transmitted in a time unit (successfully)
2. Delay
	- The time needs for transmitting a packet
3. Fairness
	- All the terminals are treated as equals
#### Throughput
- Offered load (G)
	- `G` 表示在单位时间内，**系统需要处理的数据包数量**。
	- 也可以理解为**输入到系统的总流量**（即用户发送的数据请求）。
	- 单位通常是“数据包/时间单位”。
	- 如果 `G > 1`，表示系统**过载（overloading）**：  
	    - 每个时间单位内要处理的数据包数超过了系统能处理的最大能力（假设发送一个包需 1 时间单位），导致排队、延迟甚至丢包。
	- 如果 `G ≤ 1`，系统可以处理所有到达的包，不会发生拥塞。
- Throughput (T)
	- `S` 是指**单位时间内成功发送出去的数据包数量**。
	- 它反映了系统的实际有效输出能力。
- 理想协议的目标是尽可能让 `S` 接近 `G`，即不浪费资源、无碰撞、无等待。

| 条件         | 吞吐量 S                      |
| ---------- | -------------------------- |
| 如果 `G < 1` | `S = G`（系统可以处理全部输入）        |
| 如果 `G ≥ 1` | `S = 1`（系统最大只能每单位时间发 1 个包） |

|概念|含义|特性|
|---|---|---|
|**Offered Load (G)**|输入到系统的数据包速率|G > 1 → 过载；G ≤ 1 → 可处理|
|**Throughput (S)**|成功发出的数据包速率|理想情况下 S = min(G, 1)|
|**理想协议**|在任何负载下都能实现最优吞吐|S = G（当 G < 1），S = 1（当 G ≥ 1）|

### Contention MAC Goals
1. 共享信道（Share the medium）
	- 如果两个host同时发送数据则会导致**冲突（collision）** 和**干扰（interference）**，导致接收端无法正确解析。
	- 如果**没有任何主机发送**，信道就会处于**空闲状态（idle）**，造成资源浪费。
	- 在任意给定时间，**只有一个主机发送数据**，避免冲突，同时避免信道空闲。
2. 高利用率（High utilization）
	- **竞争型 MAC 的优势**：
		- 允许所有主机**按需**使用信道。
		- 当某台主机有数据时，它可以尝试发送。
		- 这样可以**动态适应流量变化**，提高整体信道利用率。
3. 简单且**分布式**的算法（Simple, distributed algorithm）
	- 竞争型 MAC 不依赖中心控制器或复杂的协调机制，而是通过**分布式决策**来实现资源共享。
	- 多个主机之间**不能直接协调**（例如没有中央调度器），只能通过监听信道行为来判断是否可以发送。
	- 使用**简单的规则**（如“先听后说”、“检测冲突后重试”）进行通信。
	- 避免使用复杂的**令牌传递（token-passing）机制**（如 Token Ring），因为这些机制需要维护令牌顺序，容易出错且复杂。

| 类型                              | 是否需要协调     | 利用率     | 复杂度 | 适用场景      |
| ------------------------------- | ---------- | ------- | --- | --------- |
| **竞争型 MAC**（如 CSMA）             | 分布式、无中心协调  | 高       | 低   | 以太网、Wi-Fi |
| **非竞争型 MAC**（如 TDMA、Token Ring） | 需要预分配或令牌传递 | 低（可能空闲） | 高   | 卫星通信、工业控制 |


#### 竞争型协议的分类
##### ALOHA
- **工作方式**：
    - 主机一旦有数据要发送，就**立即发送**（immediately transmit），无需等待或侦听。
    - 不关心信道是否空闲，也不检测是否有其他设备在发送。
- **冲突处理**：
    - 如果发生碰撞（两个包重叠），接收端无法正确解析 → 数据丢失。
    - 发送方会**在稍后随机时间重新发送**该数据包（retransmit later）。
- **协议内容**：
	- Stations transmit data immediately
	- Receivers ACK all packets
	- No ACK = collision, **wait a random time then retransmit**
##### Slotted ALOHA
在 ALOHA 的基础上引入了**时间槽（time slots）**，将时间划分为固定长度的时隙。
- **工作方式**：
    - 所有主机只能在**每个时隙的开始时刻**发送数据。
    - 每个数据包必须完整地放在一个时隙内传输。
- **优点**：
	- 冲突显著减少
	- 最大吞吐量提升到约 **36.8%**（比 ALOHA 提高一倍）

##### Carrier Sense Multiple Access (CSMA)
- **工作方式**：
    - 如果信道是**空闲的**（idle），则可以开始发送。
    - 如果信道正在被使用（busy），则等待一段时间再尝试。

##### CSMA / Collision Detection (CSMA/CD)
- **核心改进**：
    - 在 CSMA 基础上增加了**冲突检测机制**（Collision Detection）。
- **工作方式**：
    - 先侦听信道是否空闲 → 空闲则发送。
    - **在发送过程中持续监听信道**。
    - 如果检测到冲突（信号强度异常或收到干扰），则**立即停止发送**，并发送一个“jam signal”（填充信号）通知其他主机发生了冲突。
    - 然后等待一段随机时间后重试。

## Performance analysis
### Poisson Process
**泊松过程（Poisson Process）** 是一个用于描述**随机事件发生时间序列**的数学模型，特别适用于模拟**独立、随机到达的事件**
它是**排队系统分析的基础工具**，常用于评估网络延迟、吞吐量、拥塞概率等性能指标。

核心假设：在短时间内，事件的发生是**随机且独立**的，且满足两个关键性质：
1. 短时间间隔内的到达概率与时间成正比（Memory-less Property）
	- 在一个非常短的时间间隔 $Δt$ 内，发生一次到达的概率为：
$$
P(one\;arrival) = \lambda \Delta t+o(\Delta t)
$$
	- 其中：
		- $λ$ 是**平均到达率**（单位时间内期望到达的次数，如包/秒）
		- $o(\Delta t)$ 表示比 $Δt$ 更高阶的小量（当 $Δt→0$ 时趋于 0）
	- 到达的概率与时间长度成正比。
	- 时间越长，发生的可能性越大；
2. 短时间内不可能有多个到达（多重到达概率趋近于零）
	 - 在极短的时间 $Δt$ 内，发生**两个或更多个事件**的概率趋近于 0
$$
P(multiple\;arrivals\;in\;\Delta t) \to 0 \; as\;\Delta t \to 0
$$
	- 我们可以忽略“同时有两个包到达”的情况（现实中几乎不会发生）。
	- 这使得建模变得简单：每次只考虑是否有一个事件发生。

| 假设                     | 解释                                 |
| ---------------------- | ---------------------------------- |
| **1. 到达概率与时间成正比，且无记忆** | $P(arrival\;in\;Δt)=λΔt$，与历史无关     |
| **2. 短时间内不会有多次到达**     | $P(multiple\;arrivals)→0\;当\;Δt→0$ |
如果事件以速率 $λ$随机到达，则在时间 $t$ 内恰好有 $k$ 次到达的概率服从**泊松分布**：
$$
P(k;\lambda t) = \frac{(\lambda t)^k e^{-\lambda t}}{k!}
$$
其中：
- $k$：到达次数
- $λt$：平均到达数
- $e$：自然对数底


### 分析 Pure ALOHA
#### 记号
- $T_f$: _frame time_ (processing, transmission, propagation)
- $S$: Average number of _successful transmissions_ per $T_f$ ; that is, the _throughput_
	- the number of _“good” transmissions_ per frame time
- $G$: Average number of _total frames_ transmitted per $T_f$
	- represents the _total number of attempted_ transmissions per frame time
- $D$: _Average delay_ _between_ the time a packet is _ready_ for transmission and the _completion_ of successful transmission.
#### 假设
1. 所有帧定长
2. 信道都是无噪声信道，错误只会被collision造成
3. 帧不会在任意一个栈入队
4. 信道都服从泊松过程

#### 计算吞吐量
$$
S=G*(Probability\;of\;good\;transmission)
$$
##### Vulnerable Time
**对于一个正在发送数据包的主机来说，它在传输期间容易受到其他主机干扰的时间窗口称为“vulnerable time”（脆弱期）**。
为了保证这次传输成功，**在以下时间段内不能有其他主机开始发送**：

|时间段|描述|
|---|---|
|**从当前帧开始前的 Tf​ 时间**|如果另一个主机在这个时间段开始发送，它的包会与当前包重叠（后半部分）|
|**从当前帧开始后的 Tf​ 时间**|如果另一个主机在这个时间段开始发送，它的包会与当前包重叠（前半部分）|
所以，总共的**易受攻击时间是 $2T_f$​**。
![[Pasted image 20251229005547.png]]
##### 成功传输的概率
- 成功传输的条件是：**在 $2T_f​$ 的易受攻击时间内，没有其他主机发起传输**。
- 假设所有主机的行为服从泊松过程，平均到达率为 $G$（单位：帧/帧时间）。
- 那么，在 $2T_f$​ 时间内，其他主机发送的概率可以用指数分布建模。
代入公式：
$$
P_0(2T_f) = \frac{(\lambda*2T_f)^0\: e^{-\lambda 2 T_f}}{0!}  = e^{-2G}\; as\;\lambda = \frac{G}{T_f} \; so \; S = G*e^{-2G}
$$
$$S=G×e^{−2G}$$
这是一个经典的表达式！我们可以用它来分析 Pure ALOHA 的最大吞吐量。
######  分析：
- 当 $G=0$：$S=0$
- 当 $G=0.5$：$S=0.5×e^{−1}≈0.184$
- 当 $G=1$：$S=1×e^{−2}≈0.135$
- 最大值出现在 $G=0.5$ 时，此时：
    $S_{max}​=\frac{1}{2e}​≈0.184​$
所以，**Pure ALOHA 的最大吞吐量约为 18.4%**，也就是说，最多只有约 18.4% 的信道容量被有效利用。


### ALOHA 协议 和 TDMA（时分多址）协议
#### TDMA
TDMA（Time Division Multiple Access）是一种**静态、轮转式**的多路复用技术。将时间划分为固定长度的时隙（time slots），每个主机被分配一个固定的时隙来发送数据。
- **工作方式**：
    - 每个主机必须等待轮到自己的时隙才能发送。
    - 即使没有数据要发，它的时隙也会被保留。
- **优点**：
    - ✅ **避免冲突**：因为每个主机只在自己的时隙发送。
    - ✅ **公平性好**：所有用户轮流使用信道。
- **缺点**：
    - ❌ **延迟高**：如果某个主机有紧急数据，也必须等到它的“轮次”到来。
        - 延迟与主机数量成正比：如果有 N 个主机，最坏情况下要等 N 个时隙。
    - ❌ **利用率低**：当某台主机无数据发送时，其时隙仍被占用 → 信道空闲浪费。

#### ALOHA
ALOHA 是一种**竞争型协议**（Contention-based protocol）。主机一旦有数据就立即发送，无需等待。
- **工作方式**：
    - 主机可以随时发送数据包。
    - 如果发生冲突（多个包同时发送），则重传。
- **优点**：
    - ✅ **延迟极低**：数据一产生就可以立即发送，适合突发性流量。
    - ✅ **响应快**：适用于实时或交互式应用。
- **缺点**：
    - ❌ **利用率低**：由于频繁冲突，只有约 **18% 的信道容量能被有效利用**。
    - ❌ **吞吐量受限**：最大吞吐量约为 $S_{max​}=\frac{1}{2e}​≈0.184$（即 18.4%）。
    - 无法充分利用带宽，尤其在网络负载较高时性能急剧下降。

### “Slotted ALOHA”（分时隙 ALOHA）
**Slotted ALOHA** 是在 **Pure ALOHA** 的基础上发展而来的，其核心思想是：

> **将时间划分为固定长度的时隙（slots），每个时隙的大小等于一个帧的传输时间（Tf）**。

这使得所有主机只能在**时隙边界处开始发送数据**，从而减少冲突机会。

#### 关键规则
1. **信道组织为均匀时隙**
	- 时间被划分为一系列等长的时隙。
	- 每个时隙的长度 = 一个数据帧的传输时间（即发送完整个帧所需的时间）。
	- 所有主机必须同步这些时隙（例如通过广播信号或网络同步机制）。

2. **仅允许在时隙边界开始传输**
	- 主机不能随意发送数据。
	- 只能在下一个时隙的开始时刻（slot boundary）发送新帧。
	- 如果某个主机的数据包准备好了但当前不在时隙边界，则需等待到下一个时隙开始再发。
3. **发送流程（Procedure）**
	- 当主机准备好发送一个新帧时，
	- 它会**立即排队**，并在**下一个可用的时隙开始时**发送该帧。
	- 不需要侦听信道（不像 CSMA），也不检测冲突（除非后续确认失败）。

| 特性     | **Pure ALOHA**      | **Slotted ALOHA**   |
| ------ | ------------------- | ------------------- |
| 发送时机   | 任意时刻都可以发送           | 仅允许在时隙边界发送          |
| 冲突范围   | 任何时间接近都会冲突（前后各 T_f） | 只有在**同一时隙内**才可能发生冲突 |
| 易受攻击时间 | $2T_f​$             | $T_f​$              |
| 最大吞吐量  | ~18.4%              | ~36.8%              |
| 同步要求   | 无                   | 需要时钟同步              |
