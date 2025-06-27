---
title: conky使用
date: 2025-6-10
category: linux
tag: tools
index_img: /img/wallhaven-9o55qd.jpg
---

#### 安装conky

```
sudo pacman -S conky
```

#### 安装依赖

```
sudo pacman -S mesa-utils brightnessctl 
```

#### 编辑用户目录下配置文件

```
vim ~/.config/conky/comprehensive.conf
```

> [!IMPORTANT]
>
> 加入

```
conky.config = {
    background = true,
    update_interval = 1,
    total_run_times = 0,
    own_window = true,
    own_window_type = 'desktop',
    own_window_transparent = true,
    own_window_argb_visual = true,
    own_window_argb_value = 0,
    double_buffer = true,
    draw_shades = false,
    draw_outline = false,
    draw_borders = false,
    alignment = 'top_right',
    gap_x = 30,
    gap_y = 60,
    minimum_width = 250,
    minimum_height = 400,
    default_color = 'white',
    use_xft = true,
    font = 'Ubuntu Mono:size=12',
};

conky.text = [[
${color cyan}日期时间：${time %Y-%m-%d %H:%M:%S}
${color cyan}主机名称：${nodename}
${color cyan}系统信息：${sysname} ${kernel} on ${machine}
${color cyan}运行时间：${uptime}
${color cyan}当前用户：${exec whoami}
${color white}------------------------------------------------------------
${color red}CPU使用率：${cpu}%  ${cpubar 6,150}
${color red}内存使用：${mem} / ${memmax} ${membar 6,150}
${color red}交换分区：${swap} / ${swapmax} ${swapbar 6,150}
${color white}------------------------------------------------------------
${color blue}硬盘使用：${fs_used /} / ${fs_size /} ${fs_bar 6,150}
${color blue}当前显卡：${execi 60 glxinfo | grep "OpenGL renderer" | cut -d: -f2 | sed 's/^[ \t]*//'}
${color blue}当前桌面：${exec echo $XDG_SESSION_TYPE}
${color white}------------------------------------------------------------
${color green}网络流量：
${color green}下载：${downspeed wlo1} kB/s  上传：${upspeed wlo1} kB/s
${color green}IP：${addr wlo1}
${color green}WiFi 状态
${color green}SSID: ${exec nmcli -t -f active,ssid dev wifi | grep '^是' | cut -d: -f2}
${color green}信号强度: ${exec nmcli -t -f signal dev wifi | sort -nr | head -n 1}%
${color white}------------------------------------------------------------
${color pink}屏幕亮度: ${exec brightnessctl g | awk '{print int($1/100 * 100) "%"}'}
${color pink}温度：${execi 10 sensors | grep 'Package id 0:' | cut -d' ' -f5}

# ${color pink}电池状态：${battery_short BAT0} ${battery_bar 6,150 BAT0}

${color pink}剩余电量：${battery_percent}%
${color pink}爱小旋的一天！！！
]];
```

> [!CAUTION]
>
> 这样就可以在系统屏幕上显示小卡片信息，还不能开机自启，需要添加desktop文件

```
sudo vim /etc/xdg/autostart/conky.desktop
```

```
vim ～/.config/autostart/conky.desktop
```

> [!TIP]
>
> 这两个文件自启路径都可以，加入以下内容

```
[Desktop Entry]
Type=Application
Exec=sh -c "sleep 5 && conky -c /home/zhh/.config/conky/comprehensive.conf"
Hidden=false
NoDisplay=false
Name=Conky
Comment=启动 Conky 桌面监视器
```



