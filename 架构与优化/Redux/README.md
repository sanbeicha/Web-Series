[![返回目录](https://i.postimg.cc/50XLzC7C/image.png)](https://parg.co/UGZ)

# Redux

随着 JavaScript 单页应用开发日趋复杂，JavaScript 需要管理比任何时候都要多的状态。 这些 state 可能包括服务器响应、缓存数据、本地生成尚未持久化到服务器的数据，也包括 UI 状态，如激活的路由，被选中的标签，是否显示加载动效或者分页器等等。

管理不断变化的 state 非常困难。如果一个 model 的变化会引起另一个 model 变化，那么当 view 变化时，就可能引起对应 model 以及另一个 model 的变化，依次地，可能会引起另一个 view 的变化。直至你搞不清楚到底发生了什么。state 在什么时候，由于什么原因，如何变化已然不受控制。 当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。

# 设计理念

![Redux 的几大特性](https://s2.ax1x.com/2019/10/07/uWSmp6.png)

![Redux 有无对比](https://s2.ax1x.com/2019/09/01/n9la5j.png)

# 设计原则

# 链接

- https://www.redux.org.cn/docs/introduction/Motivation.html
- http://www.aliued.com/?p=3204
- https://www.leighhalliday.com/easy-mobx-redux-comparison
- https://github.com/leighhalliday/easy-mobx-redux-comparison/tree/context
