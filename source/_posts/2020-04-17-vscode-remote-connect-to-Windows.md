---
title: vscode-remote-connect-to-Windows
comments: true
typora-root-url: ../../source/
date: 2020-04-17 11:56:30
tags:
- vscode
- Windows
---

vscode的remote插件可以通过ssh连接到远程服务器，本地化开发远端的工程，并可以使用远程机器的终端环境。本文主要记录如何使用remote连接到远程的WIndows。

https://zhuanlan.zhihu.com/p/122999157

<!--more-->

# 安装OpenSSH Server

OpenSSH 客户端和服务器是 Windows 10 1809 的可安装功能。

若要安装 OpenSSH，请启动“设置”，然后转到“应用”>“应用和功能”>“管理可选功能”。

扫描此列表，查看 OpenSSH 客户端是否已安装。 如果没有，则在页面顶部选择“添加功能”，然后：

- 若要安装 OpenSSH 客户端，请找到“OpenSSH 客户端”，然后单击“安装”。
- 若要安装 OpenSSH 服务器，请找到“OpenSSH 服务器”，然后单击“安装”。

安装完成后，请返回“应用”>“应用和功能”>“管理可选功能”，你应当会看到列出的 OpenSSH 组件。

在“服务”中可以启动、停止、重新启动OpenSSH SERVER。

![image-20200417121818831](/imgs/image-20200417121818831.png)

# 配置OpenSSH Server

配置文件在`C:\ProgramData\ssh\sshd_config`中

注释掉最后两行

```
#Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

```bash
Start-Service sshd
# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'
# Confirm the Firewall rule is configured. It should be created automatically by setup. 
Get-NetFirewallRule -Name *ssh*
# There should be a firewall rule named "OpenSSH-Server-In-TCP", which should be enabled
# If the firewall does not exist, create one
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

#配置密钥登录

1. 在客户端处生成用户密钥

```bash
cd ~\.ssh\
ssh-keygen
```

得到rsa密钥对

```
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2018/3/30     11:33           1679 id_rsa
-a----        2018/3/30     11:33            398 id_rsa.pub
```

2. 将公钥拷贝到服务器的`~/.ssh/authorized_keys`中

   

