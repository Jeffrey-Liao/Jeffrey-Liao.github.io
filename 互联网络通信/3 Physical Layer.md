一个网络被描述为
- delay
- loss
- throughput

# Delay
## 延迟类型
- 链路型延迟(link properties)
	- Transmission delay
		- $\frac{packet\:size\:[bits]}{link\:bandwidth\:[bits/s]}=\frac{1000\:bits}{100\:Mbps} = 10μsec$
	- Propagation delay(一般是定值)
		- 以太网的传播寻找host的延迟
		- $\frac{link\:length\:[m]}{signal\:propagation\:speed\:[m/sec]} = \frac{30000\:[m]}{2*10^8 \:[m/sec]} = 150μsec$
- 流量和交换机内部延迟(traffic mix & switch internals)
	- Processing delay(_一般很小_)
		- 指一个网络节点（如路由器或交换机）在接收到一个数据包后，**为处理该数据包所花费的时间**。
	- Queueing delay
		- Queuing delay is the **hardest to evaluate**
		- Queue解决了短时间大量包涌入的情况，但是增加了延迟
		- It is characterized with statistical measures
		- 延迟取决于：
			- arrival rate at the queue
			- transmission rate of the outgoing link
			- traffic burstiness
总延迟就是上面的所有求和
## 包数量和链路型传输延迟类型的关系
### 带宽
**信道带宽 H** 描述的是**频率维度上的“通道宽度”**，决定了信道能承载多少频率成分，从而影响数据传输能力。

|情况|数据包数量与大小|链路带宽|主导延迟类型|
|---|---|---|---|
|1|`10⁷ × 100B` 包（即100万包）|1 Gbps|**传输延迟主导**|
|2|`1 × 100B` 包|1 Gbps|**传播延迟主导**|
|3|`1 × 100B` 包|1 Mbps|**两者都重要**|
- 高带宽高数据量，则传输延迟主导
- 高带宽低数据量，则传播延迟主导
- 低带宽低数据量，则两者都主要
## 计量因数
- average packet arrival rate   
	- $a$   \[packet/sec]
- transmission rate of outgoing link   
	- $R$   \[bit/sec]
- fixed packets length   
	- $L$   \[bit]
- average bits arrival rate   
	- $L*a$   \[bit/sec]
- traffic intensity   
	- $\frac{L*a}{R}$

# Loss
If the queue is persistently overloaded,  it will eventually drop packets (loss)

# Throughput
- Average **throughput** \[bits/sec] = $\frac{data\:size\:[bits]}{transfer\:time\:[sec]}$
- To compute throughput, one has to consider **thebottleneck link**（在一条端到端路径（end-to-end path）中，**带宽最小（或可用容量最低）的那条链路**。它决定了整个数据流的最大传输速率，就像瓶子的“颈部”限制了液体流出的速度一样。）

As technology improves, _throughput increase_ &  _delays are getting lower_ _except for propagation_(传播延迟受制于光速影响)
Because of propagation delays,  Content Delivery Networks **move content closer to you**

# 工作原理
- Get bits across a physical medium（从物理介质中获取一个bit）
	- Digital computers
		- 0 or 1
	- 模拟信号（Analog world）
		- 波幅和频率
## 电与比特
- 1：有电压或者是当前电压
- 0：无电压
但是实际传输中传输的是模拟信号波形，需要被转换为0,1的形式
![[Pasted image 20251226113631.png]]
## 波形计算与转换
周期函数可以被写为：
$$
g(t)=\frac{1}{2}c +\sum^{\infty}_{n=1}{a_n\sin({2\pi nft})}+\sum^{\infty}_{n=1}{b_n\cos({2\pi nft})}
$$
是**傅里叶级数Fourier Series** 的一种常见形式，用于将一个**周期函数**表示为一系列正弦和余弦函数的无穷线性组合
其中：
- $f=\frac{1}{T}$是基础频率
- $a_n=\frac{2}{T}\int^{T}_{0}{g(t)\sin(2\pi nft)\; dt}$：为常数，第 n 阶正弦项的系数，反映该频率**正弦波**的“强度”
- $b_n=\frac{2}{T}\int^{T}_{0}{g(t)\cos(2\pi nft)\; dt}$：为常数，第 n 阶余弦项的系数，反映该频率**余弦波**的“强度”
- $c=\frac{2}{T}\int^{T}_{0}{g(t)\; dt}$：常数项（直流分量），通常记作 a
- $2\pi nft$：角频率为 $2\pi nft$的谐波（harmonic），其中 n=1 是基波，n=2,3,… 是高次谐波。

