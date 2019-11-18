# React Hooks 源码解析

今天我将会深入 React hooks 的实现来让我们更加了解它。这个神奇的特性存在的问题是，一旦出现问题就很难调试，因为它有复杂的堆栈跟踪支持。因此，通过深入理解 React hooks 的系统，我们就可以在遇到问题时非常快的解决它们，甚至可以提前避免错误发生。

首先，让我们进入需要确保 hooks 在 React 的作用域调用的机制，因为你现在可能知道如果在没有正确的上下文调用钩子是没有意义的。

![React Hooks 示意图](https://s2.ax1x.com/2019/11/17/MrVzy6.png)

# dispatcher

dispatcher 是包含了 hooks 函数的共享对象。它将根据 ReactDom 的渲染阶段来动态分配或者清除，并且确保用户无法在 React 组件外访问 hooks。我们可以在渲染根组件前通过简单的切换来使用正确的 dispatcher，用一个叫做 enableHooks 的标志来开启/禁用；这意味这从技术上来说，我们可以在运行时开启/禁用挂钩。React 16.6.x 就已经有了试验性的实现，只不过它是被禁用的。

当我们执行完渲染工作时，我们将 dispatcher 置空从而防止它在 ReactDOM 的渲染周期之外被意外调用。这是一种可以确保用户不做傻事的机制。dispatcher 在每一个 hook 调用中使用 resolveDispatcher()这个函数来调用。就像我之前说的，在 React 的渲染周期之外调用是毫无意义的，并且 React 会打印出警告信息“Hooks 只能在函数组件的主体内部调用。

```js
let currentDispatcher;
const dispatcherWithoutHooks = {
  /* ... */
};
const dispatcherWithHooks = {
  /* ... */
};

function resolveDispatcher() {
  if (currentDispatcher) return currentDispatcher;
  throw Error("Hooks can't be called");
}
```

## The hooks queue

在使用场景之后，hooks 表示为在调用顺序下链接在一起的节点。它们被表示成这样是因为 hooks 并不是简单的创建然后又把它遗留下来。它们有一种可以让他们变成它们自己的机制。一个 Hook 有几个我希望你可以在深入研究实现之前记住的属性：

- 它的初始状态在首次渲染时被创建。

- 她的状态可以即时更新。

- React 会在之后的渲染中记住 hook 的状态。

- React 会根据调用顺序为您提供正确的状态

- React 会知道这个 hook 属于哪个 Fiber。

因此，我们需要重新思考我们查看组件状态的方式。到目前为止，我们认为它就像是一个普通的对象：

```json
{
  "foo": "foo",
  "bar": "bar",
  "baz": "baz"
}
```

但是在处理 hook 时，它应该被视为一个队列，其中每个节点代表一个状态的单个模型：

```js
{
  memoizedState: 'foo',
  next: {
    memoizedState: 'bar',
    next: {
      memoizedState: 'bar',
      next: null
    }
  }
```

可以在实现中查看单个 hook 节点的模式。你会看到 hook 有一些额外的属性，但是理解钩子如何工作的关键在于 memoizedState 和 next。其余属性由 useReducer()hook 专门用于缓存已经调度的操作和基本状态，因此在各种情况下，还原过程可以作为后备重复：

- baseState - 将给予 reducer 的状态对象。

- baseUpdate- 最近的创建了最新 baseState 的调度操作。

- queue - 调度操作的队列，等待进入 reducer。

回到 hooks，在每个函数组件调用之前，将调用一个名为 prepareHooks()的函数，其中当前 fiber 及其 hooks 队列中的第一个 hook 节点将被存储在全局变量中。这样，只要我们调用一个 hook 函数（useXXX()），就会知道要在哪个上下文中运行。
