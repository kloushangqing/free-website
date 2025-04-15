# 如何在 RackNerd VPS 上重装 AlmaLinux 系统并完成初始化配置

## 一、系统重装指南

### 1.1 选择操作系统
RackNerd VPS 提供多种 Linux 发行版选择：
- AlmaLinux
- CentOS
- Debian
- Fedora
- Rocky Linux
- Ubuntu

### 1.2 为什么选择 AlmaLinux？
AlmaLinux 作为 CentOS 的替代品，具有以下优势：
- 出色的稳定性
- 与 CentOS 完美兼容
- 日益壮大的社区支持

👉 [【点击查看】2025年最新 Racknerd 优惠码及特价云服务器方案汇总](https://bit.ly/Rack_Nerd)

### 1.3 重装步骤
1. 选择 AlmaLinux 8 或 9 版本
2. 点击 **Reinstall** 按钮
3. 等待系统重装完成

## 二、系统初始化配置

### 2.1 解决 SSH 指纹问题
重装后可能出现 SSH 连接问题，解决方法：
bash
# 删除本地 known_hosts 中的旧记录
vim ~/.ssh/known_hosts

### 2.2 基础系统配置

#### 修改主机名
bash
hostnamectl set-hostname my_host_name

#### 安全优化
bash
# 禁用 ICMP 协议
echo "net.ipv4.icmp_echo_ignore_all = 1" >> /etc/sysctl.conf
sysctl -p

# 关闭 SELinux
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

### 2.3 SSH 安全加固

#### 修改默认端口
bash
# 检查端口占用
yum -y install lsof
lsof -i:1234

# 防火墙设置
firewall-cmd --add-port=2222/tcp --permanent
firewall-cmd --reload

# 修改 SSH 配置
vi /etc/ssh/sshd_config
systemctl restart sshd

#### 用户管理
bash
# 创建新用户
adduser username
passwd username

# 赋予管理员权限
usermod -aG wheel username

# 禁止 root 远程登录
vi /etc/ssh/sshd_config
systemctl restart sshd

### 2.4 性能优化
bash
# 启用 BBR 加速（RackNerd VPS 通常已默认开启）
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p

## 三、必备软件安装

### 3.1 基础工具
bash
sudo dnf clean all
sudo dnf update
sudo dnf groupinstall "Development Tools"
sudo yum -y install wget git zsh tar util-linux-user lua

### 3.2 实用工具
bash
# 安装 Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# 安装 fzf
sudo dnf install epel-release
sudo dnf install fzf

# 安装 Neovim
sudo dnf install neovim

## 四、终端环境配置

### 4.1 Zsh 优化
bash
# 修改默认 shell
sudo chsh -s /bin/zsh

# 配置 .zshrc
export TERM=xterm-256color
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

### 4.2 Powerlevel10k 主题
bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

通过以上步骤，您可以在 RackNerd VPS 上快速部署一个安全、高效的 AlmaLinux 服务器环境。