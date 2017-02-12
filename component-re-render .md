# Component 重新渲染 

通常情况下，只有在 props 或 state 发生变化时会触发组件 re-render; 如果 render() 依赖了其他数据而需要re-render 时，需要调用 forceUpdate 来强制更新组件。调用 forceUpdate 在组件重新渲染过程中会跳过 shouldComponentUpdate(), 子组件的生命周期会被正常触发。应该避免使用 forceUpdate, 尽量使用 props 和 state 来维持组件的渲染，这样做的好处是，组件不依赖强制刷新，有利于组件的复用。

每次 setState 都会触发组件 re-render, 但是在某些情况下 state 中的数据可能并没有发生变化, 因此这时进行 re-render 是没有必要的，需要对此进行优化。 shouldComponentUpdate 可以进行优化渲染，如果 state 没有发生变化 shouldComponentUpdate 返回 false，就不会触发组件的 re-render。

```jsx
constructor(props) {
    super();
    this.state = {data: 1};
}
shouldComponentUpdate(nextProps, nextState) {
    if (nextState.data !== this.state.data) {
        return true;
    }
    return false;
}
```

如果 state 里面包含嵌套对象，而嵌套对象的修改，可能会检测不出来：

```js
constructor(props) {
    super();
    this.state = {data: {name: 'Jim', age: 12}}
}
shouldComponentUpdate(nextProps, nextState) {
    if (nextState.data !== this.state.data) {
        return true;
    }
    return false;
}

// 更新 state, 但是 shouldComponentUpdate 返回 false
var data = this.state.data;
data.name = 'Tom'
this.setState({data: data})

```
这时就需要将 state 进行 deepCopy 和 deepCompare，或者使用 Immutable Data. 

- [React Component forceupdate](https://facebook.github.io/react/docs/react-component.html#forceupdate) 
- [Can you force a React component to rerender without calling setState?](http://stackoverflow.com/questions/30626030/can-you-force-a-react-component-to-rerender-without-calling-setstate)
- [React-js-pure-render-performance-anti-pattern](https://medium.com/@esamatti/react-js-pure-render-performance-anti-pattern-fb88c101332f#.jpa9kgmvl)
- [Immutable 详解及 React 中实践](https://github.com/camsong/blog/issues/3)