# WebWorker

## Web Worker

Web Worker 即是运行在后台独立线程中的 JavaScript 脚本，可以用其来执行阻塞性程序以避免影响到页面的性能。Web Worker 的两大特性是高效与并行，因为浏览器是单线程的，任何大量耗时的 JS 任务都会卡住界面，使浏览器无法响应任何操作，这样的用户体验非常糟糕。Web Workers 可以将耗时任务拆解出去，降低主线程的压力，避免主线程无响应。但 CPU 资源是有限的，Web Workers 并不能增加总体运行效率，算上通信的损耗，整体计算效率会有一定的下降。

Worker 会运行在独立的不同于当前 window 的全局上下文中，因此我们并不能再 Worker 中进行 DOM 操作。

* 直接使用 Worker 构造函数创建的 Worker 被称为 Dedicated Worker, 其运行在所谓的 DedicatedWorkerGlobalScope 代表的上下文中，其仅允许创建脚本进行访问。
* 另一种 Shared Worker 则运行在 SharedWorkerGlobalScope 代表的上下文中，其允许多个脚本访问。

实际上 ServiceWorkers 也是 Web Worker 的一种，其常被用于 Web 应用之间，或者浏览器与网络之间的代理；致力于提供更良好的离线体验，并且能够介入到网络请求中完成缓存与更新等操作。ServiceWorkers 同样能够被用于进行通知推送与后台同步接口，更多关于 ServiceWorkers 与 PWA 相关内容可以参考 [PWA-CheatSheet](https://parg.co/Gzb)。

## 基础使用

```javascript
// 判断浏览器是否支持 Worker
typeof Worker !== 'undefined';

// 从脚本中创建 Worker
new Worker('workers.js');

// 使用字符串方式创建 Worker
new Worker('data:text/javascript;charset=US-ASCII,...');
```

[worker-loader](https://github.com/webpack-contrib/worker-loader) 是一个 webpack 插件，可以将一个普通 JS 文件的全部依赖提取后打包并替换调用处，以 Blob 形式内联在源码中。

```javascript
import Worker from 'worker-loader!./file.worker.js';

const worker = new Worker();

// 转化为下述代码
const blob = new Blob([codeFromFileWorker], { type: 'application/javascript' });
const worker = new Worker(URL.createObjectURL(blob));
```

我们也可以自己通过 Blob 的方式创建：

```javascript
const code = `
  importScripts('https://xxx.com/xxx.js');
  self.onmessage = e => {};
`;

const blob = new Blob([code], { type: 'application/javascript' });
const worker = new Worker(URL.createObjectURL(blob));
```

[workerize-loader](webworker.md)

```javascript
// worker.js
export function expensive(time) {}

// app.js
import worker from 'workerize-loader!./worker';

let instance = worker(); // `new` is optional

instance.expensive(1000).then(count => {
  console.log(`Ran ${count} loops`);
});
```

You cannot use Local Storage in service workers. It was decided that service workers should not have access to any synchronous APIs. You can use IndexedDB instead, or communicate with the controlled page using postMessage\(\).

## 网络请求

By default, cookies are not included with fetch requests, but you can include them as follows: fetch\(url, {credentials: 'include'}\).

```javascript
function XHRWorker(url, ready, scope) {
  var oReq = new XMLHttpRequest();
  oReq.addEventListener(
    'load',
    function() {
      var worker = new Worker(
        window.URL.createObjectURL(new Blob([this.responseText]))
      );
      if (ready) {
        ready.call(scope, worker);
      }
    },
    oReq
  );
  oReq.open('get', url, true);
  oReq.send();
}

function WorkerStart() {
  XHRWorker(
    'http://static.xxx.com/js/worker.js',
    function(worker) {
      worker.postMessage('hello world');
      worker.onmessage = function(e) {
        console.log(e.data);
      };
    },
    this
  );
}

WorkerStart();
```

