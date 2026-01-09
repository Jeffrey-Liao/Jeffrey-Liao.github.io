### **第一部分：域名系统 (Domain Name System - DNS)**

DNS 是互联网的一项基础服务，它充当了“电话簿”的角色，将人类可读的域名（如 `www.google.com`）转换为机器可读的 IP 地址（如 `142.250.185.206`）。

#### **1. 问题起源与历史**

- **核心问题**：人类难以记忆复杂的 IP 地址（如 `129.10.117.100`），但需要一种方式来访问网络上的资源。
- **早期解决方案 (`hosts.txt`)**：
    - 在 DNS 出现前，所有主机名到 IP 的映射都存储在一个名为 `hosts.txt` 的中央文件中。
    - 每台计算机（通过 `/etc/hosts` 或 `C:\Windows\System32\drivers\etc\hosts`）都需要手动维护或定期从中央服务器（SRI）下载此文件。
    - **缺点**：完全集中化、不可扩展、难以保证名称唯一性、更新延迟大。随着互联网规模爆炸式增长，该方案迅速失效。

#### **2. DNS 的基本设计原则**

- **分布式数据库**：DNS 不是一个单一的、集中的数据库，而是由全球数百万台服务器组成的分布式系统。这解决了可扩展性和单点故障问题。
- **分层命名空间 (Hierarchical Namespace)**：域名采用树状结构组织，从右到左范围逐渐缩小。
    - **根域 (Root)**：位于树的顶端，用 `.` 表示（通常省略）。
    - **顶级域 (Top-Level Domains, TLDs)**：如 `.com`, `.org`, `.edu`, `.gov`, `.uk` 等。
    - **二级域 (Second-Level Domains)**：如 `google` (在 `google.com` 中), `northeastern` (在 `northeastern.edu` 中)。
    - **子域 (Subdomains)**：如 `www`, `mail`, `cs` (在 `www.cs.northeastern.edu` 中)。
- **分层管理 (Hierarchical Administration)**：整个域名空间被划分为多个 **区域 (Zones)**。每个区域由一个或多个 **权威域名服务器 (Authoritative Name Servers)** 负责管理。例如，`.edu` 区域由 Educause 管理，`northeastern.edu` 区域由东北大学管理。

#### **3. DNS 服务器体系结构**

- **根域名服务器 (Root Name Servers)**：
    - 全球共有13组（A-M），由 ICANN 协调管理。
    - 它们不直接解析最终域名，而是存储所有 TLD（如 `.com`, `.org`）的权威服务器地址。
    - 大多数根服务器使用 **任播 (Anycast)** 技术在全球部署多个物理实例，以提高可靠性和性能。
- **TLD 域名服务器 (TLD Name Servers)**：
    - 负责管理特定顶级域（如 `.com`）下的所有域名。
    - 存储着每个注册在该 TLD 下的域名（如 `google.com`）的权威服务器地址。
- **权威域名服务器 (Authoritative Name Servers)**：
    - 这是存储特定域名（如 `www.neu.edu`）实际 IP 地址记录的服务器。
    - 一个域名可以有多个权威服务器以实现冗余。
- **本地域名服务器 (Local Name Server / Default Name Server)**：
    - 通常由用户的 ISP 或公司网络提供。
    - 用户的主机在进行 DNS 查询时，首先联系的就是这个本地服务器。
    - 它扮演着代理的角色，并大量使用 **缓存 (Caching)** 来提高查询速度和减轻上游服务器负担。

#### **4. DNS 查询过程**

- **递归查询 (Recursive Query)**：
    - 客户端向本地 DNS 服务器发出请求，并期望得到最终答案。
    - 本地服务器如果不知道答案，会代替客户端向根服务器、TLD 服务器等逐级查询，直到找到答案，再将结果返回给客户端。
    - **负担**：主要在被查询的服务器上。
- **迭代查询 (Iterative Query)**：
    - 这是 DNS 服务器之间实际采用的方式。
    - 当一个 DNS 服务器收到它无法回答的查询时，它不会代替请求者去查询，而是返回一个“**引用 (Referral)**”——即它所知道的、更接近目标域名的下一个权威服务器的地址。
    - 请求者（通常是另一个 DNS 服务器）收到引用后，再自行向该服务器发起新的查询。
    - **负担**：主要在发起查询的客户端（或本地服务器）上。
- **典型解析流程（结合两者）**：
    1. 主机 `asgard.ccs.neu.edu` 想知道 `www.google.com` 的 IP。
    2. 它向其配置的 **本地 DNS 服务器** 发起 **递归查询**。
    3. 本地服务器检查缓存，若无，则向一个 **根服务器** 发起 **迭代查询**。
    4. 根服务器返回 `.com` TLD 服务器的地址。
    5. 本地服务器向 `.com` TLD 服务器发起 **迭代查询**。
    6. TLD 服务器返回 `google.com` 的 **权威服务器** 地址（如 `ns1.google.com`）。
    7. 本地服务器向 `google.com` 的权威服务器发起 **迭代查询**。
    8. 权威服务器返回 `www.google.com` 的 A 记录（IP 地址）。
    9. 本地服务器将最终答案 **递归地** 返回给主机 `asgard`，并缓存该结果。

