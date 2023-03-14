---
title: 【初体验】Atom 的“精神继承者”，Atom 的创始人新作“Zed”
date: 2023.03.13 20:00:00
cover: /static/articles/zed.webp
categories:
  - 开源大事件
tags:
  - 前端
  - 编辑器
---
## 序

rust卷出天际，Atom 的创始人新作“Zed”，对标vscode的基于rust开发的代码编辑来了。今天获得了zed的体验资格，第一时间就下载来尝鲜一下。vscode现在插件安装多了确实有些卡顿感🤔

【10枚邀请码奉上】https://zed.dev/invites/FIEca2jU_qxJrUsS

需要的小伙伴可以自行体验，现在仅支持Mac

## 省流

目前没有使用价值，功能对于一个code 编辑器是不合格。唯一亮点多人协同编辑，没有看见插件相关功能。建议过段时间（至少半年以上）再体验。🧐


## 简介

Zed 是一个高性能的多人协作代码编辑器，由 Atom 和 Tree-sitter 的创造者开发。Zed 用 Rust 编写，自定义原生 UI 框架，设计为“协作”产品。Zed 的主打特点是速度快，启动、加载文件和响应按键都非常迅速。Zed 还支持语法树、语言服务器协议、多缓冲区、集成终端、Vim 模式和主题等功能。

Zed 目前还未开源，但有开源计划，将于本周推出 Private Alpha。

## 体验

下载安装后，打开ts文件就报告ts server 下载失败，初始印象就不好，我没有找到解决的方法，没有提供解决的指引，也不知道失败的结果，

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09e1a0f4a7f049d0b4701a717c783571~tplv-k3u1fbpfcp-watermark.image?)

小巧思：面包屑加入了导航回退功能

![截屏2023-03-13 22.33.13.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/611e07bee4fe441bbecc16f3a23e903f~tplv-k3u1fbpfcp-watermark.image?)

小巧思：独立的终端页面。

![截屏2023-03-13 22.33.20.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab74c55e5dc14693881cd0e516ef4b81~tplv-k3u1fbpfcp-watermark.image?)

确实markdown相关支持，或许是我没有找到。没看见插件商店，或者应用市场之类的东西。
![截屏2023-03-13 22.14.41.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/062eec19b752467181f37df749180a25~tplv-k3u1fbpfcp-watermark.image?)


唯一的亮点，多人协同编辑功能。没有具体体验，不知道怎么样。
![截屏2023-03-13 22.16.52.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aca4db957698470dbe6a1fd8d25f05db~tplv-k3u1fbpfcp-watermark.image?)

官方效果看起来还行
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e3297b2f0a174057a7332b0d50d7833a~tplv-k3u1fbpfcp-watermark.image?)


我想说的是到这里差不多结束了，我能体验到的功能，相比vscode功能少的不像话。

## 性能

官方测试对比性能，没有给出具体测试用例。具体不得而知。

#### 启动时间

![截屏2023-03-13 23.05.46.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2e0f2c6e30a4c5194634f86415eee71~tplv-k3u1fbpfcp-watermark.image?)

#### 内存占用
![截屏2023-03-13 23.05.57.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d77ea0c412174d87b59ccf142f7e7e27~tplv-k3u1fbpfcp-watermark.image?)

#### 插入延迟
![截屏2023-03-13 23.06.04.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f00a277b8664115a7b558b2f1e3caea~tplv-k3u1fbpfcp-watermark.image?)

## 最后

- 性能上体验不出差别，好像没看见插件功能，体量与vscode相比差距大，无法比较。
- 目前只能说是一个简版的文本编辑器。
- 相关操作按钮位置感觉不怎么友好，也可能是vscode用习惯了的原因，带有主观色彩。
- 唯一亮点就是多人编辑吧，但是感觉这个功能很鸡肋，只有特定场景下有用处，不应该是coding的重点功能，锦上添花还行。

## 相关链接

【官方特性介绍】https://zed.dev/features


***本文正在参加[「金石计划」](https://juejin.cn/post/7207698564641996856/ "https://juejin.cn/post/7207698564641996856/")***

本文首发于 [@阿乐去买菜](https://kinfuy.github.io/)，转载请署明出处
