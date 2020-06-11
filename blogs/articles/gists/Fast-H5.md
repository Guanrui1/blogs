# H5 页面秒开思路

## 整体思路

整体思路是：

* 减少请求、渲染操作的中间环节。串行操作改并行。
* 预加载、预执行、预渲染。

**那么代价是什么？**

任何转换都有代价，加速本质上就是在用更多的网络、内存和 CPU 换取速度，以空间换时间。

## 传统方案

### 直出+离线包缓存

直出是指后台渲染带首屏数据的 HTML 文档，客户端加载 HTML 后，再请求 JS/CSS 更新数据等。

离线包是将 JS/HTML/CSS 以及数据都缓存到本地，客户端创建 Webview 加载资源时，异步请求最新数据，回填至页面上。

👍 对于 Web 端而言，逻辑相对透明，侵入性小
👎 Webview 初始化耗时；数据量庞大的 HTML 解析也很耗时；直出方案中获取更新数据是串行的

### 客户端代理

如腾讯的 VasSonic，在离线包策略的基础上，做了以下优化：

* 预请求 HTML 资源
* 流式拦截数据请求，边加载边渲染
* 动态缓存与增量更新

预请求即提前请求 HTML 并缓存到本地，等 Webview 线程发起相应资源的请求时，直接返回本地的缓存。

动态缓存和增量更新是建立在强代码侵入性的基础上的。通过自定义 HTML 注释区分动态数据和模板数据，拓展 HTTP 头部与后台约定具体页面。请求时，通过 ID 判断具体页面是否需要更新数据，后台将差异部分通过 JS 回调在客户端局部更新。

👍 高性能
👎 强侵入性，高额的沟通及维护成本

## 无缝方案

传统方案不可省略的时间是 HTML 加载及解析时间以及 Webview 启动时间。

Webview 启动时间可以通过一个 Webview 进程常驻内存来解决。

在相同模板的页面下，切换页面其实等同于仅替换页面的数据，这样可以达到省去加载和解析 HTML 的时间成本。

👍 极致性能
👎 极大的维护成本；客户端高内存占用；

极大增加维护难度。

## SSR -> CSR

SSR -> Static SSR -> NSR -> CSR

* SSR 服务端渲染，性能最好。对前端开发的运维和服务器水平有高要求。
* Static SSR，静态页面。Lionad 的博客就是预渲染的静态页面。
* NSR（Native Side Rendering）。UC浏览器在新闻列表页面中加载离线页面模板，预加载页面数据，然后预渲染生成 HTML 文件储存在内存中。有分布式 SSR 的味道。


## 引用&参考

* [H5 秒开方案大全](http://www.alloyteam.com/2019/10/h5-performance-optimize/)
* [大型 H5 页面无缝闪开方案](http://www.alloyteam.com/2020/06/fast-open-h5/)
* [CSR、SSR、NSR、ESR傻傻分不清楚，一文帮你理清前端渲染方案](https://blog.csdn.net/m0_37411791/article/details/106513195?utm_source=blogxgwz7)
* [边缘计算听说过吗？淘宝用它提升了69%的首屏性能](https://juejin.im/post/5ecf90b7f265da76e7651a96)