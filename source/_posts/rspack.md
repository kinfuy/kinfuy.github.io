---
title: webpack沦为对比单位？前端整活圈又出新品了
date: 2023.3.10 12:00:00
cover: /static/articles/rspack.webp
categories:
  -  开源
tags: 
  - rspack webpack
---

### 序

能被JavaScript重写的都将被JavaScript重写

能被rust重写的都将被rust重写？

#### 省流

新项目建议直接vite，rspack可以当作历史巨石项目的的一个过渡品，长期观望，farm目前阶段还不推荐使用。

### rspack（Rust）

#### 为什么要做 Rspack

我们创建 Rspack 的原因是要解决在 ByteDance 维护构建工具时遇到的各种性能问题。由于 ByteDance 内部存在许多巨石应用，它们都具有复杂的构建配置，生产环境构建需要耗费十几分钟，甚至超过半小时；开发环境的耗时也超过十几分钟。

我们在 Webpack 上尝试了多种方法来优化这些巨石应用，但是效果甚微。我们意识到在 Webpack 上的优化已经难以为继，必须要从底层改造，才能适应我们的需求。

#### 对比数据

目前仅与webpack比较

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68a89533adf14aab9b5a5923a3b9d3d5~tplv-k3u1fbpfcp-zoom-1.image)

#### 相关链接

【官网】[Rspack](https://www.rspack.dev/)

【Github】<https://github.com/web-infra-dev/rspack>

  


### Farm （Rust）

阿里系，很低调基本没宣传,项目还在很早期阶段

文档仅英文，很多内容没有，处于不可用状态，玩玩还行

#### Why Farm?

As the web project scales, building performance has been the major bottleneck, for a huge project, compiling with webpack may cost 10min or more, a hmr update may cost 10s or more, heavily reduced the efficiency.

Then some tools like vite comes out, it uses native ESM and is unbundled for source files in dev mode, pre-bundle dependencies using esbuild, which makes the dev server launch and the HMR very fast.

But Unbundled is not perfect, there are still big problem when comes for a large project:

-   **The huge numbers of module requests**: For a large project, there may be thousands of modules that should be loaded, using native module system to load thousands of modules will make the browser get stuck or even crashed.
-   **Inconsistency between Dev and Production**: Native module can not be used in production for most situations, For the compatibility and request numbers. So Unbundled tools choose to bundle in production. This brings inconsistency, when there are production bugs caused by this inconsistency, it's really hard to debug and really painful. And vite is using esbuild in dev and using rollup in production, which enlarged the inconsistency.
-   And Vite is so fast in dev because of esbuild, which is written in go. Go takes advantages of native platform and much faster than Js.

So I thing we just need a fast, powerful, consistent web bundler, which can solve the problems above and fast, then I designed Farm.

And Farm is not just a normal bundler re-written in Rust, it has a lot of powerful and progressive designs:

#### 对比数据

性能提升很有限

| Webpack             | Vite  | Farm  | Compare |                                              |
| ------------------- | ----- | ----- | ------- | -------------------------------------------- |
| **cold start**      | 853ms | 276ms | 67ms    | Farm is faster: **12x webpack**，**4x vite**  |
| **HMR**             | 43ms  | 23ms  | 2ms     | Farm is faster: **20x webpack**，**10x vite** |
| **onload**          | 83ms  | 310ms | 57ms    | Farm is faster: **5x vite**                  |
| **accessible time** | 936ms | 586ms | 124ms   | Farm is faster: **8x webpack**，**5x vite**   |

  


#### 相关链接

【官网】<https://farm-fe.github.io/>

【GitHub】<https://github.com/farm-fe/farm?version=2.5.50001.11&platform=win>

### Turbopack

Turbopack 建立在新的增量架构上，以提供最快的开发体验。在大型应用上，它的更新速度比 Vite 快 10 倍，比Webpack 快 700 倍。在更大的应用上，通常会比 Vite 快 20 倍。

由于 Turbopack 只打包开发所需的最少资源，因此启动时间非常快。在具有 3000 个模块的应用上，Turbopack 需要 1.8 秒即可启动，而 Vite 则需要 11.4 秒：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25d2b8cd39d5436eac6646f1efa43e41~tplv-k3u1fbpfcp-zoom-1.image)

### 最后

新出的两个打包工具，目前阶段rspack已经基本可用，farm可能还有一段距离，对比上次据说比webpack快700的低调了很多。数据上没有怎末华丽，重点可能就是rust重写。

rspack文档比较全，对标webpack，有可取之处。尤大原话：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a637d71991704400ba59fe02b40591eb~tplv-k3u1fbpfcp-zoom-1.image)

  


不能看出两个新出的工具都是解决各置公司的历史巨石项目的问题出现的产物。farm 指出vite 开发生产环境不一致，生产问题调试困难，rspack 则是历史项目包袱过重迁移困难。

所以新项目还是vite。🤔

### 参考

- [字节跳动自研 Web 构建工具 Rspack 正式发布](https://mp.weixin.qq.com/s/R-tjPrj2N2DKMO8_cPsp9Q)

- [比Webpack快700倍的Turbopack，到底快在哪？](https://juejin.cn/post/7158997985870200839)


