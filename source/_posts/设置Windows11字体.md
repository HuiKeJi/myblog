---
title: 设置Windows字体
date: 2025-6-6
category: linux
tag: fonts
index_img: /img/woman/wallhaven-d695zg.jpg
---

> [!NOTE]
>
> 创建本地存放字体文件夹
>
> ```
> sudo mkdir /usr/local/share/fonts
> ```
>
> 将字体复制到文件夹内
>
> ```
> sudo cp -r  /windows/Windows/Fonts /usr/local/share/fonts/WindowsFonts/
> ```
>
> 添加权限
>
> ```
> sudo chmod 644 /usr/local/share/fonts/WindowsFonts/*
> ```
>
> 然后重新生成字体缓存
>
> ```
> sudo fc-cache --force
> ```
