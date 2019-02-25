# React 代码转换

当项目中使用的 React 版本升级之后，往往有些 api 发生变动，需要将项目中的代码进行批量修改。
[jscodeshift](https://github.com/facebook/jscodeshift) 可以协助我们进行代码迁移，除次之外还需要安装
[react-codemod](https://github.com/reactjs/react-codemod)。

使用方法：

```js
jscodeshift -t react-codemod/transforms/React-PropTypes-to-prop-types.js code.js
jscodeshift -t react-codemod/transforms/class.js code.js
```
