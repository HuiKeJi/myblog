---
title: ArchLinux Installation
date: 2025-6-9
category: linux
tag: system installation
index_img: /img/wallhaven-e8omk8.jpg
---

## 1.连接到互联网
#### 1.1 首先，使用 rfkill 命令确认 Wi-Fi 设备未被屏蔽,如果不知道你的网络设备名称，请列出所有 WiFi 设备：
`device list`
#### 1.2 如果设备或其相应的适配器已关闭，请将其打开
`device name set-property Powered on`
`adapter adapter set-property Powered on`
#### 1.3 然后，要开始扫描网络（注意：这个命令不会输出任何内容），执行：
`station name scan`
#### 1.4 再然后，就可以列出所有可用的网络：
`station name get-networks`
#### 1.5 最后，要连接到一个网络：
`station name connect SSID`
#### 1.6 输入密码即可
`ping archlinux.org`
##### 注意： 默认情况下，安装映像在启动时已经预先配置好并启用了 systemd-networkd、systemd-resolved、iwd 和 ModemManager。但在已经安装完成了的系统之中并非如此

## 2.更新系统时间
#### 在 Live 环境中 systemd-timesyncd 默认启用，也就是说当系统已经创建互联网连接后，系统时间将自动同步
#### 使用 timedatectl确保系统时间是同步的
`timedatectl`

## 3.创建硬盘分区
#### 系统如果识别到计算机的内置硬盘、U盘或者移动硬盘等类型磁盘，就会将其分配为一个块设备，如 /dev/sda、/dev/nvme0n1 或 /dev/mmcblk0。可以使用 lsblk 或者 fdisk 查看
`fdisk -l（此处为小写字母l）`
#### 结果中以 rom、loop 或者airootfs结尾的设备可以被忽略。结果中以 rpbm、boot0 或者 boot1 结尾的 mmcblk* 设备也可以被忽略
#### 对于一个选定的设备，以下分区是必须要有的：
| 已安装系统上的挂载点 | 分区                      | 分区类型                | 建议大小                 |
| -------------------- | ------------------------- | ----------------------- | ------------------------ |
| /boot                | /dev/efi_system_partition | EFI 系统分区            | 1 GiB                    |
| SWAP                 | /dev/swap_partition       | Linux Swap （交换空间） | 至少4GiB                 |
| /                    | /dev/root_partition       | Linux x86-64根目录 (/)  | 设备剩余空间至少23-32GiB |

# 4.格式化分区

#### 4.1 创建分区后，必须使用合适的文件系统对每个新创建的分区进行格式化
#### 要在根分区 /dev/root_partition 上创建一个 Ext4 文件系统，请运行
`mkfs.ext4 /dev/root_partition（根分区）`
#### 4.2 如果你要创建一个 EFI 系统分区，使用 mkfs.fat将其格式化为Fat32
#### 警告： 只有在分区步骤中创建EFI系统分区时才需要格式化。如果这个磁盘上已经有一个 EFI 系统分区了，将它重新格式化会破坏其他已安装操作系统的引导加载程序
`mkfs.fat -F 32 /dev/efi_system_partition（EFI 系统分区）`
#### 4.3 如果创建了交换分区，请使用 mkswap将其初始化
`mkswap /dev/swap_partition（交换空间分区）`

## 5.挂载分区4.格式化分区
#### 5.1 将根磁盘卷挂载到 /mnt
`mount /dev/root_partition（根分区） /mnt`
#### 5.2 然后使用 mkdir 在 /mnt 下创建任何剩余的挂载点（例如，为/boot 而创建/mnt/boot），并按相应的层级顺序挂载相应的磁盘卷
#### 提示：使用 --mkdir 选项运行 mount来创建指定的挂载点。或者，先使用 mkdir 创建挂载点再挂载
#### 注意： 挂载分区一定要遵循顺序，先挂载根（root）分区（到 /mnt），再挂载引导（boot）分区（到 /mnt/boot 或 /mnt/efi，如果单独分出来了的话），最后再挂载其他分区。否则您可能遇到安装完成后无法启动系统的问题
#### 对于 UEFI 系统，挂载 EFI 系统分区
`mount --mkdir /dev/efi_system_partition /mnt/boot`
#### 5.3 如果创建了交换空间卷，请使用 swapon 启用它
`swapon /dev/swap_partition（交换空间分区）`

