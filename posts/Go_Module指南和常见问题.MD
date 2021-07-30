---
title: "Go_Modules指南和常见问题"
date: 2021-07-30T22:11:41+08:00 
draft: false 
tags: ["go"]
---

# Go Modules简明使用

Go Modules是从go1.11开始初步支持，到现在的1.16、1.17默认开启，是目前go项目的推荐包管理方式。接下来让我们看一下go modules的简单使用方法。

## 使用Go Modules

### 初始化

```shell
# go mod init [包路径]
go mod init github.com/overstarry/go-mod-example
```

执行上述命令会生成go.mod文件

```shell
module github.com/overstarry/kratos-demo
go 1.16
```

### 添加依赖

在当前项目的目录中执行go get命令，会添加相应的库到mod文件中

```shell
go get github.com/go-kratos/kratos/v2
```

这个命令会在mod文件里添加以下信息

```shell
require (
	github.com/go-kratos/kratos/v2 v2.0.1
)
```

* 在 go get 的时候如果不手动指定版本信息，会自动拉取最新的版本的包
* 如果想要拉取指定版本可以通过 go get github.com/go-kratos/kratos/v2 v2.0.1 的方式，支持
    * @版本号 例如 `@v2.0.1`
    * @分支名 例如 @master
    * @commit tag 例如 @6cff360233dc4457f1536e4f3df4e4e740fd3410
    * // indirect 表示，我们在代码中没有直接应用这个包
* 执行完 go get 命令之后还会在目录下生成一个 go.sum 文件

```shell
github.com/go-kratos/kratos/v2 v2.0.0-rc7 h1:Qvpz07BefgMFQycSDb57NlWhtYGz4me3wh8E1naI9/k=
github.com/go-kratos/kratos/v2 v2.0.0-rc7/go.mod h1:/2bGobqE+/F9kKOe4Re0OO5X2NWNGt+7n2e8Y5DHFRc=
github.com/go-kratos/kratos/v2 v2.0.1 h1:iFteVlcLWnAQu5n4I5bTN63svW+0YylGwhNTYO2MkOQ=
github.com/go-kratos/kratos/v2 v2.0.1/go.mod h1:Jz6fuJFF2SLczQ7Y8ocKieVGgstQKa+R9NX09bCHekU=
```

这个文件主要包含当前依赖的所有的包.

### 清理未使用的依赖

通过go mod tidy命令可以清理在项目中未使用的依赖包。

### 生成依赖文件夹

如果你的项目需要切换到新的开发环境，需要重新下载相关的依赖，这时如果你的网络不好或者其它情况，会大大影响你的开发。这时通过go mod vendor命令，可以将原本下载在你的GoPath中的go mod缓存至你的项目根目录下。
可以有效的避免一系列问题。

### 下载依赖至本地缓存

使用 go mod download将你的项目依赖下载到本地缓存，一般是存放在GoPath的pkg目录的mod目录下。

# 常见问题

上面介绍完基础的使用方法，接下来介绍一些Go Modules常见的问题。

1. 拉取依赖很慢，有的包还拉取不到 Go 默认的 GOPROXY 配置是 proxy.golang.org , 默认国内无法访问，我们可以配置国内镜像，推荐 goproxy.cn 或者 goproxy.io
   ```shell
   go env -w GOPROXY=https://goproxy.cn,direct
   ```
   go1.16 之前 Go Module 并未默认开启还需要配置

    ```shell
    go env -w GO111MODULE=on
    ```
2. 公司私有仓库包如何获取 Go 获取包的时候默认会走 PROXY，这个只要你们的库没有对公网发布，那就获取不到，可以通过设置环境变量解决
   ```shell
   go env -w GOPRIVATE=overstarry.gitlab.com/xxx
   ```
3. 包的源代码仓库删库了怎么办？

   这个其实在 Go Modules 上还好一些，因为 Go Modules 默认使用 Go Proxy 只要你使用的库的 LICENSE 和 GOPROXY 没有问题，一般都会有缓存

   建议公司需要搭建一套自己的 GOPROXY 或者使用官方的 GOPROXY 或者是 goproxy.cn

# 参考链接

* https://github.com/golang/go/wiki/Modules