在实际应用中，我们会重复二进制编码的高低电平，从而创造周期性，比方说重复B的二进制编码波形
![[Pasted image 20251226120559.png]]
## 信号衰减（Attenuation）
- **传输**和**接收**到的信号的强度比（Ratio of transmitted ($P_0$) and received ($P_1$) power）
- High attenuation = **little power arrives at receiver**
- 计算公式：
$$
\alpha[db] = 10*log_{10}{\frac{P_0}{P_1}}
$$
- 影响因素
	- 物理介质
	- 传输距离
	- 其他
- 特性
	- 不同的频率衰减程度不同
	- 并非所有频率都会被介质传输
	- 相移（波形位移）
		- 不同波形有不同的信号传播速度
		- 基于频率的波形变形
	- 会在传播过程中出现噪声
## 符号和位
在相同单位时间内，表示多于一个位可以获得更大的传输带宽
- Symbol rate（BAUD）
	- 每秒传输的**符号**数量
- Data rate（bps）
	- 每秒传输的**比特**数量
比如：
- Having 4 symbols: 
	- A(00),B(01),C(10),D(11)
![[Pasted image 20251226121915.png]]
一个600波特（每秒发送600个符号），有16个符号

## 数据传输
### 波的特性
一个周期性信号可以分解为一系列正弦波的叠加，这些正弦波称为**傅里叶级数**中的分量。其中：

- **基波（Fundamental Frequency）**：是信号的**最低频率**成分，等于信号周期的倒数。
- **谐波（Harmonics）**：是基波频率的整数倍（如 2f₀, 3f₀, 4f₀...），称为第 2 阶、第 3 阶、第 4 阶谐波等。
	- 谐波越多 → 可以承载的信息越多 → 数据率越高
### 频率计算公式
- **T (ms)**：每个比特的时间长度（单位：毫秒），即 $T=\frac{1}{bit\;rate} ​$
- 基波频率（fundamental frequency）： $f_0 = \frac{1}{T}$
	- **要传输某个比特率的数据，需要多少个正弦波（谐波）来近似该信号**。

| b/s   | T (ms) | 基波频率 (Hz) | 谐波数量 |
| ----- | ------ | --------- | ---- |
| 300   | 26.67  | 37.5      | 80   |
| 600   | 13.33  | 75        | 40   |
| 1200  | 6.67   | 150       | 20   |
| 2400  | 3.33   | 300       | 10   |
| 4800  | 1.67   | 600       | 5    |
| 9600  | 0.83   | 1200      | 2    |
| 19200 | 0.42   | 2400      | 1    |
| 38400 | 0.21   | 4800      | 0    |

如：
传输8比特需要$T=\frac{8}{B}Hz$
基波为：$f_0 = \frac{1}{T} = \frac{B}{8}$
所以最多可以发送的谐波数为
$$
最大谐波阶数=\lfloor\frac{最大频率}{基波}\rfloor = \frac{3000}{B/8} = \frac{24000}{B}
$$
- 受限于
1. 信道的**带宽**（即能通过的最高频率）；
2. 信号的**基波频率**（由数据传输速率决定）

