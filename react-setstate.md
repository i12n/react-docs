# setState

## setState 用法

通常页面的触发了事件或接口请求响应的时候，都会更新页面的 UI 的展示，在 React 中用 `setState()` 更新状态的方式，来重新渲染组件以及它的子组件。
`setState()` 的用法如下：

```js
setState(updater[, callback])
```

`updater` 可以是一个 `object` 或 `function` 类型

`callback` 表示更新状态之后的回掉函数

## setState 注意事项

`setState(updater[, callback])` 并不保证状态更新是立即生效的，它的机制是将更新状态操作存入队列，然后集中更新状态。如果需要在状态更新完成之后进行相关操作，要在 `callback` 或 `componentDidUpdate` 里面实现。

```js
console.log(this.state.count); // 0

this.setState({count: this.state.count + 1}, () => {
  console.log(this.state.count); // 1
});

console.log(this.state.count); // 0

this.setState({count: this.state.count + 1}, () => {
  console.log(this.state.count); // 1
});
```

`setState(updater[, callback])` 的参数 `updater` 为 `function` 时，可以拿到队列更新过程中最新的 `state`。

```js
console.log(this.state.count); // 0

this.setState({count: this.state.count + 1}, () => {
  console.log(this.state.count); // 2
});

console.log(this.state.count); // 0

this.setState((state, props) => ({
    count: state.count + 1
}), () => {
  console.log(this.state.count); // 2
})
```

## setState 源码解析

### 写入队列

