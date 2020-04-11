---
title: 'Go:Windows开发配置'
comments: true
typora-root-url: ../../source/
date: 2020-03-20 20:15:37
tags:
- go
- 开发环境
---

Windows开发配置

<!--more-->

# 下载安装

 https://golang.org/dl/ 

安装路径为$GOROOT

# 环境变量配置

安装完成后会自动在系统变量Path里增加$GOROOT/bin

手动在用户变量里增加GOPATH，对应Go的工作目录，并在该目录下新建三个文件夹`bin`,`src`,`pkg`

![1584706830706](/imgs/1584706830706.png)

# 代理

## goproxy

在1.13版本之后可通过下面的方式配置go的环境变量

```bash
go env -w GOPROXY=https://goproxy.io,direct
# Set environment variable allow bypassing the proxy for selected modules
go env -w GOPRIVATE=*.corp.example.com
go env -w GO111MODULE=on
```

经测试，经proxy配置为`https://goproxy.io,direct`时go get命令较快

当`GO111MODULE=on`时，go install 会提示找不到mod文件，此时需要使用`go mod init project_name`新建项目，或者将`GO111MODULE`置为`auto`

## bash proxy

## cmd proxy

## git proxy

# 测试

`go version` `go env`

```bash
D:\Project\web\blog>go version
go version go1.14.1 windows/amd64

D:\Project\web\blog>go env
set GO111MODULE=on
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\choco\AppData\Local\go-build
set GOENV=C:\Users\choco\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOINSECURE=
set GONOPROXY=
set GONOSUMDB=
set GOOS=windows
set GOPATH=D:\Project\go
set GOPRIVATE=
set GOPROXY=https://goproxy.io,direct
set GOROOT=c:\go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=c:\go\pkg\tool\windows_amd64
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=NUL
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\choco\AppData\Local\Temp\go-build035265762=/tmp/go-build -gno-record-gcc-switches
```

# vscode

安装下面的插件

![1584707140314](/imgs/1584707140314.png)

`ctrl+shift+p`

![1584707168653](/imgs/1584707168653.png)

可将下面的选项全选

![1584707188615](/imgs/1584707188615.png)

可能会有部分安装失败，这部分可在将proxy配置为`https://goproxy.io,direct`后在终端内分别安装，即`go get -v golang.org/x/tools/cmd/gomports`![1584707216660](/imgs/1584707216660.png)