### 噪声干扰
- 带宽（H）：是指信号可以有效传输的**频率范围**
- 符号数量（V）：表示**每个符号**能携带的**信息量**。
#### 无噪声信道（奈奎斯特准则）：
在**无噪声理想信道**中，最大数据速率由奈奎斯特定理决定：$$C=2Hlog_2​(V)[bps]$$
- $log_2​(V)$: 每个**符号**携带的比特数
#### 有噪声信道（香农定理）： 
在**存在噪声的信道**中，最大可传输速率由香农定理给出：
$$
    C=Hlog_2​(1+\frac{N}{S}​)[bps]
$$
- $\frac{N}{S}$: 信噪比（Signal-to-Noise Ratio）
# 主要挑战
1. How to _represent bits in analog_（如何使用模拟信号表示位）
2. Ideally, want _high-bit rate_
3. But, must _avoid desynchronization_

# 传输方式
## 有线传输
1. 存储介质运输：使用卡车等实体运输
2. 双绞线（Twisted pair）：
	1. telephone networks; 
	2. double copper wire, 
	3. both analog and digital; 
	4. UTP and STP (Unshielded Twisted Pair and  Shielded Twisted Pair)
3. 同轴线缆（Coaxial cable）：
	1. **Higher speed** and **larger distance** than with twisted pair; 
	2. analog (75 Ω) and digital (50 Ω)
4. 光纤：
	1. light source, media and detector; 
	2. light impulse = 1 bit, no  light impulse = 0 bit;

## 无线传输
### 传输公式
- 频率($f$,单位Hz)：the rate per second of a vibration **constituting an electromagnetic wave**.
- 波长($\lambda$)：the distance between successive crests of a wave（两个相邻波峰的距离）
- 光速($c = 3.8*10^8 m/s$)：signal propagation **speed of electric signals** in a physical media
	- 真空中：$c = 3.8*10^8 m/s$
	- 同轴线缆中：$c = \frac{2}{3}*c$
- 关系
	- $\lambda f = c$
		- 电磁波的传播公式
### 无线电传输特性
- **无线电频率传输**
	- 特性：
		- 简单易实现；
		- 可覆盖**长距离**；
		- 适用于**室内和室外**环境；
		- 传播特性**依赖于频率**（不同频率的信号传播方式不同）。
	- 类型
		- 地波传播（ground wave），低频信号**沿地面传播**，适合远距离通信（如AM广播）
		- 天波传播（sky wave），信号被**电离层反射回地面**，可实现超视距通信（如短波电台）
- **微波传输**
	- 沿**直线传播**（视距传播，Line-of-Sight）
	- 信号容易衰减（attenuation）；
	- 成本相对便宜。
- **红外线与毫米波**
	- 仅适用于**短距离**通信；
	- **无法穿透物体**（如墙壁、家具）；
	- 通常用于定向通信。
	- 举例：
		- 红外线：常见于遥控器、红外数据传输（如旧式手机、电脑）；
		- 毫米波：属于**极高频段**（24–100 GHz），带宽大，**支持高速传输**（如 5G mmWave）。
- **可见光通信**
	- 使用激光或LED光源；
	- 传输速度**很高**；
	- 成本**较低**；
	- 受**天气条件**影响（如雾、雨、尘埃会削弱信号）。

|传输方式|传播特性|距离|是否穿墙|成本|应用场景|
|---|---|---|---|---|---|
|**无线电波**|弯曲、反射、折射|长距离|✅ 可以|低|手机、Wi-Fi、广播|
|**微波**|直线传播|中到远|❌ 不行|低|中继、卫星、雷达|
|**红外/毫米波**|直线、短程|短距离|❌ 不行|低|遥控、5G mmWave|
|**可见光（激光）**|直线、高方向性|短至中|❌ 不行|低|Li-Fi、FSO、卫星通信|

### 传输分频
一种将多个信号通过**不同频率**在**同一物理信道**上同时传输的技术。
![[Pasted image 20251226133842.png]]
Internet in a cable TV network
- 上行：0–5 MHz（低频，抗干扰强）
- 下行：550–750 MHz（高频，容量大，适合高速数据）
- **下载速度远高于上传速度**，就是因为下行频段更宽、更多。

