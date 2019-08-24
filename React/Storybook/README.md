![](https://i.postimg.cc/wjc6ss1H/image.png)

# Storybook

Storybook 是用户界面开发环境和 UI 组件的游乐场。 该工具使开发人员能够独立创建组件，并在隔离的开发环境中以交互方式展示组件。Storybook 在主应用程序之外运行，因此用户可以独立开发 UI 组件，而无需担心应用程序特定的依赖关系和要求。

# React

基础的 Story 的格式如下：

```js
// file: src/stories/index.js

import React from 'react';
import { storiesOf } from '@storybook/react';
import { action } from '@storybook/addon-actions';
import Button from '../components/Button';

storiesOf('Button', module)
  .add('with text', () => (
    <Button onClick={action('clicked')}>Hello Button</Button>
  ))
  .add('with some emoji', () => (
    <Button onClick={action('clicked')}>
      <span role="img" aria-label="so cool">
        😀 😎 👍 💯
      </span>
    </Button>
  ));
```