#### **5. DNS 传播与缓存 (Propagation & Caching)**

- **DNS 传播 (DNS Propagation)**：当一个**新的域名记录被创建或修改后**，全球的 DNS 服务器并不会立即同步。这是因为各级服务器都会 **缓存** 查询结果。
- **生存时间 (Time-To-Live, TTL)**：每条 DNS 记录都包含一个 TTL 值（单位：秒）。它告诉缓存服务器这条记录可以被保存多久。TTL 过期后，缓存会被丢弃，下次查询需要重新获取。
- **影响**：新域名生效或 DNS 修改后，可能需要等待 TTL 时间（通常是几小时到72小时）才能在全球范围内生效，这就是所谓的“传播延迟”。

#### **6. DNS 资源记录 (Resource Records - RR)**

DNS 数据库中的信息以资源记录的形式存储。一条 RR 包含四个字段：`(Name, Value, Type, TTL)`。

- **A 记录 (Address Record)**：
    - `Type = A`
    - 将一个 **主机名** 映射到一个 **IPv4 地址**。
    - 例：`www.ccs.neu.edu` -> `129.10.116.81`
- **AAAA 记录**：
    - `Type = AAAA`
    - 将一个 **主机名** 映射到一个 **IPv6 地址**。
- **NS 记录 (Name Server Record)**：
    - `Type = NS`
    - 指定负责该 **域名（或子域）** 的 **权威 DNS 服务器** 的主机名。
    - 例：`ccs.neu.edu` 的 NS 记录可能是 `ns1.ccs.neu.edu`。
- **CNAME 记录 (Canonical Name Record)**：
    - `Type = CNAME`
    - 为主机名创建一个 **别名 (Alias)**，指向其规范（真实）主机名。
    - **用途**：常用于 CDN（内容分发网络），将 `www.example.com` 指向 CDN 提供商分配的动态主机名。
    - 例：`foo.mysite.com` -> `bar.mysite.com`
- **MX 记录 (Mail Exchange Record)**：
    - `Type = MX`
    - 指定负责接收发送到该 **域名** 的电子邮件的 **邮件服务器** 的主机名。
    - 例：`ccs.neu.edu` 的 MX 记录可能是 `mail.ccs.neu.edu`。
- **PTR 记录 (Pointer Record)**：
    - 用于 **反向 DNS 查找 (Reverse DNS Lookup)**，即从 IP 地址查询对应的域名。
    - 存储在特殊的 `in-addr.arpa` (IPv4) 或 `ip6.arpa` (IPv6) 域名空间下。
    - 例：`129.10.116.51` -> `ccs.neu.edu`

#### **7. DNS 作为间接层 (Indirection Service)**

DNS 的强大之处不仅在于名字解析，更在于它提供了一种 **间接性 (Indirection)**。

- **IP 地址变更**：只需在权威 DNS 服务器上修改 A 记录，所有用户下次查询时就会自动获得新 IP，无需通知每个用户。
- **负载均衡**：一个域名可以对应多个 A 记录（多个 IP）。DNS 服务器可以轮询返回这些 IP，从而将用户流量分散到多台服务器上。
- **CDN 集成**：通过 CNAME 记录，可以**轻松地将流量导向地理位置最近的 CDN 边缘节点**。

---

### **第二部分：超文本传输协议 (Hypertext Transfer Protocol - HTTP)**

HTTP 是 Web 的应用层协议，定义了浏览器（客户端）和 Web 服务器之间如何通信以交换超文本文档（如 HTML 页面、图片等）。

#### **1. Web 与 HTTP 基础**

- **Web 页面 (Web Page)**：由一个 **基础 HTML 文件** 和多个 **引用对象 (Referenced Objects)**（如图片、CSS、JS 文件）组成。
- **URL (Uniform Resource Locator)**：用于唯一标识 Web 上的资源，格式为 `http://<主机名>/<路径>`，例如 `www.someschool.edu/someDept/pic.gif`。
- **客户-服务器模型 (Client-Server Model)**：
    - **客户端**：通常是 Web 浏览器（如 Firefox, Safari），负责发起请求、接收响应并渲染内容。
    - **服务器**：Web 服务器软件（如 Apache, Nginx），负责接收请求并返回所请求的对象。
- **基于 TCP**：HTTP 使用可靠的 TCP 作为传输层协议，默认端口为 80。客户端首先与服务器建立 TCP 连接。
- **无状态 (Stateless)**：HTTP 服务器默认不会保留任何关于客户端过去请求的信息。每一次请求都是独立的。这简化了服务器设计，但也带来了挑战（如用户登录状态维持）。

#### **2. HTTP 连接类型**

- **非持久连接 (Non-Persistent HTTP)**：
    - **HTTP/1.0 的默认行为**。
    - 每次请求/响应对都需要建立一个新的 TCP 连接。
    - **缺点**：对于包含多个对象的页面，需要多次建立/关闭 TCP 连接，开销巨大（每个对象至少需要 2 个 RTT：1个用于建连，1个用于请求/响应）。