| 特性             | 说明                |
| -------------- | ----------------- |
| ✅ **频谱利用率高**   | 多个信号共享同一物理媒介，提高效率 |
| ✅ **独立传输**     | 各信号互不影响（只要频段不重叠）  |
| ✅ **易于实现**     | 通过滤波器即可分离不同频段     |
| ❌ **需要严格频率规划** | 若频段划分不当，会产生干扰     |
| ❌ **固定带宽分配**   | 某些频段不能动态调整，灵活性较差  |
| ❌ **存在频率损耗**   | 高频信号衰减快，尤其在长距离传输中 |

# 数据传输
我们假设
1. We have two **discrete signals**, high and low, **to encode 1 and 0**
2. Transmission is **synchronous**, i.e. there is a clock that **controls signal sampling**
3. Amplitude and duration of signal **must be significant**

## 归零编码
- 每个比特用一个固定的电压电平表示：
    - `1`：保持在高电平（如 +5V）
    - `0`：保持在低电平（如 0V）
- 主要问题：**长串的 0 或 1 会导致失步（desynchronization）**, 当连续出现多个 `1` 时，信号始终处于高电平，没有任何跳变。
	- _如何区分“很多个 0”和“没有信号”？_
	- _如何在长时间的 1 中恢复时钟？_
### 时钟漂移（Clock drift）
是一个主要问题——两个不同的时钟永远无法始终保持完全同步。
- 没有两个物理时钟是**绝对精确且完全一致**的。
- 即使标称都是 1 MHz，实际可能是：
    - 发送端：1.000001 MHz
    - 接收端：0.999998 MHz
- 随着时间推移，这种微小差异会累积，造成**采样时刻错位**，最终导致**误码**。
### 解决时钟漂移
1. Explicit clock signal（单独的时钟信号）
	- 发送端不仅发送数据线（Data），还**单独发送一根时钟线（Clock）**；
	- 接收端用这根时钟信号来**精确控制采样时刻**。
	 - 典型应用：
		- SPI（Serial Peripheral Interface）
		- I²C（虽有时钟线，但速率低）
		- 并行总线（如早期计算机内存接口）
2. **在关键点重新同步（Synchronize at Crucial Points）**
	- 接收端使用自己的本地时钟；
	- 但在每个**数据帧/字符的起始位**（如 UART 的 start bit）进行**相位校正**；
	- 之后在短时间内（如一个字节 8–10 位）依靠时钟的**短期稳定性**完成采样。
	- 典型应用：
		- **异步串行通信（如 RS-232, UART）**
		    - 每个字节以“起始位（0）”开头，接收方检测下降沿后启动计时
		    - 按约定波特率（如 9600 bps）在中间时刻采样后续位
3. **从信号中提取时钟（Clock Recovery / Self-Clocking）**
	- 设计一种**编码方式**，使得数据信号中**天然包含足够的跳变（transitions）**；
	- 接收端通过检测这些跳变，用**锁相环（PLL）或延迟锁定环（DLL）** 重建出发送端的时钟。
常见编码方案：

| 编码               | 特点               | 应用                        |
| ---------------- | ---------------- | ------------------------- |
| **曼彻斯特编码**       | 每比特中间必有一次跳变      | 早期以太网（10BASE-T）、RFID      |
| **4B/5B + NRZI** | 每4位转5位，保证足够1（跳变） | Fast Ethernet（100BASE-TX） |
| **8B/10B**       | 直流平衡 + 跳变丰富      | PCIe、SATA、光纤通道            |
| **64B/66B**      | 高效，用于高速链路        | 10GbE、USB 3.0             |
## 非归零编码Non-Return to Zero Inverted (NRZI)
- 1 ：make transition
- 0 ：remain the same
## Manchester
- 每个比特用一个跳变表示：
	- 1：high-to-low 
	- 0：low-to-high
