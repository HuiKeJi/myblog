---
title: arch安装ollama
date: 2025-6-2
category: linux
tag: AI
index_img: /img/wallhaven-9o51l8.jpg
---

##### 安装ollama

`sudo pacman -S ollama`

##### 编辑服务
`sudo systemctl edit ollama.service`

##### 添加如下
`[Service]`
`Environment="OLLAMA_HOST=0.0.0.0"`
##### 文件位置，切记内容只有这两行代码，注释等信息都不能有
`/etc/systemd/system/ollama.service.d/override.conf`
##### 打开服务
`sudo systemctl enable --now ollama.service`


# 安装模型
##### 手动安装推荐
##### 创建模型存放文件夹
`/home/zjh/.ollama/models/blobs/`
##### 将gguf的模型重命名为哈希值
##### 首先获取哈希值
`shasum -a 256 gguf模型`

##### 创建Modelfile文件夹
`/home/zjh/.ollama/models/Modelfile/`
##### 创建Modelfile文件
`vim Modelfile`
##### 添加如下代码
`FROM /home/zjh/.ollama/models/blobs/33981adf6bae52c503fb5c24f72539010632f7ed290a56c1315a8cd50adca587.gguf`

##### 创建模型运行
`ollama create Nous-Hermes-2-Mistral-7B-DPO.Q8_0 -f Modelfile`

##### 运行ollama查看模型
`ollama list`

# Docker从安装到代理
##### 安装docker包
`sudo pacman -S docker`
##### 启动docker.service服务
`sudo systemctl start docker.service`
##### 也可以使用docker.socket将会在第一次启动Docker时启动，使用后者可以减少开机启动时间
##### 如果你想以普通用户身份运行docker的话，添加你自己到 docker 用户组
`gpasswd  -a  [用户名]  [组名]`
##### 将用户从组中移除
`gpasswd  -d  [用户名]  [组名]`
##### 这里需要重新启动archlinux或者重新登录并重启docker.service
`sudo systemctl daemon-reload`
`sudo systemctl restart docker.service`
##### 查看当前配置
`sudo docker info`

# 配置代理
##### 在执行docker pull时，是由守护进程dockerd来执行。因此代理需要配在dockerd的环境中。而这个环境则是受systemd所管控
`sudo mkdir -p /etc/systemd/system/docker.service.d`
`sudo vim /etc/systemd/system/docker.service.d/proxy.conf`
##### 键入proxy.conf文件（可以是任意*.conf的形式）
[Service]
Environment="HTTP_PROXY=socks5://127.0.0.1:1080/"
Environment="HTTPS_PROXY=socks6://127.0.0.1:1080/"
Environment="NO_PROXY=localhost,.example.com"
##### 注：HTTP_PROXY或HTTPS_PROXY 分别用于代理访问 socks5请求（也可以是http或https请求，根据自己代理为准），如果想某个 IP或域名不走代理则配置到 NO_PROXY中
##### 注：socks5://127.0.0.1:1080/必须是免密代理
##### 最后重启docker
`sudo systemctl daemon-reload`
`sudo systemctl restart docker.service`

# 安装openwebui
`docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main`

Open WebUI 设置
OpenAI API 管理
OpenAI API连接 https://api.openai.com/v1
Ollama API 管理
Ollama API连接 http://host.docker.internal:11434

最后浏览器打开http://localhost:3000