- **持久连接 (Persistent HTTP)**：
    - **HTTP/1.1 的默认行为**。
    - 在一个 TCP 连接上可以连续发送多个请求和接收多个响应。
    - **优点**：显著减少了 TCP 连接的建立和关闭开销，提高了页面加载速度。
    - **流水线 (Pipelining)**：HTTP/1.1 的一个可选特性，允许客户端在收到前一个响应之前就发送后续请求，进一步减少等待时间。

#### **3. HTTP 报文格式**

- **HTTP 请求报文 (Request Message)**：
    - **请求行 (Request Line)**：包含方法（GET, POST, HEAD 等）、URL 和 HTTP 版本。
        - `GET /index.html HTTP/1.1`
    - **首部行 (Header Lines)**：提供关于请求或客户端的附加信息。
        - `Host: www-net.cs.umass.edu` (必需)
	        - 指明**目标服务器的主机名和端口**（默认端口 80 或 443 可省略）。
        - `User-Agent: Firefox/...`
	        - 告诉服务器 客户端的类型、操作系统、浏览器版本等信息。
        - `Accept: text/html,...`
	        - 告知服务器 客户端能够接收的内容类型（MIME 类型）及其优先级。
        - `Connection: keep-alive`
	        - 控制 TCP 连接在本次请求后是否保持打开状态。
    - **实体主体 (Entity Body)**：对于 POST 请求，用于上传数据（如表单内容）。

|特性|GET|POST|
|---|---|---|
|**数据位置**|附加在 URL 的 **查询字符串（Query String）** 中  <br>例：`/search?q=hello&page=1`|放在 **HTTP 请求体（Body）** 中|
|**可见性**|数据对用户可见（浏览器地址栏显示）|数据不可见（不在 URL 中）|
|**长度限制**|受浏览器和服务器限制（通常 ≤ 2048 字符）|理论上无限制（实际受服务器配置限制，如 `client_max_body_size` in Nginx）|
|**编码类型**|只能使用 `application/x-www-form-urlencoded`（URL 编码）|支持多种：  <br>• `application/json`  <br>• `multipart/form-data`（文件上传）  <br>• `text/xml` 等|
- **HTTP 响应报文 (Response Message)**：
    - **状态行 (Status Line)**：包含 HTTP 版本、状态码和状态短语。
        - `HTTP/1.1 200 OK`
    - **首部行 (Header Lines)**：
        - `Date: ...`
        - `Server: Apache/...`
        - `Content-Type: text/html; charset=ISO-8859-1`
        - `Content-Length: 2652`
    - **数据 (Data)**：请求的对象本身（如 HTML 文件内容）。
- **常用状态码**：
    - `200 OK`：请求成功。
    - `301 Moved Permanently`：请求的对象已被永久移动到新位置（由 `Location:` 首部指定）。
    - `400 Bad Request`：请求报文有误。
    - `404 Not Found`：服务器找不到请求的对象。
    - `505 HTTP Version Not Supported`：服务器不支持请求的 HTTP 版本。

#### **4. Cookie 机制**
由于 HTTP 是无状态的，Cookie 被发明出来以在客户端和服务器之间维持 **状态 (State)**。

- **工作原理**：
    1. **首次访问**：客户端向服务器发送请求。
    2. **服务器创建 Cookie**：服务器生成一个唯一的 **用户 ID**，并在其后端数据库中创建一条记录。然后在 HTTP 响应中加入 `Set-Cookie: <user-id>` 首部。
    3. **客户端存储**：浏览器收到响应后，将 `<user-id>` 存储在本地的 **Cookie 文件** 中。
    4. **后续访问**：浏览器在向同一服务器发送的每个 HTTP 请求中，都会自动包含 `Cookie: <user-id>` 首部。
    5. **服务器识别**：服务器通过 `<user-id>` 识别用户，并执行特定操作（如显示购物车、个性化内容）。
- **用途**：用户认证、购物车、用户偏好设置、会话跟踪（如 Web 邮件）。
- **隐私问题**：Cookie 可以被网站用来追踪用户行为，引发隐私担忧。

#### **5. 第三方 Cookie 与跟踪**

- **原理**：许多网页嵌入了来自第三方（如 Facebook, Google Analytics）的内容，例如“点赞”按钮或广告。
- **过程**：
    1. 用户访问 `Wired.com`。
    2. 页面加载时，浏览器会向 `Facebook.com` 请求“点赞”按钮的图片。
    3. 如果用户之前访问过 Facebook 并有 Cookie，那么这次请求也会携带该 Cookie。
    4. **结果**：Facebook 得知该用户访问了 `Wired.com` 的这篇文章。
- **影响**：这是一种强大的跨站用户行为跟踪技术，是在线广告定向投放的基础。

总结：
**DNS** 通过其分布式、分层的设计解决了互联网规模下的名字解析问题，并提供了灵活的间接寻址能力。**HTTP** 则定义了 Web 通信的基本规则，从连接管理、报文格式到通过 Cookie 机制克服其固有的无状态性，共同构成了我们今天所使用的万维网的基础。