- Good: Solves clock skew (every bit is a transition)
- Bad: Halves throughput (two clock cycles per bit)
## Desynchronization
是指在通信、计算或控制系统中，**发送端与接收端（或多个组件之间）的时序不再对齐**，导致无法正确解析数据、协调操作或维持系统一致性。
## 信号传输
1. **基带传输**（Baseband） 是将**原始的数字信号序列**直接放到传输线路上，不进行调制。
	![[Pasted image 20251226142528.png]]
	1. **直接传输数字符号**
	    - 数字信号（如 0 和 1）被转换为不同电平的电压或电流。
	    - 例如：`1` 对应高电压，`0` 对应低电压（像 NRZ 编码）。
	2. **使用直流（DC）或低频信号**
	    - 信号本质是**直流（Direct Current, DC）** 或接近 DC 的低频信号。
	    - 不需要载波频率，信号本身就是数据。
		    - **以太网供电（PoE）**：在网线（双绞线）上同时传输**直流电**（为设备供电）和**高频数据信号**（用于通信）。
			- **串行通信（如 RS-232）**：使用电压电平（如 +3V 至 +15V 表示逻辑“1”，-3V 至 -15V 表示逻辑“0”）传输数据，本质上是**低频或基带信号**。
	- 缺点
		- **带宽限制导致信号失真**
			- 接收端收到的信号可能变形（如边沿变钝），难以准确识别比特。
		- **衰减和失真依赖于频率**
			- 不同频率分量在传输中衰减程度不同 → 引起**信号畸变**。
		-  **存在直流分量**
			- 长串 0 或 1 会导致平均电压偏移，影响后续电路（如变压器隔离失效）
	- 步骤
		1. 将信息**转化为数字形式**（Bring source information in digital form）
		2. **使用**有损或无损压缩将无用**数据压缩**（Source encode: Remove redundant or irrelevant data）
		3. 将原有**数字位转化为通道编码**以提高传输效率或添加安全位保证传输安全性和完整性（Channel encode: Map source bits to channel symbols）
		4. 通过物理通道发送信号（Physical transmit: Turn the channel symbols into physical signals）
2. **宽带传输**（Broadband） 是将数字信号**调制到一个特定频率的正弦波（载波）上**，再发送出去。
![[Pasted image 20251226145504.png]]
	1. **使用正弦波作为载波（carrier）**
	    - 载波通常是**单一频率的高频频正弦波**（sine wave）。
	    - 例如：50 MHz、1 GHz 等。
	    - 但**纯正弦波没有信息内容**！
	2. **通过调制改变载波参数**
		- 为了传输信息，必须根据要发送的符号（bit）来**修改载波的某些特性**，这个过程叫 **调制（Modulation）**。
		- 可以调制的三个参数：
		    - ✅ **振幅（Amplitude）** → AM（幅度调制）
		    - ✅ **频率（Frequency）** → FM（频率调制）
		    - ✅ **相位（Phase）** → PM（相位调制）
			- 这些方法统称为 **ASK、FSK、PSK** 等。
	3. **扩展频谱，避免 DC 问题**
		- 调制后的信号具有较窄的频谱，集中在载波频率附近。
		- 没有直流分量，**适合长距离**、**多路复用传输**。
	- 优点：
		- ✔️ 可以在**同一根电缆上同时传输多个信道**（频分复用 FDM）
		- ✔️ 支持远距离传输（如无线电、光纤、卫星）
		- ✔️ 更好地抵抗噪声和干扰
		- ✔️ 适用于无线通信、有线电视、Wi-Fi、
## 在调制波形中携带信息
$$s(t)=A\:sin⁡(2πft+φ)$$where
- $s(t)$： 信号随时间的变化 
	- 表示在任意时刻 t（单位：秒）的信号瞬时值。
- $A$ is the amplitude(振幅),
	- 信号的最大偏离值（峰值），反映信号的“强度”或“能量”
- $f$ the frequency（频率）
	- 信号每秒钟完成多少次完整振荡（从 0 → 峰值 → 0 → 谷值 → 0）。
