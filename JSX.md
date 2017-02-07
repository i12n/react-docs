# JSX

JSX 是对 JavaScript 语法的一种扩展，在 React 中用来描述 UI，它的作用与模板语言类似。JSX 会生成 React 元素，这些元素经过解析最终被渲染到 DOM 上。

JSX 的语法与 XML 类似, 可以自行定义标签，但是标签一定要闭合，例如在JSX中 `input` 必须要用 `/` 关闭。 
	
	<input /> 
	
JSX 与 HTML 在属性名称上有一些区别([相关文档](https://facebook.github.io/react/docs/dom-elements.html)), 例如 `class` 要用 `className`, 所有属性都是用驼峰命名法命名:

	<form className="address-form">
		<label htmlFor="address">地址:</label>
		<input name="address" type="text"/>
	</form>



任何 JavaScript 表达式都可以内嵌到 JSX 中：

	const name = 'Jim';
	const element = (<h1> Hi, {name} </h1>);
	
	ReactDOM.render(
  		element,
  		document.getElementById('root')
	);

	
需要注意的是，JavaScript 表达式要放到 `{}` 中，另外 JSX 也是一个表达式，会被解析成 JavaScript 对象, 这个对象被称作 “React 元素”，React 会将它作用于 DOM 节点上。上面代码中的 `element` 最终会被解析成的 React 元素如下所示：

	const element = {
	  type: 'h1',
	  props: {
	    children: 'Hi, Jim'
	  }
	};


为了在 React 中使用 JSX , 需要安装和配置 [Babel](http://babeljs.io/)，Babel 的作用就是将 JSX 表达式翻译成 JavaScript 表达式，并且支持 ES6 的语法。

	const name = 'Jim';
	const element = (<h1> Hi, {name} </h1>); // JSX
	
	// Babel 翻译成 JavaScript
	var name = 'Jim';
	var element = React.createElement(
	  'h1',
	  null,
	  ' Hi, ',
	  name,
	  ' '
	);
	
在 React 中也可以不使用 JSX，那么可以用 React.createElement 来创建 React 元素，就省去了 Babel 翻译的过程。而使用 JSX 的优点在于，可以使代码简洁易读。
