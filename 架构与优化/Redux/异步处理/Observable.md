# Observable

redux-observable 是 redux 一个中间件，使用了 RxJs 来驱动 action 副作用。与其目的类似的有大家比较熟悉的 redux-thunk 和 redux-saga。通过集成 redux-observable，我们可以在 Redux 中使用到 RxJS 所提供的函数响应式编程（FRP）的能力，从而更轻松的管理我们的异步副作用。

![redux-observable 示意图](https://s2.ax1x.com/2019/11/02/KOFJRf.png)

Epic 是 redux-observable 的核心概念和基础类型，几乎承载了 redux-observable 的所有。从形式上看，Epic 是一个函数，其接收一个 action stream，输出一个新的 action stream：

```ts
function (action$: Observable<Action>, state$: StateObservable<State>): Observable<Action>
```

在 redux-observable 的视角下，Redux 作为中央状态收集器，当一个 action 被 dispatch，历经某个同步或者异步任务，将 dispatch 一个新的 action，携带着它的负载（payload）到 reducer，如此反复。这么看的话，Epic 定义了 action 因果关系。

同时，FRP 模式的 RxJS 还带来了如下能力：

- 竞态处理能力
- 声明式地任务处理
- 测试友好
- 组件自治（redux-observable 1. 0 开始支持）

# 传统模式下组件的耦合

在传统的模式下，我们需要面对一个现实，对于状态的获取是**主动式（proactive）**的：

```js
const state = store.getState();
```

亦即我们需要主动取用状态，而无法监听状态变化。因此，在这种模式下，我们组件化开发的思路会是：

- 组件挂载，开启轮询
  - 搜索时，结束上次轮询，构建新的请求参数，开始新的轮询
  - 排序变动时，结束上次轮询，构建新的请求参数，开始新的轮询
  - 分页变动时，结束上次轮询，构建新的请求参数，开始新的轮询
- 组件卸载，结束轮询

在这种思路下，我们撰写搜索，排序，分页等容器时，当容器涉及的取值变动时，不仅需要在状态树上更新这些值，还需要去重启一下轮询。

![状态主动取用](https://s2.ax1x.com/2019/11/03/KX7ZJs.png)

假定我们使用 redux-thunk 来处理副作用，代码大致如下：

```ts
let pollingTimer: number = null;

function fetchUsers(): ThunkResult {
  return (dispatch, getState) => {
    const delay = pollingTimer === null ? 0 : 15 * 1000;
    pollingTimer = setTimeout(() => {
      dispatch({
        type: FETCH_START,
        payload: {}
      });
      const { repo }: { repo: IState } = getState();
      const { pagination, sort, query } = repo;
      // 封装参数
      const param: ISearchParam = {
        // ...
      };
      // 进行请求
      // fetch(param)...
    }, delay);
  };
}

export function polling(): ThunkResult {
  return dispatch => {
    dispatch(stopPolling());
    dispatch({
      type: POLLING_START,
      payload: {}
    });
    dispatch(fetchUsers());
  };
}

export function stopPolling(): IAction {
  clearTimeout(pollingTimer);
  pollingTimer = null;
  return {
    type: POLLING_STOP,
    payload: {}
  };
}

export function changePagination(pagination: IPagination): ThunkResult {
  return dispatch => {
    dispatch({
      type: CHANGE_PAGINATION,
      payload: {
        pagination
      }
    });
    dispatch(polling());
  };
}

export function changeQuery(query: string): ThunkResult {
  return dispatch => {
    dispatch({
      type: CHANGE_QUERY,
      payload: {
        query
      }
    });
    dispatch(polling());
  };
}

export function changeSort(sort: string): ThunkResult {
  return dispatch => {
    dispatch({
      type: CHANGE_SORT,
      payload: {
        sort
      }
    });
    dispatch(polling());
  };
}
```
