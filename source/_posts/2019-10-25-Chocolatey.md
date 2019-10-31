---
title: Chocolatey
typora-root-url: ../../source/
date: 2019-10-25 16:02:37
tags:
---
Windows下的包管理器Chocolatey的使用

https://www.cnblogs.com/ys-wuhan/p/6395417.html

<!--more-->

1. 安装方法 https://chocolatey.org/docs/installation

```bash
# cmd.exe
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"


# powershell.exe
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

# powershell v3+
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```

2. 代理设置

```bash
choco config set proxy http://127.0.0.1:1080
```

3. 常用命令：https://chocolatey.org/docs/commands-reference

```bash
choco search 关键字

choco install 软件包名称

choco upgrade 软件包名称

choco uninstall 软件包名称
```

4. 仓库地址：https://chocolatey.org/packages/

5. 安装位置设置

 环境变量中增加 ChocolateyToolsLocation ： 想安装的地点