# Babel & Webpack & React

1. 依赖的 package 安装
  
  ```
  npm install react --save
  npm install react-dom --save
  npm install babel-core --save-dev
  npm install babel-loader --save-dev
  npm install babel-preset-react --save-dev
  npm install babel-preset-es2015 --save-dev
  npm install webpack --save-dev
  npm install webpack-dev-server --save-dev
  ```

2. 写一个简单的组件, 保存为 hello.jsx
  ```
  import React, {Component} from 'react';

  export default class Hello extends Component {
    render() {
      return (<h1>Hello World</h1>)
    }
  }
  ```
  
3. 写一个简单页面, 保存为 index.html

  ```
  <!DOCTYPE html>
  <html>
    <head></head>
    <body>
    <div id="app"></app>
    <script type="text/javascript" src="bundle.js"></script>
    </body>
  </html>
  ```

4. 将组件渲染到页面，保存为 main.js

  ```
  import React from 'react';
  import ReactDOM from 'react-dom';
  import Hello from './hello'

  main();

  function main() {
    ReactDOM.render(<Hello />, document.getElementById('app'))
  }
  ```

5. webpack 配置，保存为 webpack.config.js
	
  ```
  var path = require('path');
  var webpack = require('webpack');

  module.exports = {
    entry: './main.js',
    output: { path: __dirname, filename: 'bundle.js' },
    module: {
      loaders: [{
        test: /.jsx?$/,
        loader: 'babel-loader',
        exclude: /node_modules/,
        query: {
          presets: ['es2015', 'react']
        }
      }]
    },
    resolve: {
      extensions: [ '.js', '.jsx']
    },
  };
  ```

6. 构建 bundle.js
		
	运行命令 `node_modules/.bin/webpack` 生成 bundle.js 文件。在浏览器中打开 index.html，可以看到页面中出现 “Hello World” 字样。
	
7. 设置 `webpack-dev-server`，修改 package.json 文件
	
  ```
  {
    "scripts": {
      "build": "node_modules/.bin/webpack",
      "dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build"
    }
  }
  ```
		
8. 运行命令 `npm run dev`, 在浏览器中访问：http://localhost:8080/index.html

9. 参考文档

	[https://fakefish.github.io/react-webpack-cookbook/Introduction-to-React-JS.html](https://fakefish.github.io/react-webpack-cookbook/Introduction-to-React-JS.html)
	[https://hulufei.gitbooks.io/react-tutorial/content/webpack.html](https://hulufei.gitbooks.io/react-tutorial/content/webpack.html)
