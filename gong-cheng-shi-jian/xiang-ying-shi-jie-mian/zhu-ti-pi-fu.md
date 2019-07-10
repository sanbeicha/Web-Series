# 主题皮肤

## 主题皮肤

## 动态样式文件

改变皮肤link元素的href地址。例如：

```css
<link id="skinLink" href="skin-default.css" rel="stylesheet" type="text/css">
```

换皮肤的时候JS改变href属性值：

```javascript
skinLink.href = 'skin-red.css';
```

### alternate

```javascript
<link href="reset.css" rel="stylesheet" type="text/css">

<link href="default.css" rel="stylesheet" type="text/css" title="默认">
<link href="red.css" rel="alternate stylesheet" type="text/css" title="红色">
<link href="green.css" rel="alternate stylesheet" type="text/css" title="绿色">
```

上面4个元素，共出现了3中不同性质的CSS样式文件加载：

* 没有title属性，rel属性值仅仅是stylesheet的无论如何都会加载并渲染，如reset.css；
* 有title属性，rel属性值仅仅是stylesheet的作为默认样式CSS文件加载并渲染，如default.css；
* 有title属性，rel属性值同时包含alternate stylesheet的作为备选样式CSS文件加载，默认不渲染，如red.css和green.css；

这里有个非常有趣的特性，那就是rel="stylesheet"的如果有title属性并有值，性质上就变成了一个可以控制其渲染或者不渲染的特殊元素了。

使用JavaScript代码修改元素DOM对象的disabled值为false，可以让默认不渲染的CSS开始渲染。注意，必须是DOM元素对象的disabled属性，而不是HTML元素的disabled属性，元素是没有disabled属性的。

```javascript
// 渲染red.css这个皮肤
document.querySelector('link[href="red.css"]').disabled = false;
```

## 全局变量