- $φ$ the phase.
	- 描述信号在 t=0 时刻的起始位置（相对于标准正弦波的偏移）。
	- 它不改变信号的形状、频率或幅度，但决定了**波形在时间轴上的左右平移**。
![[Pasted image 20251226213724.png]]
## 模拟调制（Amplitude modulation）
将**低频的模拟基带信号**（如语音、音乐）加载到**高频载波信号**上，以便于在信道中高效传输的过程。
The time-varying $s(t)$ signal is encoded into the amplitude of the _sine wave_ (carrier):
$$
f_A(t) = s(t)* \sin(2\pi ft + \phi)
$$
### 幅度调制(Analog signal)
- 用基带信号 $s(t)$ 控制**载波的振幅**。
- 幅移键控（ASK）或开关键控（OOK）(Digital signal): amplitude keying or on/off keying (s(t) takes discrete values)
![[Pasted image 20251226162722.png]]

### 频率调制（Frequency modulation）
用基带信号 $s(t)$ 控制**载波的瞬时频率**。
The time-varying $s(t)$ signal is encoded into the frequency of the _sine wave_
$$
f_F(t) = a*\sin(2\pi s(t)t+\phi)
$$
- Analog signal: frequency modulation
- Digital signal: frequency-shift keying
使用不同的偏移作为0,1

### 相位调制（Phase Modulation）
用基带信号 $s(t)$ 控制**载波的相位**。
The signal $s(t)$ is encoded in the phase of the _sine wave_:
$$
f_P(t) = a*\sin(2\pi ft+s(t))
$$
- Analog signal: phase modulation (not really used)
- Digital signal: phase-shift keying  (discrete set of phase changes)
![[Pasted image 20251226164758.png]]
## Usage of multiple symbols
如何通过**使用多个相位或幅度组合**来提高数据传输速率，是现代数字通信中高效调制技术的核心思想。
### 1. PSK with multiple values（多值相移键控）
- 基本 PSK（如 BPSK）只用两个相位（0° 和 180°）表示一个比特。
- 但接收端通常能很好地分辨**较小的相位变化**，因此可以引入更多相位值来编码更多信息。
- 4 symbols/values: $\frac{\pi}{4},\frac{3\pi}{4},\frac{5\pi}{4},\frac{7\pi}{4}$
- **Quadrature Phase Shift Keying (QPSK)**
	- 每个符号可携带 $\log_2​(4)=2$ 比特信息
	- 若符号速率为 $R_s$​，则数据速率 $R_b​=2×R_s$

### 2. Amplitude + Phase modulation（幅度 + 相位联合调制）
- 不仅改变相位，还同时改变**幅度（振幅）**
- 结合两种调制方式：**ASK（幅度键控）+ PSK（相移键控）**
- 从而在一个符号中携带更多比特
- 使用不同的**幅度和相位组合**
- 构成一个 4×4 的网格，共 16 个点
- 每个点代表一个符号，可编码 $log_2​(16)=4$ 比特
- **Quadrature Amplitude Modulation - 16 (16-QAM)**
	- 每个符号携带 4 比特
	- 若符号速率为 $R_s​$，则数据速率 $R_b​=4×R_s​$

| 调制方式       | 符号数 | 每符号比特数 | 数据速率倍数 | 应用场景                |
| ---------- | --- | ------ | ------ | ------------------- |
| **BPSK**   | 2   | 1      | ×1     | 低速、抗干扰要求高的环境        |
| **QPSK**   | 4   | 2      | ×2     | 卫星通信、移动通信           |
| **16-QAM** | 16  | 4      | ×4     | 高速 Wi-Fi、4G/5G、有线电视 |
## 数字信号和模拟信号
1. **有限个离散信号** → **数字信号传输（digital transmission）**
2. **无限多个连续信号** → **模拟信号传输（analog transmission）**

