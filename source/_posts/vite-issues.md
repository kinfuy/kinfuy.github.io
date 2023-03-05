---
title: 【开源那些事】vite issues 蹲点计划？
cover: /static/articles/vite.webp
date: 2023.3.6 12:00:00
categories:
  - Vuejs
tags:
  - vite 
  - 开源
---

## 序
去年一直有计划多参与到开源社区中，所以没事就会去vite issues 下逛逛看看有没有自己可以解决的问题，这不就被我混到几个。😄

目前提2个issues，提了5个PR (成功合并两个半)为啥是两个半，因为有一个合并了被revert了，不过用到还是我初始思路。

## 我遇到了什么？

### 1、use --host duplicate port number

这是最近遇到的一个问题，vite 使用--port 的时候启用端口号会重复使用。

> When localhost(vite dev) is listening on port 5173, it's possible for 127.0.0.1(vite dev --host) to listen on port 5173 again.

相关issues
[#12144](https://github.com/vitejs/vite/issues/12144)
[#12205](https://github.com/vitejs/vite/issues/12205)
[#10651](https://github.com/vitejs/vite/pull/10651)
[#10638](https://github.com/vitejs/vite/issues/)

#### 尝试解决 
<https://github.com/vitejs/vite/pull/12211> 详细PR

经过我一系列的测试：

> According to my test:
> 
> Set -- host, and then httpServer.listen passes the host as undefined, and do not set the pass localhost
> 
> I start the first service, and when -- host is not delivered <http://127.0.0.1:5173/> 、<http://localhost:5173/> are effective
>
> Then start the second service and pass -- host, http://localhost:5173/ Covered by the second service, http://127.0.0.1:5173/ Still occupied by the first service

#### 问题原因：

当使用httpServer.listen（）方法,在Node.js中启动HTTP服务器并传递localhost作为主机参数，然后再次传递0.0.0.0作为主机参数并对两者使用相同的端口时，可能不会总是收到一个错误，即端口已在使用中。通常，这会引发错误，因为第一台服务器已经占用了端口。
然而，情况并非总是如此。这是因为，在某些操作系统上，两个进程可以在同一端口上侦听不同的IP地址，而不会相互干扰。如果您的操作系统支持此行为，并且第一台服务器使用的是localhost地址，那么您可以使用0.0.0.0地址启动第二台服务器，而不会收到有关正在使用的端口的错误。
然而，值得注意的是，这种行为不是标准的或跨平台的，因此在编写代码时不建议依赖它。如果需要在同一台计算机上启动多个服务器，最好使用不同的端口号以避免端口冲突。如果需要在不同的计算机之间共享服务器，则应使用公共IP地址来启动服务器，而不是使用localhost。(下面是我用翻译软件翻译的一遍，vite都是英文交流🤔)

>When you use the httpServer.listen() method to start an HTTP server in Node.js and pass localhost as the host parameter, and then pass 0.0.0.0 as the host parameter again, and use the same port for both, you may not always get an error saying that the port is already in use. Normally, this should throw an error because the port is already occupied by the first server.  
However, this is not always the case. This is because, on some operating systems, two processes can listen on the same port for different IP addresses without interfering with each other. If your operating system supports this behavior, and the first server is using the localhost address, then you may be able to start the second server using the 0.0.0.0 address without getting an error about the port being in use.  
It's worth noting, however, that this behavior is not standard or cross-platform, so it's not recommended to rely on it when writing code. If you need to start multiple servers on the same computer, it's best to use different port numbers to avoid port conflicts. If you need to share a server between different computers, you should use a public IP address to start the server instead of using localhost.

#### 解决方案

我尝试获取一个合法的端口号时，先尝试创建一个tcp连接，连接该端口号以检查是否是否使用，常规条件下，看起来十分成功。这个方案我想了很久。

#### 最终

可是最后还是倒在了并发问题上，这似乎是一个不可避免的问题。

> This fail might cause an issue. This PR's approach doesn't work well in a concurrent situation.  
<https://github.com/vitejs/vite/actions/runs/4292613465/jobs/7479273031#step:13:105>
> I guess this fail happened by the following steps:
>1.  playground A: net.connect checks port 5173 => success
>1.  playground B: net.connect checks port 5173 => success
>1.  playground A: listen port 5173
>1.  playground B: listen port 5173 => error


需要一个更好的解决方案

> In this way, concurrency seems to be inevitable, and better methods need to be found 🤔

  


### 2、after setting server.open, the default open is inconsistent with the shortcut key

这是vite设置了server.open 后 默认打开的浏览器地址与快捷键不一致

> After setting server.open, the default open is inconsistent with the shortcut key，Now only the shortcut key processing has been modified

具体issues [#11971](<https://github.com/vitejs/vite/issues/11971>)

#### 最终解决

详细PR [#11974](<https://github.com/vitejs/vite/pull/11974>)

统一的快捷键和默认打开浏览器 使用resolvedUrls.local viteServer 新增api openBrowser

### 3、Ctrl+C no longer kills processes running in parallel with vite

这是vite 设置快捷键后通过npm run all 等脚本启动的时候需要两次ctrl+c才可以终止程序

#### 问题原因
通过设置process.stdin.setRawMode(true)后，无法得知，vite处于第三方进程的子进程中，无法通知父亲销毁，导致ctrl+c 仅退出vite程序 从而截断了终止信号的传播。
应该所有使用process.stdin.setRawMode(true) 都会有此问题

#### 尝试解决

相关PR [#11518](<https://github.com/vitejs/vite/pull/11518>)


#### 最终方案：

```js
await server.close().finally(() => process.exit(1));
```
回到了初始方案

后续问题：会有丑陋的错误提示ctrl+c

因为这个issues 我做了一个vite插件，为vite提供自定义快捷键功能，vite不支持的时候则添加

[vite-plugin-shortcuts](https://github.com/kinfuy/vite-plugin-shortcuts)


### 2、Need a new shortcut key to clear the output.

需要一个快捷键清除当前输出

相关issue [#11493](<https://github.com/vitejs/vite/issues/11493>)


最终解决 [11494](<https://github.com/vitejs/vite/pull/11494>)

### 5、Always provide access address Population

#### 提issue

> In one case, when IED is developed for a period of time, terminal information is flooded by hot update information. After leaving for a period of time, close the browser and forget the access address. You must restart to obtain the access address

[Always provide access address Population · Issue #10527 · vitejs/vite](https://github.com/vitejs/vite/issues/10527)

#### 尝试解决
我第一次给vite提PR，尝试在log打印后重复输出地址，现在想想方案确实太不成熟了，合理了。
[# 10594](<https://github.com/vitejs/vite/pull/10594>)

#### 最终解决方案
添加了快捷键模式
[#11319e](https://github.com/vitejs/vite/pull/11319)

  
### 我收获到了什么？

通过参与vite贡献，确实会让我们对vite的理解更深了一些，需要去调试，打断点追踪bug发生的原因。每一次解决问题代码被合并到主分支，都会有一种成就感。

- 重要的不是怎么解决问题，而是解决问题的过程，每一次断点调试代码的过程，和每一次解决方案的思路。

- 流程化，规范化真的可以避免很多意料之外的错误。

- 英文确实很重要🤔


### 最后

vite是一个基于ESM的前端构建工具。参与vite开源贡献可以让你更深入地了解这些技术，并且为前端开发做出贡献。希望vite越来越好吧。毕竟我也算出了一份力，哈哈哈😂


