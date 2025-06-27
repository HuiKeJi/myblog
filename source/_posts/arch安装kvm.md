---
title: Arch安装Kvm
date: 2025-6-8
category: linux
tag: tools
index_img: /img/wallhaven-vpmw83.jpg
---

## 安装必备的程序

`sudo pacman -S qemu virt-manager virt-viewer dnsmasq vde2 bridge-utils openbsd-netcat`
:: 有 5 个软件包可提供 qemu ：
:: 软件仓库 extra

   1) qemu-base  2) qemu-desktop  3) qemu-full
:: 软件仓库 archlinuxcn
   4) qemu-git  5) qemu-headless-git

输入某个数字 ( 默认=1 ): 这里推荐：2

## 开启服务
`sudo systemctl enable --now libvirtd.service `

## 将普通用户添加进组
`sudo usermod -aG libvirt $(whoami)`

## 查看用户都在那些组
`groups zjh`

## kvm安装windows11出现不满足系统要求时
- 在虚拟机中按住快捷键Shift+F10打开终端界面
- 输入regedit打开注册表
- 在HKEY_LOCAL_MACHINE\SYSTEM\Setup右击新建一个项，命名为LabConfig
- 在该项右击新建三个DWORD (32位) 值，分别命名为BypassTPMCheck、BypassRAMCheck、BypassSecureBootCheck，并将这三个值都设置为1

## windows11跳过联网激活
- 在联网界面按【Shift+F10】或【Shift+Fn+F10】，弹出命令提示符窗口 
- 在命令提示符窗口中输入：OOBE\BYPASSNRO，敲回车电脑重启 
- 再次回到连接网络界面，点击“我没有Internet连接”即可 
- 接着点击“继续执行受限设置”即可
- 安装win11的时候尽量将网卡移除省得更新慢
- 进入win11之后将CDROM换成virtio-win，win11系统安装即可


## 当windows有时启动时出错: Requested operation is not valid: network 'default' is not active

### 意思是：虚拟机尝试启动时，使用的网络（名称为 default）当前未激活。


🔧 解决方法如下：

✅ 方法一：在终端中激活默认网络

1. 打开终端

2. 输入以下命令激活默认网络：

sudo virsh net-start default

3. 设置默认网络开机自动启动（可选）：

sudo virsh net-autostart default

✅ 方法二：检查 libvirtd 服务状态

有时是因为 libvirtd 没有正常运行：

sudo systemctl start libvirtd
sudo systemctl enable libvirtd

✅ 方法三：如果网络配置丢失（default 网络不存在）

你可以重新定义 default 网络：

1. 创建一个名为 default.xml 的配置文件，内容如下：

<network>
  <name>default</name>
  <bridge name='virbr0' stp='on' delay='0'/>
  <forward mode='nat'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>

2. 然后导入并启动它：

sudo virsh net-define default.xml
sudo virsh net-start default
sudo virsh net-autostart default

