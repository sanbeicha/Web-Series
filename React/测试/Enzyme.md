[![返回目录](https://i.postimg.cc/50XLzC7C/image.png)](https://github.com/wx-chevalier/Web-Series)

# Enzyme

Enzyme 允许我们在 DOM 环境中测试 React 组件，首先需要在项目中安装 Enzyme：

```
$ npm i enzyme @types/enzyme enzyme-to-json enzyme-adapter-react-16 -D
```

然后在 jest.config.js 文件中添加 snapshotSerializers 与 setupTestFrameworkScriptFile 配置：

```js
module.exports = {
  // OTHER PORTIONS AS MENTIONED BEFORE

  // Setup Enzyme
  snapshotSerializers: ['enzyme-to-json/serializer'],
  setupTestFrameworkScriptFile: '<rootDir>/src/setupEnzyme.ts'
};
```

然后创建初始化文件：

```ts
// src/setupEnzyme.ts
import { configure } from 'enzyme';
import * as EnzymeAdapter from 'enzyme-adapter-react-16';
configure({ adapter: new EnzymeAdapter() });
```

简单的 React 组件如下：

```ts
import * as React from 'react';

export class CheckboxWithLabel extends React.Component<
  {
    labelOn: string;
    labelOff: string;
  },
  {
    isChecked: boolean;
  }
> {
  constructor(props) {
    super(props);
    this.state = { isChecked: false };
  }

  onChange = () => {
    this.setState({ isChecked: !this.state.isChecked });
  };

  render() {
    return (
      <label>
        <input
          type="checkbox"
          checked={this.state.isChecked}
          onChange={this.onChange}
        />
        {this.state.isChecked ? this.props.labelOn : this.props.labelOff}
      </label>
    );
  }
}
```

其对应的测试文件如下：

```ts
import * as React from 'react';
import { shallow } from 'enzyme';
import { CheckboxWithLabel } from './checkboxWithLabel';

test('CheckboxWithLabel changes the text after click', () => {
  const checkbox = shallow(<CheckboxWithLabel labelOn="On" labelOff="Off" />);

  // Interaction demo
  expect(checkbox.text()).toEqual('Off');
  checkbox.find('input').simulate('change');
  expect(checkbox.text()).toEqual('On');

  // Snapshot demo
  expect(checkbox).toMatchSnapshot();
});
```

# 链接

- https://mp.weixin.qq.com/s/isZLVenQrAUzA77O4TYVfQ
