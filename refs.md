#refs

通常 `props` 作为父组件与子组件交互的唯一方式，因为在响应式数据流中，只需要更给子组件传递最新属性，就能够使子组件被更新。而 refs 提供了另外一种更新子组件的方式，refs 可以拿到组件所对应的实例对象，并且通过回调能够更新子组件的实例对象或DOM元素。

	import React, {Component} from 'react';
	
	export default class Input extends Component {
	   	render() {
	     	return (<input ref={self => self.focus()}/>)
	   	}
	}
	
refs 的回调函数在组件 mount 的时候被调用，因此在 componentDidMount 可以使用 refs 所引用的子组件；我们可以在回调函数中将组件的DOM元素对象保存在 class 的一个属性中，以方便在组件中使用它。

下面是用 refs 实现的一个 Uncontrolled Input：

	import React, {Component} from 'react';
	
	export default class Input extends Component {
	    render() {
	        return (
	            <div>
	                <input defaultValue='' ref={self => this.inputText = self} />
	                <button onClick={() => alert(this.inputText.value)}>click</button>
	            </div>
	        )
	    }
	}

Controlled Input 实现如下：

	import React, {Component} from 'react';
	
	export default class Input extends Component {
	    constructor(props) {
	        super();
	        this.state = {text: ''}
	    }
	    render() {
	        return (
	            <div>
	                <input defaultValue={this.state.text} onChange={e => this.setState({text: e.target.value})} />
	                <button onClick={() => alert(this.state.text)}>click</button>
	            </div>
	        )
	    }
	}

关于 Uncontrolled Input 和 Controlled Input 的区别可以查看以下介绍：  

- [Controlled and uncontrolled form inputs in React don't have to be complicated](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)
- [Uncontrolled Components](https://facebook.github.io/react/docs/uncontrolled-components.html)


	
