---
title: 【前端必备】极简的 API 代理工具!
date: 2022.2.22 21:43:51
cover: https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24d98c2f40754a2eabe6c54e78d8e689~tplv-k3u1fbpfcp-zoom-1.image
categories:
  - JavaScript
tags:
  - Angular
---

## 【前端必备】极简的 API 代理工具!

极简的 API 代理工具!

背景：不想写 mock，mock 语法老忘记！并不需要多真实的数据，只需要一个模板数据，简单更改，所以写了这个插件！前段时间看见有人分享了 Ajax Interceptor 的 chrome 插件，就想着自己也可以写一个试试，就做了这个插件

### 使用

- 该插件未发布至谷歌商店，可以直接下载 version 文件夹里的最新版本，解压安装即可
- 也可自行 fork 源码 build

### demo

![juejin](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24d98c2f40754a2eabe6c54e78d8e689~tplv-k3u1fbpfcp-zoom-1.image)
![juejin](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d77d1c14541b450ba0752aa1b72543e6~tplv-k3u1fbpfcp-zoom-1.image)
![juejin](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/841b0421239740e6a982ffb8c53841c4~tplv-k3u1fbpfcp-zoom-1.image)

### 请求拦截

- 拦截 fetch 请求
- 拦截 xmrhttprequest 请求
- 请求参数可配置
- 响应参数可配置
- Json 可视化编辑
- 请求头暂时只支持追加
- 响应头不支持修改

### 后续计划

- 增加 mock 语法支持
- 优化参数，请求头，响应头配置

### 具体实现

- 重写 fetch 和 XMLHttpRequest 对象

### 源码

[gitee 地址](https://gitee.com/Y_onghu/api_proxy_pro)

[githup 地址](https://github.com/HitStarrySky/api_proxy)

### 参考

- [Ajax Interceptor](https://github.com/YGYOOO/ajax-interceptor)
- [Mock](https://github.com/nuysoft/Mock)
