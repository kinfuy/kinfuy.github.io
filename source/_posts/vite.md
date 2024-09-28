---
title: Vite4.3 来袭，性能提升 10x！冷启动时间比turbopack还快！
cover: https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10f42280470c4e8f9d51fc4ef406e910~tplv-k3u1fbpfcp-zoom-crop-mark:1512:1512:1512:851.awebp?
date: 2023.4.13
categories:
  - 开源大事件
tags:
  - vite
---

## 序
最近vite发布4.3-Beta版本，vite团队在这个版本重点是工作是性能优化，并且取得了惊人的成果🥳

## Rust vs JavaScript
？？？

JavaScript也可以写出性能足够好的程序

Rust时候提升新一波了性能了（敲打）🤔🔨

当然vite的性能提升，也离不开esbuild与SWC等的支持。（不是引战😄）
## 性能提升
![截屏2023-04-11 21.26.49.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/602f633d62314cbb98f39e63465facac~tplv-k3u1fbpfcp-watermark.image?)
简单翻译一下：
我在我的Windows电脑上用Vite 4.3 beta测试了一些基准测试。
Vite 4.3的冷启动时间快3倍，热启动时间也快1.3倍🏎。更重要的是，当使用SWC插件时，Vite 4.3的冷启动时间现在比涡轮背包快1.5s。


## 数据对比
下面是核心成员：[翠 / green](https://github.com/sapphi-red)，给出的测试数据：


### Start Time （启动时间）
相比于vite4.2.1，vite4.3-beta使用plugin-react或plugin-react-swc插件的冷启动和热启动时间都有显著缩短，分别提升了3.5倍和1.3倍，4.4倍和1.6倍12。
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bc5bf718e404f5f8cfc1d95b077715a~tplv-k3u1fbpfcp-watermark.image?)


### HMR（热更新时间）
相比于vite4.2.1，vite4.3-beta使用plugin-react或plugin-react-swc插件的根HMR和叶子HMR时间都有明显缩短，分别提升了1.7倍和2.3倍，1.3倍和1.7倍12。
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3d0bb3391b547a39c5ec42fda03dad0~tplv-k3u1fbpfcp-watermark.image?)

### FCP（首次有内容渲染的时间）
相比于vite4.2.1，vite4.3-beta使用plugin-react插件的FCP平均值从38031降低到3083，快了12倍
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0c5473d237a4fce8f06355db0b6f88f~tplv-k3u1fbpfcp-watermark.image?)

### 其他工具对比
与其他构建工具的对比：相比于webpack、farm、rspack和turbopack，vite4.3-beta在启动时间和热更新时间上都有优势，特别是在使用plugin-react-swc插件时，其冷启动时间比turbopack快了1.5秒，热启动时间比turbopack快了1.8秒
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f47c2c2d4e94f1e98d275100d21639e~tplv-k3u1fbpfcp-watermark.image?)

> 此处respack数据存在误差。
> 
>![截屏2023-04-11 21.42.30.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8e5efa4fe3f4d6f8d1ce2155541e51e~tplv-k3u1fbpfcp-watermark.image?)
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5da07c5a78554aed93c243054c71b371~tplv-k3u1fbpfcp-watermark.image?)

### 更多对比细节
- [performance-compare](https://github.com/sapphi-red/performance-compare)
- [Vite 4.3 performance](https://gist.github.com/sapphi-red/db27f9c18ed31894e409224051119e1b)

## 为什么vite可以这么快
看了最近vite的[CHANGELOG.md](https://github.com/vitejs/vite/blob/v4.3.0-beta.4/packages/vite/CHANGELOG.md)，发现从vite4.2.1到v4.3.0-beta.4，性能提升的相关commit 就有多达25个。重构相关的17个。我一一看了一遍，只能说 vite团队太强了，很多都是尽可能的压榨性能，不放过每一个可能提升性能的点，当然前提也保证API的健壮性。

1、对一些语法的替换

我简单的列出几个例子。
- 禁用了`new URL()` 
> perf: avoid new URL() in hot path (#12654) ([f4e2fdf](https://github.com/vitejs/vite/commit/f4e2fdf)), closes [#12654](https://github.com/vitejs/vite/issues/12654)

- 打开浏览器的时候，禁用`execSync` 使用`exec`。
> perf: avoid execSync on openBrowser (#12510) ([a2af2f0](https://github.com/vitejs/vite/commit/a2af2f0)), closes [#12510](https://github.com/vitejs/vite/issues/12510)

- 将startsWith、endsWith替换为`===`
> perf: replace endsWith with === (#12539) ([7eb52ec](https://github.com/vitejs/vite/commit/7eb52ec)), closes [#12539](https://github.com/vitejs/vite/issues/12539)
> 
>perf: replace startsWith with === (#12531) ([9cce026](https://github.com/vitejs/vite/commit/9cce026)), closes [#12531](https://github.com/vitejs/vite/issues/12531)


2、优化一些同步的操作。

3、去除了一些不必要的验证，减少 vite 客户端路径检查

4、对嵌套优化的 deps 使用更简单的解析

5、重构深度导入的 package.json、提高 package.json 解析速度 

### 值得推荐的一个vite插件:

[vite-plugin-warmup](https://github.com/bluwy/vite-plugin-warmup)

vite团队成员开发。是一个vite文件预热插件，可以尽早的开始处理一些我们必须加载的vite文件。

Vite 的核心是一个按需文件服务器。当收到请求时，它将转换文件并提供服务。这意味着我们只做请求的工作，保持开发服务器的快速运行。

但是，有时我们会提前知道在开始开发周期时会请求哪些文件。我们可以提前开始转换文件，而不是在打开页面之前让 Vite 处于空闲状态，这样当它被请求时，它就会被缓存并可以立即提供。

现在该插件依赖vite4.3的一些新特性：

> 1. warmup可以在server init上启动而不是server listen 
> 
> 2. 直接对 HTML 文件进行预热
> 
> 3. 同时推动用户升级到4.3

用起来也相当简单。
```js
// vite.config.js
import { defineConfig } from 'vite'
import { warmup } from 'vite-plugin-warmup'

export default defineConfig({
  plugins: [
    warmup({
      // warm up the files and its imported JS modules recursively
      clientFiles: ['./**/*.html', './src/components/*.jsx']
    })
  ]
})
```

## 结语
前段时间，Vercel 推出了下一代打包工具：Turbopack，并宣称其比 Vite 快 10 倍。（此处未被证实）

但是如今，Vite 在某些方面已经超越了 Turbopack。

还有根据官方的一些信息，当稳定后 vite-plugin-warmup 插件功能会进入vite核心中。大家感兴趣可以提前升级到vite4.3-beta安装此插件，体验一下。

## Follow 认证

This message is used to verify that this feed (feedId:62851852422645760) belongs to me (userId:56870707744741376). Join me in enjoying the next generation information browser https://follow.is.