[`setState`](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react/src/ReactBaseClasses.js#L57) 的定义比较简单：

```js
/**
 * setState 的主要工作就是将更新的操作写入更新队列中
*/
Component.prototype.setState = function(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```

[`enqueueSetState`](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactFiberClassComponent.js#L183) 触发队列更新：

```js
  /**
   * payload 是 setState 的第一个参数 partialState
   * update = {
   *  ...,
   *  payload: payload,
   *  callback: callback,
   * }
  */
  enqueueSetState(inst, payload, callback) {
    const fiber = getInstance(inst);
    const currentTime = requestCurrentTimeForUpdate();
    const suspenseConfig = requestCurrentSuspenseConfig();
    const expirationTime = computeExpirationForFiber(
      currentTime,
      fiber,
      suspenseConfig,
    );

    const update = createUpdate(expirationTime, suspenseConfig);
    update.payload = payload;
    if (callback !== undefined && callback !== null) {
      update.callback = callback;
    }

    enqueueUpdate(fiber, update);
    scheduleWork(fiber, expirationTime);
  },
```

[`enqueueUpdate`](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactUpdateQueue.js#L207) 用于更新队列：

```js
/**
 * updateQueue.shared.pending 是环状队列的尾
 * updateQueue.shared.pending.next 是环状队列的头
*/
export function enqueueUpdate<State>(fiber: Fiber, update: Update<State>) {
  const updateQueue = fiber.updateQueue;
  if (updateQueue === null) {
    // Only occurs if the fiber has been unmounted.
    return;
  }

  const sharedQueue = updateQueue.shared;
  const pending = sharedQueue.pending;
  if (pending === null) {
    // This is the first update. Create a circular list.
    update.next = update;
  } else {
    update.next = pending.next;
    pending.next = update;
  }
  sharedQueue.pending = update;
}
```

### 计算逻辑

更新 state 过程由 [processUpdateQueue](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactUpdateQueue.js#L337) 控制：

```ts
/**
 *  queue.shared.pending 加入 queue.baseQueue 队尾,
 *  执行 getStateFromUpdate 计算出新的 newState
 *  setState 的 callback 存入 queue.effects
 *  返回 newState
*/
export function processUpdateQueue<State>(
  workInProgress: Fiber,
  props: any,
  instance: any,
  renderExpirationTime: ExpirationTime,
): void {
  // This is always non-null on a ClassComponent or HostRoot
  const queue: UpdateQueue<State> = (workInProgress.updateQueue: any);

  hasForceUpdate = false;

  // The last rebase update that is NOT part of the base state.
  let baseQueue = queue.baseQueue;

  // The last pending update that hasn't been processed yet.
  let pendingQueue = queue.shared.pending;
  if (pendingQueue !== null) {
    // We have new updates that haven't been processed yet.
    // We'll add them to the base queue.
    if (baseQueue !== null) {
      // Merge the pending queue and the base queue.
      let baseFirst = baseQueue.next;
      let pendingFirst = pendingQueue.next;
      baseQueue.next = pendingFirst;
      pendingQueue.next = baseFirst;
    }

    baseQueue = pendingQueue;

    queue.shared.pending = null;
    // TODO: Pass `current` as argument
    const current = workInProgress.alternate;
    if (current !== null) {
      const currentQueue = current.updateQueue;
      if (currentQueue !== null) {
        currentQueue.baseQueue = pendingQueue;
      }
    }
  }
  // These values may change as we process the queue.
  if (baseQueue !== null) {
    let first = baseQueue.next;
    // Iterate through the list of updates to compute the result.
    let newState = queue.baseState;
    let newExpirationTime = NoWork;

    let newBaseState = null;
    let newBaseQueueFirst = null;
    let newBaseQueueLast = null;

    if (first !== null) {
      let update = first;
      do {
        const updateExpirationTime = update.expirationTime;
        if (updateExpirationTime < renderExpirationTime) {
          // Priority is insufficient. Skip this update. If this is the first
          // skipped update, the previous update/state is the new base
          // update/state.
          const clone: Update<State> = {
            expirationTime: update.expirationTime,
            suspenseConfig: update.suspenseConfig,

            tag: update.tag,
            payload: update.payload,
            callback: update.callback,

            next: (null: any),
          };
          if (newBaseQueueLast === null) {
            newBaseQueueFirst = newBaseQueueLast = clone;
            newBaseState = newState;
          } else {
            newBaseQueueLast = newBaseQueueLast.next = clone;
          }
          // Update the remaining priority in the queue.
          if (updateExpirationTime > newExpirationTime) {
            newExpirationTime = updateExpirationTime;
          }
        } else {
          // This update does have sufficient priority.

          if (newBaseQueueLast !== null) {
            const clone: Update<State> = {
              expirationTime: Sync, // This update is going to be committed so we never want uncommit it.
              suspenseConfig: update.suspenseConfig,

              tag: update.tag,
              payload: update.payload,
              callback: update.callback,

              next: (null: any),
            };
            newBaseQueueLast = newBaseQueueLast.next = clone;
          }
          // Mark the event time of this update as relevant to this render pass.
          // TODO: This should ideally use the true event time of this update rather than
          // its priority which is a derived and not reverseable value.
          // TODO: We should skip this update if it was already committed but currently
          // we have no way of detecting the difference between a committed and suspended
          // update here.
          markRenderEventTimeAndConfig(
            updateExpirationTime,
            update.suspenseConfig,
          );

          // Process this update.
          newState = getStateFromUpdate(
            workInProgress,
            queue,
            update,
            newState,
            props,
            instance,
          );
          const callback = update.callback;
          if (callback !== null) {
            workInProgress.effectTag |= Callback;
            let effects = queue.effects;
            if (effects === null) {
              queue.effects = [update];
            } else {
              effects.push(update);
            }
          }
        }
        update = update.next;
        if (update === null || update === first) {
          pendingQueue = queue.shared.pending;
          if (pendingQueue === null) {
            break;
          } else {
            // An update was scheduled from inside a reducer. Add the new
            // pending updates to the end of the list and keep processing.
            update = baseQueue.next = pendingQueue.next;
            pendingQueue.next = first;
            queue.baseQueue = baseQueue = pendingQueue;
            queue.shared.pending = null;
          }
        }
      } while (true);
    }

    if (newBaseQueueLast === null) {
      newBaseState = newState;
    } else {
      newBaseQueueLast.next = (newBaseQueueFirst: any);
    }

    queue.baseState = ((newBaseState: any): State);
    queue.baseQueue = newBaseQueueLast;

    // Set the remaining expiration time to be whatever is remaining in the queue.
    // This should be fine because the only two other things that contribute to
    // expiration time are props and context. We're already in the middle of the
    // begin phase by the time we start processing the queue, so we've already
    // dealt with the props. Context in components that specify
    // shouldComponentUpdate is tricky; but we'll have to account for
    // that regardless.
    markUnprocessedUpdateTime(newExpirationTime);
    workInProgress.expirationTime = newExpirationTime;
    workInProgress.memoizedState = newState;
  }
}
```

state 的计算逻辑是在 `getStateFromUpdate` 中的，具体见源代码
[react/packages/react-reconciler/src/ReactUpdateQueue.js](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactUpdateQueue.js#L264)。

```js
/**
 * 如果 payload 是一个 function:
 *   partialState = payload.call(instance, prevState, nextProps);
 * 如果 payload 是一个 object:
 *   partialState = payload
 * 最终做浅拷贝得到新的 state:
 *  newState = Object.assign({}, prevState, partialState);
*/
function getStateFromUpdate<State>(
  workInProgress: Fiber,
  queue: UpdateQueue<State>,
  update: Update<State>,
  prevState: State,
  nextProps: any,
  instance: any,
): any {
  switch (update.tag) {
    case ReplaceState: {
      const payload = update.payload;
      if (typeof payload === 'function') {
        const nextState = payload.call(instance, prevState, nextProps);
        return nextState;
      }
      // State object
      return payload;
    }
    case CaptureUpdate: {
      workInProgress.effectTag =
        (workInProgress.effectTag & ~ShouldCapture) | DidCapture;
    }
    // Intentional fallthrough
    case UpdateState: {
      const payload = update.payload;
      let partialState;
      if (typeof payload === 'function') {

        partialState = payload.call(instance, prevState, nextProps);

      } else {
        // Partial state object
        partialState = payload;
      }
      if (partialState === null || partialState === undefined) {
        // Null and undefined are treated as no-ops.
        return prevState;
      }
      // Merge the partial state and the previous state.
      return Object.assign({}, prevState, partialState);
    }
    case ForceUpdate: {
      hasForceUpdate = true;
      return prevState;
    }
  }
  return prevState;
}
```


### callback 执行

callback 的执行入口是在 [`commitLifeCycles`](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactFiberCommitWork.js#L412)

```js
/**
 * 首先，执行 componentDidMount
 * 然后，执行 commitUpdateQueue
 * commitUpdateQueue 会执行 setState 的 callback 参数
*/
const instance = finishedWork.stateNode;
if (finishedWork.effectTag & Update) {
  if (current === null) {
    startPhaseTimer(finishedWork, 'componentDidMount');

    instance.componentDidMount();
    stopPhaseTimer();
  } else {
    const prevProps =
      finishedWork.elementType === finishedWork.type
        ? current.memoizedProps
        : resolveDefaultProps(finishedWork.type, current.memoizedProps);
    const prevState = current.memoizedState;
    startPhaseTimer(finishedWork, 'componentDidUpdate');

    instance.componentDidUpdate(
      prevProps,
      prevState,
      instance.__reactInternalSnapshotBeforeUpdate,
    );
    stopPhaseTimer();
  }
}
const updateQueue = finishedWork.updateQueue;
if (updateQueue !== null) {
  // We could update instance props and state here,
  // but instead we rely on them being set during last render.
  // TODO: revisit this when we implement resuming.
  commitUpdateQueue(
    finishedWork,
    updateQueue,
    instance,
    committedExpirationTime,
  );
}

```

[`commitUpdateQueue`](https://github.com/facebook/react/blob/0cf22a56a18790ef34c71bef14f64695c0498619/packages/react-reconciler/src/ReactUpdateQueue.js#L529)代码如下：

```js
/**
 * 将 updateQueue.effects 中的 callback 依次执行
*/
export function commitUpdateQueue<State>(
  finishedWork: Fiber,
  finishedQueue: UpdateQueue<State>,
  instance: any,
  renderExpirationTime: ExpirationTime,
): void {
  // Commit the effects
  const effects = finishedQueue.effects;
  finishedQueue.effects = null;
  if (effects !== null) {
    for (let i = 0; i < effects.length; i++) {
      const effect = effects[i];
      const callback = effect.callback;
      if (callback !== null) {
        effect.callback = null;
        callCallback(callback, instance); // callback.call(instance);
      }
    }
  }
}
```
