---
title: 在 Arch Linux 上安装并配置 Samba 共享文件
date: 2025-6-13
category: linux
tag: tools
index_img: /img/wallhaven-d8gxj3.png
---

## 步骤 1: 安装 Samba
使用 pacman 安装 Samba：

`sudo pacman -S samba avahi `

安装 avahi软件包， 然后启用/启动 avahi-daemon.service 通过 Zeroconf 使 Samba 服务器可被发现

## 步骤 2: 配置 Samba
1. 复制默认配置文件作为备份：
`sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak`
2. 编辑 Samba 配置文件：
`sudo nano /etc/samba/smb.conf`
根据需求修改或添加以下配置：
`[global]`
`workgroup = WORKGROUP`
`server string = Arch Samba Server`
`netbios name = archserver`
`security = user`
`map to guest = Bad User`
`----`
`[share]`
`path = /path/to/shared/directory`
`browseable = yes`
`writable = yes`
`guest ok = yes`
`read only = no`
`create mask = 0777`
`directory mask = 0777`
path：替换为你想共享的目录路径。
guest ok = yes：允许匿名访问（可选）。

## 步骤 3: 创建共享目录并设置权限
确保共享目录存在，并设置正确的权限：
`sudo mkdir -p /path/to/shared/directory`
`sudo chmod -R 777 /path/to/shared/directory`
如果只允许特定用户访问，可以用以下方式设置权限：
`sudo chown -R username:username /path/to/shared/directory`

## 步骤 4: 设置 Samba 用户
为 Samba 添加用户（如果需要密码访问）：
`sudo smbpasswd -a username`


## 步骤 5: 启用并启动 Samba 服务
启动和启用 Samba 服务：
`sudo systemctl start smb nmb`
`sudo systemctl enable smb nmb`

# 步骤 6: 防火墙设置（可选）
如果启用了防火墙，需要开放相关端口：
`sudo iptables -A INPUT -p tcp --dport 139 -j ACCEPT`
`sudo iptables -A INPUT -p tcp --dport 445 -j ACCEPT`
`sudo iptables -A INPUT -p udp --dport 137 -j ACCEPT`
`sudo iptables -A INPUT -p udp --dport 138 -j ACCEPT`
或者使用 ufw：
`sudo ufw allow Samba`


# 步骤 7: 测试共享
1. 在 Windows 或其他设备上打开文件管理器。
2. 输入地址，例如 \\archserver 或 \\<服务器IP地址>。
3. 访问共享目录。
如果遇到问题，可以查看 Samba 日志来排查错误：
`sudo journalctl -u smb`
`sudo journalctl -u nmb`