以导线上的**电压或电流**为例：
1. **数字信号（Digital Signal）**
	- 只能取**有限个电压值**。
	- 最常见的是**两个电平**：
	    - 高电平（如 +5V）→ 表示 `1`
	    - 低电平（如 0V）→ 表示 `0`
	- 图形表现为**阶梯状**，在时间上是分段恒定的。
2. **模拟信号（Analog Signal）**
	- 电压可以取**任意值**，即在某个范围内连续变化。
	- 例如：语音信号、温度变化、音频波形等。
	- 图形表现为**平滑曲线**，随时间连续变化。

| 类型       | 说明                                                     |
| -------- | ------------------------------------------------------ |
| **数字信号** | 使用**有限个不同的正弦波**（如不同频率、相位或幅度），每个代表一个符号。例如：FSK、PSK 中的载波。 |
| **模拟信号** | 使用**无穷多的正弦波组合**，可以表示任意复杂波形（如傅里叶级数）。                    |

|方面|数字信号|模拟信号|
|---|---|---|
|**抗干扰能力**|强（只需判断是高还是低）|弱（任何干扰都会改变信号值）|
|**再生与中继**|可以在中继点“重新生成”干净信号（如放大并整形）|无法完美恢复，每次放大都积累噪声|
|**错误检测与纠正**|支持 ECC 编码（如奇偶校验、CRC）|几乎不可能纠错|
|**存储与处理**|易于数字化存储、加密、压缩|处理复杂，易失真|

# 静态信道分配
1. Multiplexing（多路复用）
	- 允许多个用户或数据流共享一条物理线路
	- 使多个信号能够**同时**通过**同一传输介质**进行传输。  
	- 为此，将通信信道划分为多个较小的子信道。  
	- 发送端需要一个专用设备（**多路复用器，Multiplexer**），将各路信号分配到对应的子信道中。
2. Space-Division Multiplexing（空分复用，SDM）
	- 空分复用是最“直白”的复用方式——**物理隔离**。
	- 每个子信道使用**独立的点对点导线**；  
	- 或者为各子信道使用**不同的天线**。
3. Frequency-Division Multiplexing（频分复用，FDM）
	- 多路信号被合并后在同一信道上传输；  
	- 每路信号占用**不同的频率范围**；  
	- 通常用于**模拟信号传输**。
	- 各信号在频域上互不重叠，接收端用滤波器分离。
4. Wavelength-Division Multiplexing（波分复用，WDM）
	- 用于**光纤通信**。
	- 是**光通信中的频分复用**。由于光信号频率极高（~200 THz），工程上习惯用**波长**（单位：nm）而非频率来描述。
5. Time-Division Multiplexing（时分复用，TDM）
	- 将时间划分为**互不重叠的时间间隔**（时隙）；  
	- 每个时隙**专属于某一个发送方**。
6. CDMA – Code Division Multiple Access
	- 允许多个用户共享同一频段进行通信，而不会互相干扰
	- CDMA的关键特性
		1. **扩频技术**：CDMA采用直接序列扩频(Direct Sequence Spread Spectrum, DSSS)技术，将原始信号扩展到一个更宽的频带上，这样即使在低功率下也能有效传输信息，并且**提高了抗干扰能力**。
		2. **独特编码**：每个用户都分配有一个**唯一的编码序列**（通常是伪随机序列），这个序列用来区分不同用户的信息。接收机使用同样的编码序列来解码信号，从而恢复出原始数据。
		3. **软容量限制**：与TDMA（时分多址）或FDMA（频分多址）相比，CDMA**没有固定的信道数限制**。理论上，系统容量取决于干扰水平，而不是固定频率资源的数量。
		4. **隐私保护**：由于采用了复杂的编码方式，未授权方很难截获和解码CDMA信号，这提供了更好的通信安全性和隐私保护。
		5. **抗多径效应**：CDMA具**有良好的抵抗多径干扰的能力**，这是无线通信中常见的问题，当信号经由不同的路径到达接收器时会**导致信号重叠和失真**。
![[Pasted image 20251226173016.png]]