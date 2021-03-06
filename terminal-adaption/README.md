# 终端适配

目录：
*   [引言](#引言)
*   [PC 端](#PC-端)
*   [移动端](#移动端)

### 引言
通常我们需要采用一些技术手段来使页面内容能够自动适应各类常见屏幕，并最大程度还原设计稿效果。然而前端系统的运行和显示环境较为复杂，这一过程往往会面临很多问题，例如：
*   移动端屏幕碎片化严重，简单的流式布局和媒体查询等技术难以覆盖所有主流机型，且适配效果较差；
*   各平台对 H5 和 CSS3 新特性的支持水平参差不齐，导致部分技术无法兼容老旧设备或浏览器环境；
*   对于一些较新的硬件设备，必须对页面进行特殊优化才能使其达到最优显示状态；
*   可选的适配技术较多，但每种技术都有各自的适用范围和潜在风险；

我们在实际开发中通过引入统一的终端适配方案来解决上述可能出现的问题，该方案需要实现以下几个目标：
*   对各平台及设备提供一致的适配效果，避免出现拉伸、变形或布局错乱等现象；
*   满足基本的浏览器分级支持要求；
*   针对高像素密度屏幕尽可能提供高清优化处理；
*   保持简洁的开发流程，避免引入过多额外操作；

由于移动端和 PC 端在浏览器渲染机制及兼容性要求上存在一定差异，我们将为这两种环境分别提供不同的解决方案。

### PC 端
通常情况下，PC 端页面不需要进行特殊的终端适配处理。但为了保证良好的兼容性和代码风格，开发过程必须符合以下规范：
*   禁止使用 `%`（`100%` 除外）和 `em` 进行页面布局；
*   在满足兼容性要求的前提下，尽可能使用 [`flex`](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html) 进行页面布局；
*   以 `px` 为单位设置页面字体大小，其值必须为偶数且不得小于 `12px`；
*   在页面 `<head>` 部分添加如下标签显式设置 `viewport`：
    ```
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
    ```
*   页面容器的最大宽度固定为 `1200px`；

### 移动端
移动端方案通过 [rem](http://www.alloyteam.com/2016/03/mobile-web-adaptation-tool-rem/) 和 [lib-flexible](https://github.com/amfe/lib-flexible) 实现需要精确自适应的复杂布局。[lib-flexible](https://github.com/amfe/lib-flexible) 将根据页面运行环境自动设置 `dpr`、`viewport` 和 `rem` 基准字体大小，并利用 [postcss-px2rem](https://github.com/songsiqi/px2rem-postcss) 对 CSS 代码中以 `px` 为单位的样式进行自动转换，包括 `rem` 单位换算和基于 `dpr` 的数值缩放。具体方案如下：
*   页面设计稿的宽度统一设置为 `750px`；
*   使用 [flex](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html) 进页面基础布局；
*   以 `px` 为单位设置页面字体大小，其值必须为偶数且不得小于 `12px`；
*   在页面 `<head>` 部分内联插入 [lib-flexible](https://github.com/amfe/lib-flexible) 框架代码；
*   以 `px` 为单位设置需要精确自适应的元素的盒模型及位置，系统将在构建阶段自动对其进行 `rem` 单位换算，例如：
    ```
    width: 75px;
    ```

    将在系统构建后自动换算为：
    ```
    width: 1rem;        // for iPhone 6
    ```
*   对于需要保持以 `px` 为单位（例如字体大小和与之相关的布局）的样式，统一在后面添加 `/* px */` 注释，系统将在构建阶段自动对其进行基于 `dpr` 的数值缩放，其中原始样式中的数值将默认按 `dpr = 2` 计算，例如：
    ```
    .container {
        font-size: 28px; /* px */
    }
    ```

    将在系统构建后自动换算为：
    ```
    [data-dpr="1"] .container {
        font-size: 14px;
    }

    [data-dpr="2"] .container {
        font-size: 28px;
    }

    [data-dpr="3"] .container {
        font-size: 42px;
    }
    ```

*   对于不需要进行任何数值转换处理的样式，统一在后面添加 `/* no */` 注释，系统将在构建阶段对其保持忽略，例如：
    ```
    width: 50px; /* no */
    ```

    将在系统构建后保持为：
    ```
    width: 50px;
    ```
