#Redux

Redux 是可预测的JS应用状态容器。Redux 将整个 APP 的 state (状态) 统一管理，存储在单一的 store 对象树中(**单一数据源**); APP 只能通过发送 Action 到 Redux 修改 state，除了 Redux 之外都不能修改 state (**只读状态**); Redux 修改 state 是由一个用户定义的纯函数来完成的，这个被称为 reducer 的纯函数会将 Action 对应的 state 返回(**纯函数去修改状态**).


![state 状态](https://segmentfault.com/image?src=http://ooo.0o0.ooo/2016/01/25/56a6480c1c954.png&objectId=1190000004364418&token=ae5ae2448ce88a7fe96ef957ff0892a2)


简单代码示例

```js
import {createStore} from 'redux';

/* 定义 reducer, 根据 action 修改并返回 state */
function reducer(state, action) {
    switch (action.type) {
        case 'ON':
            state = 'open ...';
            return state;
        case 'OFF':
            state = 'close ...';
            return state;
        default:
            state = 'close ...';
            return state
    }
}

let store = createStore(reducer);

store.subscribe(() =>
  console.log(store.getState())
)

store.dispatch({ type: 'ON' })
store.dispatch({ type: 'OFF' })
```

相关文章

- [https://segmentfault.com/a/1190000004364418](https://segmentfault.com/a/1190000004364418)
- [http://react-china.org/t/redux/2687/10](http://react-china.org/t/redux/2687/10)
