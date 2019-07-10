# 组件测试

[![&#x8FD4;&#x56DE;&#x76EE;&#x5F55;](https://i.postimg.cc/50XLzC7C/image.png)](https://github.com/wx-chevalier/Web-Series)

## React 组件测试

ES6:

```javascript
// setup file
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
// test file
import { shallow, mount, render } from 'enzyme';

const wrapper = shallow(<Foo />);
```

ES5:

```javascript
// setup file
var enzyme = require('enzyme');
var Adapter = require('enzyme-adapter-react-16');

enzyme.configure({ adapter: new Adapter() });
// test file
var enzyme = require('enzyme');

var wrapper = enzyme.shallow(<Foo />);
```

