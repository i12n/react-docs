# Component 生命周期

1. Mounting: 组件实例被创建并插入 DOM 的过程，包括以下方法：
	- constructor()
		
		在 constructor 中可以进行 state 的初始化和函数的绑定。需要注意的是，constructor 必须要最先调用`super(props)`.
		
			constructor(props) {
				super(props);
				this.state = {text: ''}
			}
		
	- componentWillMount()
		
		componentWillMount 中设置 state 不会触发组件重新渲染（re-rendering），是唯一一个可以在服务端渲染过程中被调用的生命周期函数；可以将 componentWillMount 的内容放到 constructor 中去。
		
	- render()
	
		render 方法不会修改组件的 state，而且也不能与浏览器进行交互。可以将与浏览器交互的工作放到 componentDidMount 等其他生命周期函数中。如果 shouldComponentUpdate() 返回 false ，则 render 不会被触发。
		
		> render 方法不会修改组件的 state, 这里需要明确：不能在 render 中 使用 setState， 还是能够 setState 但 state 不更新？
		
	
	- componentDidMount()
		
		请求远端的数据、操作 DOM 节点都可以放在这里完成，在这里设置 state 可以触发组件的重新渲染。
		
2. Updating: 组件因 props、state 更新而重新渲染的过程，包括以下方法：
	- componentWillReceiveProps(nextProps)

		当 props 发生更新时，会触发 componentWillReceiveProps； 但是父组件的重新渲染有时也会触发componentWillReceiveProps，可以用 setState 把新的 props 更新到 state
		
	- shouldComponentUpdate(nextProps, nextState)
		
		在 shouldComponentUpdate 中可以检测 state 或 props 是否发生变化，如果没有发生变化就可以阻止组件重新渲染，当它 `return false` 时就不会执行  componentWillUpdate, render 和 componentDidUpdate
		
		当 forceUpdate() 被调用时，不会触发 shouldComponentUpdate
		
		
	- componentWillUpdate(nextProps, nextState)
		
		componentWillUpdate 在新的 props、state 被接受的时候被执行，它的里面不允许使用 this.setState()。
		
	- render()
	- componentDidUpdate()
		
		可以操作DOM以及进行网络请求
		
3. Unmouting: 组件从DOM中被移除
	- componentWillUnmount()
		
		进行收尾工作
		

