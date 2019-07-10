# 交互与动画

[![&#x8FD4;&#x56DE;&#x76EE;&#x5F55;](https://i.postimg.cc/50XLzC7C/image.png)](https://parg.co/UGZ)

## 交互与动画

```javascript
export default class LeashedOne extends React.Component {
  constructor(props) {
    super(props);
    this.onChange = this.onChange.bind(this);
    this.onChangeDebounce = _.debounce(
      value => this.props.onChange(value),
      300
    );
  }
  onChange(e) {
    this.onChangeDebounce(e.target.value);
  }
  render() {
    return <input onChange={this.onChange} />;
  }
}
```

## 页面帧率 FPS

## 重渲染策略

