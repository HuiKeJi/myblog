---
title: arch安装Fcitx5
date: 2025-6-1
category: linux
tag: input method
index_img: /img/wallhaven-o5zmwm.png
---

#### 安装fcitx5-im此包组提供fcitx5本体以及fcitx5-qt、fcitx5-gtk、fcitx5-configtool等

```
sudo pacman -S fcitx5-im
```

#### 安装词汇fcitx5-pinyin-zhwiki包，是由felixonmars 根据中文维基百科创建的词库。适用于 拼音输入法

#### 安装主题fcitx5-material-color包，提供了类似微软拼音的外观

```
yay -S fcitx5-pinyin-zhwiki fcitx5-material-color
```

#### 编辑 /etc/environment 并添加以下几行，然后重新登录

```
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS=@im=fcitx5
SDL_IM_MODULE=fcitx5
INPUT_METHOD=fcitx5
GLFW_IM_MODULE=fcitx5
```

#### 添加fcitx5主题

> 将主题放在如下目录中，设置主题前往 Fcitx5设置 -> 配置附加组件 -> 经典用户界面 -> 主题 

```
/usr/share/fcitx5/themes/
```