## 6.安装系统
#### 6.1 选择镜像源/etc/pacman.d/mirrorlist 中定义了软件包会从哪个镜像站下载，在 LiveCD 启动的系统上，且在连接到互联网后，reflector 会通过选择 20 个最新同步的 HTTPS 镜像站并按下载速率对其进行排序来更新镜像列表
#### 在列表中，越靠前的镜像站在下载软件包时，就会有越高的优先级。请您检查 /etc/pacman.d/mirrorlist 文件，看看列出的镜像站的顺序是否合适。如果不合适，可以手动编辑文件，将离您所处地理位置最近的镜像挪到文件的头部，同时也应该考虑一些其他的评判标准
#### 如果 /etc/pacman.d/mirrorlist 文件中没有合适的镜像站，可以手动从 archlinux 官方网站的镜像站列表下载一份。这需要您所在的网络能够正常访问 archlinux 官方网站。例如，使用 curl下载位于中国大陆的 HTTPS 镜像站：
`Curl -L 'https://archlinux.org/mirrorlist/?country=CN&protocol=https' -o /etc/pacman.d/mirrorlist`
#### 或者，也可以通过安装pacman-mirrorlis包 来获取按国家分列的原始镜像列表。这么做的缺点是pacman仍然会使用当前配置中的镜像站来下载软件包数据库和pacman-mirrorlis包，这可能会很慢。在挑选了能用的镜像之后，可以执行
`pacman -Sy pacman-mirrorlist`
#### 再将/etc/pacman.d/mirrorlist.pacnew复制到/etc/pacman.d/mirrorlist并进行编辑或者，如果您记得想要使用的镜像站的URL可以手动编辑/etc/pacman.d/mirrorlist文件并手动输  入 URL
#### 6.2 使用 pacstrap脚本，安装base包软件包和Linux 内核以及常规硬件的固件
`pacstrap -K /mnt base linux linux-firmware base-devel networkmanager openssh dhcpcd vim net-tools`
#### 这时候可以同时额外安装计算机的 CPU 微码包，使用 intel-ucode和amd-ucode包。也可以暂时都不安装，等到进入系统后再安装
#### 新安装的系统中是没有文本编辑器的，所以请您先安装文本编辑器如nano或者vim

## 7.生成fstab文件
#### 7.1 通过以下命令生成 fstab 文件（用 -U 或 -L 选项设置UUID或卷标）
`genfstab -U /mnt > /mnt/etc/fstab`
#### 7.2 强烈建议在执行完以上命令后，检查一下生成的 /mnt/etc/fstab 文件是否正确
`cat /mnt/etc/fstab`

## 8.配置新安装的系统
#### 8.1 通过以下命令 chroot 到新安装的系统
`arch-chroot /mnt`
#### 8.2 设置时区：
`ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`
#### 8.3 运行 hwclock以生成/etc/adjtime
`hwclock --systohc`
#### 8.4 区域和本地化设置
#### 程序和库如果需要本地化文本，都依赖区域设置，后者明确规定了地域、货币、时区日期的格式、字符排列方式和其他本地化标准
#### 需要设置这两个文件locale.gen与locale.conf
#### 编辑/etc/locale.gen取消掉 en_US.UTF-8 UTF-8 和其他需要的 UTF-8 区域设置前的注释#
`vim /etc/locale.gen`
#### 8.5 接着执行locale-gen以生成locale信息
`locale-gen`
#### 8.6 然后创建locale.conf文件并编辑设定LANG变量
`/etc/locale.conf`
`LANG=en_US.UTF-8`
#### 8.7 设置主机名（zjh@archnotebook）
`vim /etc/hostname`
#### 8.8 设置网络（可跳过）
`vim /etc/hosts`

- 127.0.0.1	localhost
- ::1	localhost
- 127.0.1.1	主机名.localdomain	主机名

#### 8.9 设置root密码
`passwd root`

## 9.安装GRUB引导程序
#### 9.1 首先安装软件包grub和efibootmgr其中GRUB是启动引导器，efibootmgr被GRUB脚本用来将启动项写入NVRAM，想要让grub-mkconfig探测其他已经安装的系统引导并自动把他们添加到启动菜单中，必须安装os-prober包，要挂在ntfs分区必须用到ntfs-3g包
`pacman -S grub efibootmgr os-prober ntfs-3g`
#### 9.2 注意--efi-directory和--bootloader-id是GRUB UEFI特有的--efi-directory替代了已经废弃的--root-directory您可能注意到在grub-install命令中没有device_path 选项（例如 /dev/sda）事实上即使提供了device_path，也会被GRUB UEFI安装脚本忽略，因为 UEFI 启动加载器不使用MBR启动代码或启动扇区
`grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB`
#### 9.3 如果你使用了--removable选项，那GRUB 将被安装到esp/EFI/BOOT/BOOTX64.EFI（当使用i386-efi时是esp/EFI/BOOT/BOOTIA32.EFI ），此时即使 EFI 变量被重设或者你把这个驱动器接到其他电脑上，你仍可从这个驱动器上启动。通常来说，你只要像操作BIOS 设备一样在启动时选择这个驱动器就可以了。如果设备是同时安装了Windows的多引导启动，注意 Windows 通常会在这里安装一个 EFI 可执行程序，该程序的目的是仅重建Windows的UEFI启动项。如果你想在Mac上安装GRUB，那你必须要使用该选项。某些台式机主板只会在此位置寻找 EFI 可执行文件，因此该选项是必需的，尤其是微星（MSI）主板。 如果你更新了UEFI，启动项可能会在更新后丢失。因此可以创建一个removable启动项作为后备
`grub-install --target=x86_64-efi --efi-directory=esp --removable`
#### 9.4 生成主配置文件
`grub-mkconfig -o /boot/grub/grub.cfg`
#### 9.5 退出arch-chroot
#### 输入 exit 或按 Ctrl+d 退出 chroot 环境
#### 可选用 umount -R /mnt 手动卸载被挂载的分区：这有助于发现任何“繁忙”的分区
#### 最后，通过执行 reboot 重启系统，systemd 将自动卸载仍然挂载的任何分区。这时候不要忘记移除安装介质，然后使用 root 账户登录到新系统
#### 重启之后以root用户登录

