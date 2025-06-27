---
title: asusctl的使用
date: 2025-6-10
category: linux
tag: tools
index_img: /img/qw1111.png
---

asusctl --help

asusctl aura static -c 27B7A1


asusctl profile --profile-set Quiet         # 静音模式（通常可以调节亮度）
asusctl profile --profile-set Balanced      # 均衡模式
asusctl profile --profile-set Performance   # 性能模式（可能调不了亮度）


Starting version 6.1.12
Missing arg or command

Optional arguments:
  -h, --help          print help message
  -n, --next          toggle to next profile in list
  -l, --list          list available profiles
  -p, --profile-get   get profile       # 查看当前模式
  -P, --profile-set   set the active profile    # 设置当期模式
