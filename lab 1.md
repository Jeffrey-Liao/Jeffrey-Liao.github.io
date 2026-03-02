# ab 1 – Introduction & Computer Hardware

📚 来源：

Sum_1_Lab_Introduction

## 一、课程结构（考试会考规则）

- 每周：2 lecture + 2 lab + 1 consultation
    
- 4 次 part-exam（每个 ≥2）
    
- 2 次 homework
    
- 没有单独补考（成绩综合计算）
    

⚠️ 重点：每个小测必须单独及格。

## 二、计算机基本组成

### 1️⃣ 基本外设

- Monitor
    
- Keyboard
    
- Mouse
    
- 其他 I/O 设备
    

---

## 三、主板结构（非常可能理论考）

### 主板包含：

- Processor socket
    
- Chipset（Northbridge / Southbridge）
    
- DIMM memory slots
    
- Flash ROM（非易失存储）
    
- Clock generator
    
- Expansion cards
    

---

## 四、CPU 结构（必须掌握）

CPU 包含：

- ALU（Arithmetic Logical Unit）算术逻辑单元
    
- Control Unit 控制单元
    
- Registers 寄存器
    
- Cache 高速缓存
    
- Address Generation Unit
    

### CPU 工作步骤（考试常问顺序）

1. Memory → processor
    
2. Decode instruction
    
3. Execute
    
4. Store result
    
5. Next instruction
    

这就是经典的**取指-译码-执行-存储循环**

---

## 五、Lab 实践内容

- 拆电脑
    
- 找 CPU
    
- 找 RAM
    
- 找 Flash memory
    
- 找 north/south bridge
    
- 拍照做 PPT 提交
    

⚠️ 这部分是实践考点。

---

# 🐧 Lab 2 – Linux Basics + FTP + Redirection

📚 来源：

Sum_2_Unix_Base

这是你 Linux 基础命令的核心。

---

## 一、远程连接

### 1️⃣ FTP / SCP

使用 WinSCP 连接服务器：

fundofcomp.inf.elte.hu

### 2️⃣ SSH / Telnet

用 Putty 登录服务器

---

## 二、Linux 基础命令（考试核心）

|命令|功能|
|---|---|
|man|查看帮助|
|ls|列目录|
|cd|切换目录|
|pwd|当前路径|
|mkdir|创建目录|
|rm|删除|
|cp|复制|
|ln|创建链接|
|who|查看登录用户|
|find|查找|
|passwd|改密码|

---

## 三、I/O 重定向（必考）

>    覆盖输出  
>>   追加输出

例子：

echo hello > file.txt

---

## 四、文件查看

cat file.txt

---

## 五、压缩与归档

|命令|作用|
|---|---|
|zip|压缩|
|unzip|解压|
|tar|归档|

---

## 六、重要考点

### ln vs ln -s

- ln = 硬链接
    
- ln -s = 软链接（symbolic link）
    

考试可能问区别。

---

## 七、diff

比较两个文件：

diff file1 file2

---

# 🧾 Lab 3 – Editors + Permissions + Processes

📚 来源：

Sum_3_Unix_Base

这节课开始难度上来了。

---

# 一、文本编辑器

你要会：

- vi / vim
    
- mcedit
    
- pico
    
- joe
    

### vi 基本模式

- i → 插入模式
    
- Esc → 命令模式
    
- :w → 保存
    
- :q → 退出
    
- :wq → 保存退出
    

---

# 二、Linux 权限（高频考试点）

Linux 有三类用户：

- user
    
- group
    
- others
    

权限三种：

- r = read
    
- w = write
    
- x = execute
    

---

## chmod 两种方法

### 1️⃣ 数字法

chmod 754 file

数字含义：

r=4  
w=2  
x=1

7 = 4+2+1

---

### 2️⃣ 字符法

chmod g+w file  
chmod o-r file

---

## public_html 权限测试

移除 others 读权限 → 浏览器访问失败

考试很爱问这个逻辑。

---

# 三、进程管理（超级重点）

## 前台 vs 后台

sleep 1000

### CTRL+C

发送 SIGINT  
→ 终止进程

### CTRL+Z

发送 SIGTSTP  
→ 暂停进程

---

## bg / fg

bg   # 后台运行  
fg   # 前台运行

---

## ps

查看进程

---

## jobs

查看任务

---

## kill

发送信号：

kill -9 PID

SIGKILL 强制终止

---

## trap

捕捉信号

例子：

trap 'echo MyName' 2

收到 CTRL+C 时输出名字

---

# 🎯 前三次考试重点总结

如果老师出 Lab Test，最可能考：

1. 基础 Linux 命令
    
2. 权限计算（数字转 rwx）
    
3. ln 和 ln -s 区别
    
4. 重定向 > >>
    
5. sleep + CTRL+C / CTRL+Z 区别
    
6. chmod 修改权限
    
7. ps / kill
    
8. CPU 工作步骤（理论题）
    

---

# 🧠 给你一个现实建议

你现在在 ELTE Informatics foundation，Linux 是你后面所有编程课的基础。

这门课真正的目标不是让你背命令，而是让你理解：

> Linux 是一个“文件驱动的系统”  
> 一切都是文件  
> 输入输出可以重定向  
> 进程可以被控制

理解这个思维模型，比背命令重要得多。