## 10.开启网络创建用户
#### 10.1 开启NetworkManager服务
`systemctl enable --now NetworkManager`
#### 10.2 使用NetworkManager附带nmcli连接网络
- 显示附近的Wi-Fi网络
- nmcli device wifi list
- 连接到Wi-Fi网络
- nmcli device wifi connect SSID_或_BSSID password 密码
- 连接到隐藏的Wi-Fi网络
- nmcli device wifi connect SSID_或_BSSID password 密码 hidden yes
- 连接到wlan1网络接口上的 Wi-Fi
- nmcli device wifi connect SSID_或_BSSID 
- password 密码 ifname wlan1 profile_name
- 断开网络接口上的连接：
- nmcli device disconnect ifname eth0
- 显示连接列表及其名称、UUID、类型和支持设备
- nmcli connection show
- 激活连接（即使用现有配置文件连接到网络）
- nmcli connection up name_或_uuid
- 删除连接
- nmcli connection delete name_或_uuid
- 显示所有网络设备及其状态
- nmcli device
- 关闭 Wi-Fi
- nmcli radio wifi off

#### 10.3 创建普通用户
`useradd -m -G wheel zjh`
#### 10.4 将新用户加入sudoers中
`vim /etc/sudoers 取消 %wheel ALL=(ALL:ALL)  ALL的注释`

## 11.安装桌面环境
~~pacman -S plasma kde-applications sddm~~
`pacman -S plasma-meta sddm bash-completion`
`pacman -S nvidia`
`systemctl enable sddm`
`reboot`


## 12.源列表+32位源

##### Mirrorlist的China源
- Server = https://mirrors.kernel.org/archlinux/$repo/os/$arch
- Server = http://mirrors.163.com/archlinux/$repo/os/$arch
- Server = https://mirrors.aliyun.com/archlinux/$repo/os/$arch
- Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch
- Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
- Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
- Server = https://mirrors.pku.edu.cn/archlinux/$repo/os/$arch

#### 12.1 开启pacman.conf中的32位源
##### - 去除以下注释
[multilib]
Include = /etc/pacman.d/mirrorlist
##### - 加入以下源
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
#### 12.2 安装archlinuxcn-keyring包前的额外步骤
##### - 在2023年后，安装archlinuxcn-keyring时可能会出现错误，以下提示
`error: archlinuxcn-keyring: Signature from"Jiachen YANG(Arch Linux Packager Signing Key)” is marginal trust`
##### - 需要在本地信任farseerfc的GPG key
`sudo pacman-key --lsign-key "farseerfc@archlinux.org"`
`sudo pacman -S archlinuxcn-keyring`
##### - 安装yay
`sudo pacman -S yay`


## 13.安装显卡驱动
#### 13.1 intel核显驱动（cpu不带F的需要安装）
`sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel`
#### 13.2 nvidia显卡驱动
`sudo pacman -S nvidia nvidia-settings lib32-nvidia-utils`

#### 13.3 显卡双切方案（一）
#### 安装optimus-manager以及其图形前端optimus-manager-qt通过该程序可以在集成显卡和独立显卡间轻松切换，optimus-manager提供三种模式，分别为仅用独显、仅用集显以及hybrid动态切换模式
`yay -S optimus-manager optimus-manager-qt`
`sudo systemctl enable optimus-manager.service`
#### 重启即可使用

#### 13.4 显卡双切方案（二）
#### - 安装envycontrol
`yay -S envycontrol`
#### - 动态模式
`sudo envycontrol -s hybrid`
#### - nvidia独立显卡模式
`sudo envycontrol -s nvidia`
#### - integrated核显模式
`sudo envycontrol -s integrated`
