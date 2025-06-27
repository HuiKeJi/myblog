---
title: 安装grub、efibootefi
date: 2025-6-3
category: linux
tag: tools
index_img: /img/default.png
---

> [!NOTE]
>
> 这里在安装系统时已经安装

```
sudo pacman -S grub efibootefi
```

> [!CAUTION]
>
> - 确保你安装 GRUB 软件包和运行命令的系统是你想用 GRUB 引导的系统。也就是说如果你是通过安装介质引导的，你需要在 chroot 之后再运行 `grub-install`。如果因为某些原因不得不在安装的系统之外运行 `grub-install`，在后面加上 `--boot-directory=` 选项来指定挂载 `/boot` 目录的路径，例如 `--boot-directory=/mnt/boot`。
>
> - 某些主板无法处理包含空格的 `bootloader-id`
>
>   ```
>    sudo grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB
>   ```

#### 最后生成 grub.cfg

> [!NOTE]
>
> 默认的文件路径是 `/boot/grub/grub.cfg`
>
> 使用 grub-mkconfig 工具来生成

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```



#### 以上是正常安装grub必备的内容，可以跳过不需要

> [!IMPORTANT]
>
> 编辑以下文件，并加入代码块内容
>
> ```
> sudo vim /etc/grub.d/40_custom
> ```

```
#!/bin/sh
exec tail -n +3 $0

# This file provides an easy way to add custom menu entries.  Simply type the

# menu entries you want to add after this comment.  Be careful not to change

# the 'exec tail' line above.

menuentry "Windows 11" --class windows --class os {
    insmod part_gpt
    insmod fat
    insmod chain
    search --no-floppy --set=root --fs-uuid F69C-7E34
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
```

##### 最后生成 grub.cfg

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

