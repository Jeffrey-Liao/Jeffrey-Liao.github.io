# 安装WSL
## 安装必备组件
### 通过设置安装
- 点击`设置>系统>可选功能>更多Windows功能`
![](pic/Pasted%20image%2020260408174538.png)

![](pic/Pasted%20image%2020260408174629.png)


### 通过控制面板安装
- `控制面板>程序和功能>启用或关闭Windows功能`
![](pic/Pasted%20image%2020260408174729.png)
![](pic/Pasted%20image%2020260408174849.png)

### 必要组件
如图所示
![](pic/Pasted%20image%2020260408175021.png)

## 安装Linux子系统
### 列出所有可安装版本
```powershell
wsl --list --online 
```
### 安装
```powershell
wsl --install #安装Ubuntu，默认版本为24.04.4 LTS
wsl --install Ubuntu-24.04 #安装指定版本的Linux（基于可安装版本列表中的名称）
```

### 检测安装是否成功
```powershell
wsl --list
```
出现如图所示即为成功

![](pic/Pasted%20image%2020260408175212.png)
```powershell
wsl #运行WSL
```

## 安装必要软件
### Linux-Ubuntu
先执行一遍`sudo apt update`
- SSH
	- `sudo apt install openssh-server -y`
- C/C++编译器
	- `sudo apt install gcc && sudo apt install g++`
- Java 
	- 23: `wget https://download.java.net/java/GA/jdk25.0.2/b1e0dfa218384cb9959bdcb897162d4e/10/GPL/openjdk-25.0.2_linux-x64_bin.tar.gz`
	- 21: `wget https://download.java.net/java/GA/jdk22.0.1/c7ec1332f7bb44aeba2eb341ae18aca4/8/GPL/openjdk-22.0.1_linux-x64_bin.tar.gz`
- cmake
	- `sudo install cmake`
- git
	- `sudo install git`

### Windows
- [Radmin虚拟局域网](https://www.radmin-lan.cn/)
![](pic/Pasted%20image%2020260408194947.png)
![](pic/Pasted%20image%2020260408195017.png)
# 配置WSL和Windows共用IP
1. 前往`C:\Users\用户名称\`创建`.wslconfig`
2. 添加以下配置
```
[experimental]
autoMemoryReclaim=gradual  
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```

# 创建SSH连接用户
1. `sudo adduser 用户名称`
2. 创建密码

# 配置SSH
## 设置开机自启动
```bash
sudo systemctl enable ssh
```

## 启动SSH
```bash
sudo systemctl start ssh
```

## 重启
```bash
sudo systemctl restart ssh
```

## 重新加载配置
```bash
sudo systemctl reload ssh
```

## 查看SSH状态
```bash
sudo systemctl status ssh
```

## 修改SSH配置文件
1. 打开配置文件修改配置
```bash
sudo vim /etc/ssh/sshd_config
```

其他保持默认，添加以下内容

```
AddressFamily any
ListenAddress 0.0.0.0
ListenAddress ::
#设置端口
Port 22

PasswordAuthentication yes
# 禁止root直接登录
PermitRootLogin no

# 允许特定用户登录
AllowUsers 用户名称1 用户名称2 ...

# 设置空闲超时（秒）
ClientAliveInterval 300
ClientAliveCountMax 2
PubkeyAuthentication yes
# 限制登录尝试次数
MaxAuthTries 3

#负责Git无密码登录
Match User git
    PubkeyAuthentication yes
    AuthenticationMethods publickey
#负责常规登录
Match User 用户名称1,用户名称2,...
    PasswordAuthentication yes
    AuthenticationMethods password
```
2. 重启服务
3. 检查服务状态是否为**Active**
4. 在WSL下测试`ssh 用户名称@127.0.0.1`是否可以成功连接
5. 在Windows下测试`ssh 用户名称@127.0.0.1`是否可以成功连接
6. 开放Windows端口(使用管理员打开终端)
```Powershell
# 开放入站端口
New-NetFirewallRule -DisplayName "规则名称" -Direction Inbound -LocalPort 端口号 -Protocol TCP -Action Allow
# 开放出站端口
New-NetFirewallRule -DisplayName "规则名称" -Direction Outbound -LocalPort 端口号 -Protocol TCP -Action Allow
```

# 配置Git服务
## 创建git用户
```bash
sudo adduser git
```

## 生成Key
- 在Windows下生成
```Powershell
ssh-keygen -t ed25519 -C "key描述"
#             加密方法
```
- 出现的所有对话全部不填，回车一直往下
- 打开`C:\Users\用户名称\.ssh`找到`id_加密方法.pub`文件，并复制文件内容
 ![](pic/Pasted%20image%2020260408184201.png)
- 在WSL中`/home/git`中创建或修改`/.ssh/authorized_keys`，将刚刚的秘钥粘贴进去
- `chmod 700 /home/git/.ssh && chmod 700 /home/git/.ssh/authorized_keys`
- 使用Windows测试连接，如果不需要密码就可以连接即为成功

## 创建和使用仓库
1. 在Linux下创建`项目名称.git`文件夹
2. `cd`到文件夹中
3. `git init --bare`或`git init --initial-branch=main`
4. 在Windows中的项目文件夹中`git remote add git服务名称 git@IP地址:22/home/git/项目名称.git`
5. 在Windows中的项目文件夹中`git push -u server --all`推送所有commit
6. 克隆项目时使用`git clone git@IP:/home/git/项目名称.git 本地项目文件夹名称`

