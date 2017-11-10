# React Keys

- 视图更新机制

	当组件的 state 或 props 发生变化时，组件会调用render() 方法重新渲染 UI 视图. React 采用 Diff 算法来分析 DOM 树变化前后的差异，以最小的代价更新 DOM. 为了高效的更新视图的效率，React 在使用 Diff 算法时做了一些简化工作：
	
	1. 不同类型的元素会产生不同的 DOM 树，一旦根元素的类型发生变化，整个子树需要重新生成；另外，只需要对比同一层级的元素.
	2. 使用 `key` 属性标记一个子元素，通过对比相同  key 值的元素，计算出最少的差异.

- 稳定的 `key` 属性

	要保证元素 key 值的稳定性和唯一性，请遵守以下原则：
	
	避免使用随机数、时间戳做为 key 值. 在下面的例子中，当组件重新 render 时，两次的 key 值不一样会导致 `li` 元素重新渲染.
	
	```
	<li key={ +new Date() }> Date </li>
	```
	避免使用数组的索引作为 key 值. 在下面的例子中，如果在 list 数组插入一个元素，会导致这个元素之后的所有元素重新渲染，如果使用数组元素的某个唯一的项作为 key 则不会有这样的问题存在.
	
	```
	<ul>
		list.map((x, i) => (
			<li key={i}> Date </li>
		))
	</ul>
	```
- `key` 与 props 区别
	
	在组件内部，是无法读到 key 值的；但是能读到 props 的值.
	> Keys serve as a hint to React but they don’t get passed to your components. If you need the same value in your component, pass it explicitly as a prop with a different name.
	
	改变 key 值会 mount 一个新组件；而更改 props 组件只重新调用 render() 方法. 为了组件回归到初始状态，可以通过更改 key 的方法实现 [代码示例](https://codepen.io/anon/pen/GOWNYO?editors=0011).


- 相关文档

	[https://reactjs.org/docs/lists-and-keys.html](https://reactjs.org/docs/lists-and-keys.html)
	
	[https://reactjs.org/docs/reconciliation.html#keys](https://reactjs.org/docs/reconciliation.html#keys)
	
	[http://taobaofed.org/blog/2016/08/24/react-key/](http://taobaofed.org/blog/2016/08/24/react-key/)
	
	[http://www.jianshu.com/p/0218ff2591ec](http://www.jianshu.com/p/0218ff2591ec)
		




