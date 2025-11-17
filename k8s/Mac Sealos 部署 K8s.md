

虚拟机必要的网络设置

NAT 模式可以共享宿主机的 vpn     桥接模式不能共享 vpn，每个节点都是一个独立的设备


设置静态 ip, 静态 ip 必须网关属于同一网段，可以使用 NAT 模式与桥接模式

1. 在 NAT 模式下，虚拟机会创建一个网卡，网关的地址就是网卡的地址，可以在虚拟机软件的设置中查看。只需要保证 ip 与这个网卡在同一网段就行，<font style="color:#DF2A3F;">不会随着 wifi 而变动</font>
2. 桥接模式下，网关的地址是 wifi 的地址，需要保证节点的 ip 与网关的 ip 在 wifi 在同一个网段，<font style="color:#DF2A3F;">但是当 wifi 变化是则需要重新设置节点的 ip</font>，以保证在同一个网段

ubuntu 系统需要设置以下文件

```bash
# 修改 /etc/netplan/50-cloud-init.yaml


network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s5:
      dhcp4: no
      addresses:
          # 静态ip必须与网关在同一个网段，可以设置大一点，防止与当前网段的冲突
        - 192.168.3.111/24
      routes:
        - to: default
          via: 192.168.3.1     # 网关地址, 例如 pd 默认nat是这个地址
      nameservers:
        addresses: [8.8.8.8, 114.114.114.114]
```







虚拟机下载后启用 `root` 用户并设置密码

sudo passwd root



安装 ssh，启用远程登录

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

允许使用 root 远程登录

```bash
sudo vim /etc/ssh/sshd_config

Port 22                          # SSH端口，默认22
PermitRootLogin yes             # 允许root登录
PasswordAuthentication yes       # 启用密码认证
systemctl start ssh
```





时间同步

```bash
sudo apt install chrony

# 开机启动
sudo systemctl enable --now chrony

#查看同步状态
chronyc sources -v

vim /etc/chrony/chrony.conf


# 使用公共的配置
pool ntp.aliyun.com iburst
pool ntp1.tencent.com iburst
pool ntp.ntsc.ac.cn iburst

sudo systemctl restart chrony

# 手动同步一次
sudo chronyc makestep


# 设置国内时间
timedatectl set-timezone Asia/Shanghai

```



设置不同的主机名称

```bash
# 查看当前名称
hostnamectl

hostnamectl set-hostname main-1
```





关闭防火墙

```bash
ubuntu 默认使用 ufw

如果安装 friewwall  还需禁用
```



参考 sealos 官网的安装方式

