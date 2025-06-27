---
title: 优化Wine
date: 2025-6-14
category: linux
tag: tools
index_img: /img/qw1123.png
---

了解wine的几种版本：

[wine](https://archlinux.org/packages/?name=wine)（开发版） [wine-stable](https://aur.archlinux.org/packages/wine-stable/)（稳定版） [wine-staging](https://archlinux.org/packages/?name=wine-staging)（测试版本） [Wine Staging](https://wine-staging.com/) 是 [Wine](https://www.winehq.org/) 的修补版本, 其中包含了漏洞修复和尚未集成到稳定或开发分支中的特性

 

依赖

sudo pacman -S [wine-gecko](https://archlinux.org/packages/?name=wine-gecko) [wine-mono](https://archlinux.org/packages/?name=wine-mono)

sudo pacman -S winetricks

winetricks安装对应的驱动DLL及字体

[winecfg](https://wiki.winehq.org/Winecfg) 是Wine的GUI配置工具

[regedit](https://wiki.winehq.org/Regedit)是Wine的注册表编辑工具

[control](https://wiki.winehq.org/Control) 是Wine的Windows 控制面板的实现，可以通过运行 wine control 来启动

 

WINEARCH

Wine将默认启动一个64位环境，你可以改变它使用WINEARCH[环境变量](https://wiki.archlinuxcn.org/wiki/环境变量)，改名你的~/.wine目录并创建一个新的Wine环境通过使用 WINEARCH=win32 winecfg这将给你一个32位

 

WINEPREFIX

默认情况下，Wine 将其配置文件和安装的 Windows 程序存储在 ~/.wine ，通常会把此目录叫作Wine prefix或Wine bottle。当运行 Windows 程序或 Wine 的捆绑程序(如winecfg)时，它会自动创建/更新，前缀目录还包含一个目录，Windows 程序将其视为 C 盘

可以通过修改WINEPREFIX来创建一个分开的win32和win64环境

WINEARCH=win32 WINEPREFIX=~/.win32 winecfg

WINEPREFIX=~/.win64 winecfg

你也可以使用WINEARCH与其它Wine程序合并，例如winetricks（以Steam为例）

WINEARCH=win32 WINEPREFIX=~/.local/share/wineprefixes/steam winetricks steam

注意：如果是分开创建的win32和win64环境，运行为Windows程序必须使用WINEPREFIX指定环境，不然默认是~/.wine

\# 例如env WINEPREFIX=~/.win-a wine program-a.exe和env WINEPREFIX=~/.win-b wine program-b.exe ，这两个程序将分别在 ~/.win-a 和 ~/.win-b 路径下有各自单独的 C 盘目录和注册表配置

 

注意： Wine不是[沙盒](https://en.wikipedia.org/wiki/Sandbox_(computer_security))！运行在Wine下的程序仍然可以访问外部系统的内容！（例如Wine 会默认将 / 映射到 Z:）

若要在不运行 Windows 程序或其他图形工具的情况下创建前缀，可以执行：

env  WINEPREFIX=~/.win64  wineboot  -u

桌面启动器

当使用Windows应用安装程序创建一个快捷方式时，Wine会创建 [.desktop](https://wiki.archlinuxcn.org/wzh/index.php?title=.desktop&action=edit&redlink=1) 文件，Arch Linux中这些文件的默认位置是：

桌面快捷方式位于 ~/Desktop 下

开始菜单图标位于 ~/.local/share/applications/wine/Programs/ 下

注意： Wine不支持为所有用户安装Windows应用程序，所以Wine不会把 .desktop 文件放到 /usr/share/applications 下

Wine当前并不直接支持Wayland，但你可以使用 [XWayland](#XWayland)

[XWayland](https://wayland.freedesktop.org/xserver.html) 是一个运行在Wayland之下的 X 服务器，为尚未提供Wayland支持的本地X11应用程序提供兼容性。要使用 XWayland，请安装 [xorg-xwayland](https://archlinux.org/packages/extra/x86_64/xorg-xwayland/) （默认已经有）软件包

安全性：XWayland 是一个 X 服务器，因此它不具备 Wayland 的安全特性

性能：XWayland 的 [性能几乎与 X11 相同](https://openbenchmarking.org/result/2202053-NE-NVIDIARTX35)。在某些情况下，可能会有肉眼可见的性能下降，尤其是在 NVIDIA 显卡上

兼容性：XWayland不完全向后兼容 X11某些应用程序可能无法在XWayland下正常运行

 

配置wine环境

手柄和游戏杆的 32 位支持可以安装 [lib32-sdl2](https://archlinux.org/packages/?name=lib32-sdl2)

C++和微软.NET：

env  WINEPREFIX=~/.win64 wine VCRedistSetup.exe

env  WINEPREFIX=~/.win64  wine  微软.NET.exe

env  WINEPREFIX=~/.win32  wine  VCRedistSetup.exe

env  WINEPREFIX=~/.win64  winetricks  3dx9  d3dx10  d3dx11

 