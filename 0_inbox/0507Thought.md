---
updated: 2024-05-07T14:26
---
## [[React Native 网络层分析]]
文章介绍了RN的三种网络，`fetch`, `XMLHttpRequest` 和 `WebSocket`；其中`fetch`和 `WebSocket`均使用Polyfill转换，实则调用原生模块。