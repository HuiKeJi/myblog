---
title: 自动挂载NTFS硬盘
date: 2025-6-7
category: linux
tag: mount
index_img: /img/woman/mmexport1749119873081.jpeg
---

- 查看分区信息


```
sudo fdisk -l
```

- 读取UUID码


```
sudo blkid /dev/nvme0n1p6
```

- 编辑fstab加入UUID信息


```
sudo vim /etc/fstab
```

- 键入/dev/nvme0n1p6


```
UUID=23423423242	/run/media/win11tools	ntfs-3g		defaults	0	0
```

> [!NOTE]
>
> win11tools自定义创建即可
>
> ```
> sudo mkdir /run/media/win11tools
> ```
>
> ntfs-3g是挂载的ntfs硬盘
>
> 最后重启即可

 