---
updated: 2024-05-07T17:22
---
## [[React Native 网络层分析]]
文章介绍了RN的三种网络，`fetch`, `XMLHttpRequest` 和 `WebSocket`；其中`fetch`和 `WebSocket`均使用Polyfill转换，实则调用原生模块。

## Mac安装新版模拟器
[原文](https://zhuanlan.zhihu.com/p/657338757)
- 先下载：[下载地址](https://developer.apple.com/download/all/)
- 终端执行安装：xcrun simctl runtime add "~/Downloads/iOS_17_Simulator_Runtime.dmg"
