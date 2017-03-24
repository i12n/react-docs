# Webpack Hot Middleware

[webpack-hot-middleware](https://github.com/glenjamin/webpack-hot-middleware) 可以在一个已存在的 server 上进行热加载，虽然不需要 webpack-dev-server 支持，但是需要 webpack-dev-middleware 支持。

webpack-hot-middleware 实现热加载的基本原理：
1. webpack-hot-middleware 作为一个 plugin 监听 compiler 的事件，客户端和服务端会建立一个 SSE 链接，服务端把 compiler 事件推送到 客户端
2. 客户端接收到事件之后，如果检测到本地代码更新，则触发热加载