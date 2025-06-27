---
title: 解决google-chrome、edge浏览器在wayland撕裂问题
date: 2025-6-5
category: linux
tag: binary system
index_img: /img/qw1113.png
---

vim /usr/share/applications/.desktop

Exec=/usr/bin/microsoft-edge-stable --disable-gpu %U

--ozone-platform=wayland --enable-features=WaylandWindowDecorations（这个会造成浏览器输入不了中文）
