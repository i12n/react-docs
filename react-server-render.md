## React server render

`ReactDOMServer` 提供了两个方法来支持服务端渲染：

- `renderToString()` 在服务端将一个 React 元素渲染为初始的 HTML 字符串
- `renderToStaticMarkup()` 与 renderToString() 功能类似, 但 HTML 字符串中不包含额外的 DOM 属性, 如 `data-reactid`

服务端渲染的优点是提高了页面加载的速度，减少了页面白屏的时间。
简单的示例代码如下

```
import React, {Component} from "react";
import {renderToString} from "react-dom/server";

module.exports =  function () {
    var content = renderToString(
        <App />
    );
    return `<!DOCTYPE html>
<html>
    <head>
    </head>
    <body>
        <div id="app">${content}</div>
    </body>
</html>`
}

class App extends Component {
    render() {
        return <h1>hello world</h1>
    }
}

```

具体demo [https://github.com/gewenmao/react-server-render-demo](https://github.com/gewenmao/react-server-render-demo)

相关资料
- [https://medium.com/front-end-hacking/server-side-rendering-with-react-and-express-382591bfc77c#.x5tnmy39e](https://medium.com/front-end-hacking/server-side-rendering-with-react-and-express-382591bfc77c#.x5tnmy39e)
- [http://www.alloyteam.com/2017/01/react-from-scratch-server-render/](http://www.alloyteam.com/2017/01/react-from-scratch-server-render/)
