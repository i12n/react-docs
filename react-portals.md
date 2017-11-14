# React Portals

- 一种场景

	大多数情况下，React 的元素都是以层级结构存在的，数据由父级元素流向子级元素。但也有极少数场景，需要组件挂载到其他的节点上，譬如一个弹窗组件（Modal）往往需要挂载到 body 元素上，否则可能会出现被其他元素遮挡的现象。

- 如何解决

	放弃层级结构，将 Modal 挂载到 body 元素上渲染，ant-design 的 [Modal.confirm](https://github.com/ant-design/ant-design/blob/master/components/modal/confirm.tsx) 就是这么实现的, 简单的代码示例如下：
	
	```
	import React from 'react';
	import ReactDOM from 'react-dom';
	import Dialog from './Modal';
	
	export default function confirm() {
	  let div = document.createElement('div');
	  document.body.appendChild(div);
	  
	  function close() {
	    const unmountResult = ReactDOM.unmountComponentAtNode(div);
	    if (unmountResult && div.parentNode) {
	      div.parentNode.removeChild(div);
	    }
	  }

	  ReactDOM.render(
	    <Dialog>
	    	Just see see, can't run.
	    </Dialog>
	  , div);
	  return {
	    destroy: close,
	  };
	}
	```
	这种方式打破了层级结构，并且在组件销毁时需要额外做一些处理.
	
- React Portals
	React 16 版本正式引入了 Portals，可以很好的应用在上面的场景中，既能将组件挂载到其他 DOM 元素上，又能保持层级结构，而且父级组件还能监听到子级组件事件.
	
	Portals 用法很简单，在 render 方法中，执行 `ReactDOM.createPortal` 即可，具体可以[参考代码](https://codepen.io/gaearon/pen/jGBWpE) 

	```
	render() {
	  // React does *not* create a new div. It renders the children into `domNode`.
	  // `domNode` is any valid DOM node, regardless of its location in the DOM.
	  return ReactDOM.createPortal(
	    this.props.children,
	    domNode,
	  );
	}
	```

- 更多资料

	[https://reactjs.org/docs/portals.html](https://reactjs.org/docs/portals.html)
	[https://zhuanlan.zhihu.com/p/29880992](https://zhuanlan.zhihu.com/p/29880992)