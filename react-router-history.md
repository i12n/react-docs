# React-Router history

[history](https://github.com/ReactTraining/history) 是专门管理、维护 `window.location` 的工具，可以对 `window.location` 的 pathname、hash 进行修改。在 React-Router 实现的应该中，`history` 是 Router 必不可少的一个 props：

```
import { Router } from 'react-router'
import createBrowserHistory from 'history/createBrowserHistory'

const history = createBrowserHistory()

<Router history={history}>
  <App/>
</Router>
```
在 [Router 源码](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/modules/Router.js) 中，history 是必选的：

```
static propTypes = {
  history: PropTypes.object.isRequired,
  children: PropTypes.node
}
```

history 提供了一个 `history.listen` 方法，在监听到 location 发生变化的时候执行回调函数，如下：

```
import createBrowserHistory from 'history/createBrowserHistory'

const history = createBrowserHistory()

history.listen((location, action) => {
  console.log(`The current URL is ${location.pathname}${location.search}${location.hash}`)
  console.log(`The last navigation action was ${action}`)
})
```
如果在单页应用中统计 pv，可以将统计 pv 的代码放在 `history.listen` 中，一旦 location 发生变化便可上报.

[Router](https://reacttraining.com/react-router/core/api/Router) 是一个通用的底层接口，在它的基础上实现了：

- BrowserRouter
- HashRouter
- MemoryRouter
- NativeRouter
- StaticRouter

在 `<Router>` 与 `BrowserRouter` 中 history 的使用是有区别的，
`BrowserRouter` 将重新生成一个 history，会忽略 props 中的 history:

```
import { createBrowserHistory as createHistory } from 'history'
import Router from './Router'

class BrowserRouter extends React.Component {
  history = createHistory(this.props)
  
  ... ...

  render() {
    return <Router history={this.history} children={this.props.children}/>
  }
}
```
除此之外，两者没有任何区别，具体细节见[源码](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/modules/BrowserRouter.js)，但是这需要在使用的时候，注意这样的场景，如果需要用 `history.listen` 监听的时候，一定要用 `Router`，如果使用 `BrowserRouter` 会将 `history` 而导致监听失效，譬如：

```
import { BrowserRouter } from 'react-router-dom'
import createBrowserHistory from 'history/createBrowserHistory'

const history = createBrowserHistory()

// 监听的实例 与 BrowserRouter 真正使用的不是同一个
history.listen((location, action) => {
  console.log(`The current URL is ${location.pathname}${location.search}${location.hash}`)
  console.log(`The last navigation action was ${action}`)
})

<BrowserRouter history={history}>
  <App/>
</BrowserRouter>
```





