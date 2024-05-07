---
updated: 2024-05-07T14:24
---
文：志俊

# 本文原创，转载请注明作者及出处

在使用 `ReactNative`开发中，我们熟练的采用 `JavaScript`的方式发送请求的方式发送一个请求到服务端，但是处理这个请求的过程其实和处理 `Web`应用中发送的请求的过程是不一样的。因为处理这个请求的目标不是浏览器，而是嵌入这个应用的原生操作系统。
![图片](http://mmbiz.qpic.cn/mmbiz_jpg/pibXop4Ees9pe4b8eiaOoE3P5fyu2PiccGZHCJyEt1IVCbFN5g77afrCblpvDIYMQQ5MSs4ax5rYZFtpRoetYb5jg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在处理 `ReactNative`的请求时，分为两部分：一部分是 `JavaScript`的运行环境，另一部分是嵌入 `JavaScript`的 `Native`（即原生 `Android`和 i`OS`）运行环境。 `ReactNative`内置了三种发送网络请求的方式： `fetch`, `XMLHttpRequest` 和 `WebSocket`。但是 `ReactNative`的运行环境和 Web 应用的运行环境不一样，所以需要在原生应用层采用自定义函数来拓展运行时（runtime）环境来处理 `JavaScript`发出的网络请求。

## 请求发送方式及过程

![图片](http://mmbiz.qpic.cn/mmbiz_jpg/pibXop4Ees9pe4b8eiaOoE3P5fyu2PiccGZZzicssjAsXJfU4icfCTslyc3LHM4WtWzVGBpH3IKhqVbJWAsKeViaVVdw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)对于常用的网络请求对象：XMLHttpRequest（XHR）、Fetch 及 WebSocket，熟悉前端开发的同学应该非常了解。 `XHR`是Web开发中用得比较多的发送请求的方式， `Fetch`和 `Websocket`也是后起之秀，在很多现代 Web 应用中得以采用。但是，在 `ReactNative`中，这些对象的使用和 Web 应用是有差别的。当你在 JS 层调用网络请求时，其实是经历了两个过程才到达真正的服务器端。就像头部 banner 表示的那样。

#### XMLHttpRequest（XHR）

在 `ReactNative`中, `XMLHttpRequest（XHR）`由两部分组成： “前端”（front-end）和“后端”（back-end）。前端负责与 `JavaScript`交互，后端负责在原生平台上转换 `JavaScript`发送过来的请求为原生系统自己的请求。

这里的后端其实是一个原生平台顶层抽象的统一API层，使得 `JavaScript`层可以调用原先系统的网络模块。例如 iOS下内置的 URLSession 模块和 `Android`下的 OKHTTP模块。 ^62e282

#### Fetch

在现代 Web 浏览器中， `Fetch`API 提供了和 `XHR`大部分相同的功能，但是 `Fetch`提供了一种更加简单，高效的方式来跨网络异步获取资源，同时可操纵 `Request`和 `Response`对象来复用请求。

但是在 `ReactNative`中，为了兼容两种平台的差异，采用了依赖于 `XMLHttpRequest`的 Fetch Polyfill 来实现这个请求对象。这就意味着我们不能像实用 Web 平台下的 `Fetch`对象一样来实用 `ReactNative`下的该对象。比如采用这个对象来发送 binary 数据。当然可以采用第三方的库比如 react-native-fetch-blob 来实现相应的功能。

#### Websocket

`Websocket`作为一种新的通信协议，采用全双工通讯方式与服务器间进行通信的网络技术。

在 `ReactNative`中， `Websocket`并不是一个独立的请求，和 `XMLHttpRequest（XHR）`一样由两部分组成： “前端”（front-end）和“后端”（back-end）。前端负责与 `JavaScript`交互，后端负责在原生平台上转换 `JavaScript`发送过来的请求为原生系统自己的请求。在 iOS 中采用的是自己开发的 NSStream，而在 Android 系统中则是OKHTTP 模块。

## 查看React Native中的网络请求

在 `ReactNative`开发中，你可以通过 `ChromeDeveloperTools(CDT)`的 `Sources`面板中调试 `javascript`部分的代码，包括断点、输出信息、断点调试等一切 `javascript`调试所需的信息。但是，唯一缺少的就是网络请求的跟踪调试。我们没办法像 Web 开发那样，可以通过 `CDT`中的网络面板（ `Network`）来查看应用的网络请求的相关信息。

#### 使用代理调试网络请求

虽然没有办法通过 `CDT`查看应用的网络请求，但是我们可以通过 `Fiddler`、 `CharlesProxy`及 `Wireshark`等软件设置代理，来查看追踪调试网络请求。这里使用 `Fiddler`来作为代理。

1.首先设置 `Fiddler`的代理端口： 打开 Filddler -> Tool -> Options -> Connects，在监听端口处填写相应的端口号，

![图片](https://mmbiz.qpic.cn/mmbiz_png/pibXop4Ees9pe4b8eiaOoE3P5fyu2PiccGZpCc8TnqR4xnShT6Ibfl5p2e54guRJOJSnyicFAXx82Nd2XKibLyqwFPA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2.在调试机器上、 `Android`或者 i`OS`模拟器模拟器中设置代理： 找到调试的机器上的网络设置中，设置当前连接的 WIFI 的代理地址

![图片](https://mmbiz.qpic.cn/mmbiz_png/pibXop4Ees9pe4b8eiaOoE3P5fyu2PiccGZVyeiby7CiagzM73mqib1iczuibeLDXMu5OQ0fsic9ZOkQWZ9oOQk2P2seQag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.刷新应用，在 `fiddler`中查看网络请求

![图片](https://mmbiz.qpic.cn/mmbiz_gif/pibXop4Ees9qQlkfqwoxgbLCQDghpa68o2NdLuqcpBftZhucnempUOzbXWB2Y19iaCmrqRAmbsZs0Q06nrttqWcQ/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

在代理应用中，我们可以查看请求头，返回头，返回结果等相关的网络信息。当然，还可以根据相关代理软件拦截请求，重新设置后发送。

#### 使用 Reactotron 调试网络

上面通过设置代理的方式来查看和追踪网络请求，虽然功能强大，但是实际操作起来有些难度，上手成本比较高。通过使用 `Reactotron`，可以将调试的配置信息集成到应用中，方便在不同的开发环境下有相同的调试配置，节约开发配置成本。

`Reactotron`由两部分组成，一部分是调试应用，一部分是调试配置。

1.调试应用分别有各个操作系统的 GUI 安装版本。当然，如果习惯使用命令行，也可以使用 `NPM`安装 `reactotron-cli` `npm install-g reactotron-cli`

![图片](https://mmbiz.qpic.cn/mmbiz_png/pibXop4Ees9rFZuvE6UXFQvEXDgeWcqlVndrvLxAn8gxQjdpLKsSwqWl6HDrqMQv5J3eKmcHicXQsCtwFgShicmEg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2.设置调试配置：

1. `` 在你的`React Native`应用中安装`reactotron-react-native` ``
    

3.   ` ``` `
    
4.   `npm i --save-dev reactotron-react-native`
    
5.   ` ``` `
    
6.   `然后，在你的应用的添加配置文件，定制调试内容：`
    

8.   ` ``` `
    
9.   `import Reactotron, {`
    
10.     `trackGlobalErrors,`
    
11.     `openInEditor,`
    
12.     `overlay,`
    
13.     `asyncStorage,`
    
14.     `networking`
    
15.   `} from 'reactotron-react-native'`
    
16.   `Reactotron`
    
17.     `.configure({`
    
18.       `name: 'xxx'                 // 调试的名称`
    
19.     `})`
    
20.     `.use(trackGlobalErrors())     // 设置监听全局错误`
    
21.     `.use(openInEditor())          // 设置在编辑器中打开错误`
    
22.     `.use(overlay())               // 设置图片遮盖图片（用于UI还原度对比）`
    
23.     `.use(asyncStorage())          // 设置异步存储调试`
    
24.     `.use(networking())            // 设置网络调试`
    
25.     `.connect()                    // 连接应用（必须）`
    

27.   ` ``` `
    
28.   ``然后在你的应用的入口文件中引入这个配置文件。然后重新启动应用。当然，还可以使用正则表达式过滤请求的`contentType`的类型和要忽略的请求的`url`，见下面的配置：``
    

30.   ` ``` `
    
31.    `.use(networking({`
    
32.       `ignoreContentTypes: /^(image)\/.*$/i,   // 设置reactotron要忽略的文件类型`
    
33.       `ignoreUrls: /\/(logs|symbolicate)$/,    // 设置reactotron要忽略的url请求路径`
    
34.    `}))`
    
35.   ` ``` `
    
    `   `
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/pibXop4Ees9rFZuvE6UXFQvEXDgeWcqlVwrmc4JtGqTk566OCzTmsJuic1CING8jb0BvG6mvjztO8t0CId9oJZCg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/pibXop4Ees9rFZuvE6UXFQvEXDgeWcqlVf0IaDI7JugUJYFZ9lVBVHALI9ITcvM4kibRibvs7sic1HMPgbQdQbkK9A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_gif/pibXop4Ees9qQlkfqwoxgbLCQDghpa68osCI7ZX5c8BicYjkiajicJIPdIWa4tefc582djQZ23f9Nvce2DX5sAnhvA/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)  

`reactotron`调试网络只是他的一个功能之一，其他还有很多强大的功能。有兴趣可以查看他的文档。

#### 使用 Chrome Developer Tools 网络面板调试网络

`ReactNative`默认暴露出来的接口中，是没有直接在 `ChromeDeveloperTools`查看网络请求的方法的，查看 RN 源码 Libraries/Core/InitializeCore.js，注释中写着：

> Sets an object’s property. If a property with the same name exists, this will replace it but maintain its descriptor configuration. By default, the property will replaced with a lazy getter. * The original property value will be preserved as original[PropertyName] so that, if necessary, it can be restored. For example, if you want to route network requests through DevTools (to trace them): * global.XMLHttpRequest = global.originalXMLHttpRequest; * @see https://github.com/facebook/react-native/issues/934

具体实现在 XHRInterceptor.js 中。原来的 `XMLHttpRequest`被改写成了 `originalXMLHttpRequest`，所以要在 `Chrome` 中显示 `network` 只需要替换 `XMLHttpRequest` 为 `originalXMLHttpRequest`。在入口文件处设置：

1. `if (__DEV__) {`
    
2.   `GLOBAL.XMLHttpRequest = GLOBAL.originalXMLHttpRequest || GLOBAL.XMLHttpRequest`
    
3. `}`
    

当然，这样有可能会产生 `CORS`, `Chrome` 会限制跨域请求。这时要么后端配合一下去除限制，要么使用 Allow-Control-Allow-Origin: * 插件。

## React Native发送二进制数据（binary data ）

由于 `ReactNative`中 `Fetch`对象的底层采用的是 `XHR`实现，这就限制了发送二进制数据的功能。当然 `ReactNative`提供了一系列的方式来解决这个问题，比如： 转换二进制文件为 Base64 字符串或者采用第三方库 react-native-fetch-blob。但是并没有从底层解决这个问题。

#### 转换二进制为 Base64 发送

到目前为止， `ReactNative`不能发送非序列化的数据，所以，要发送二进制数据，采用 Base64 编码的字符串是个不错的选择。 

![图片](http://mmbiz.qpic.cn/mmbiz_jpg/pibXop4Ees9pe4b8eiaOoE3P5fyu2PiccGZSauz4YPLfAXnOSDzBGDqKPibO795LOM7WLGPXDviajEGz1z5pBRh5BZw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

例如，你从服务器下载一张图片（注意：不是通过 `url`从服务器获取），请求通过JavaScript 线程，再通过 `ReactNative`提供的桥接器，最后通过原生系统的网络模块发送到服务端。服务端返回一个 Base64 编码过的图片， `JavaScript`线程收到返回的字符串后，会分配相应的内存，然后 `ReactNative`会调用相应的原生模块渲染成相应图片。但是值得主要的是，这种方式会造成典型的性能问题——内存泄漏。

通过 Base64 编码的方式传输二进制文件，这里会造成一系列性能问题，这篇文章中列出了大部分性能问题及提出了相应的解决方案。

现在使用的各种方法发送二进制文件都存在各种问题，最终的解决方式是要相应的标准能够实现二进制的传输。目前， `WebSocket`已经支持了二进制传输。在最新版本的 `ReactNative`层也已经支持 `WebSocket`协议来传输二进制文件，但是，相应的原生平台的网络模块暂时还不支持。

## 总结

`ReactNative`开发方式是非常不错的体验，但是，受各个平台差异和标准的限制，不得不折中处理一些问题，随之而来的是相应的性能、效率的问题。另外，采用开发，性能上和用户体验上和原生应用还是有一定差距。但是如果在原生应用中能够集成 `ReactNative`，会显著提高开发效率。

## 参考

Network layer in React Native

-     https://medium.com/dailyjs/network-layer-in-react-native-eec841f11861
    

reactotron介绍

-     https://infinite.red/reactotron
    

reactotron

- https://github.com/infinitered/reactotron
    

Reactotron on React Native

- https://github.com/infinitered/reactotron/blob/master/docs/quick-start-react-native.md

原文：
[RN网络](https://mp.weixin.qq.com/s?__biz=MzI1MTE2NTE1Ng==&mid=2649517515&idx=1&sn=502e686ec11c352a7aaaa44178eb6060&chksm=f1efed40c69864562e7a565949287957345b36e7afdc1c3f99b759e7a35ec52347563519b029&scene=21#wechat_redirect)