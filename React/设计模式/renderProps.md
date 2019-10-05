# renderProps

renderProps 是指一种在 React 组件之间使用一个值为函数的 prop 在 React 组件间共享代码的简单技术。带有 render prop 的组件带有一个返回一个 React 元素的函数并调用该函数而不是实现自己的渲染逻辑。

```js
<DataProvider render={data => <h1>Hello {data.target}</h1>} />
```

我们常常在交叉关注点（Cross-Cutting Concerns）使用 renderProps，组件在 React 是主要的代码复用单元，但如何共享状态或一个组件的行为封装到其他需要相同状态的组件中并不是很明了。

```js
class WindowWidth extends React.Component {
  constructor() {
    super();
    this.state = { width: 0 };
  }

  componentDidMount() {
    this.setState({ width: window.innerWidth }, () =>
      window.addEventListener('resize', ({ target }) =>
        this.setState({ width: target.innerWidth })
      )
    );
  }

  render() {
    return this.props.children(this.state.width);
  }
}
```

# 案例

## 响应鼠标移动

## 通用数据加载
