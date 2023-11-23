# 趣学nodejs


**Node.js与Javscript的分层关系**
|  | 实现  |
|  ----  | ----  |
| 运行时环境 | Node.js |
| 执行引擎  | Chrome V8 |
| 语言实现  | Javascript |
| 脚本语言规范  | ECMAScript |

## 事件循环与异步I/O

Node.js的事件循环是基于**libuv**库。
**libuv**(Unicorn Velociraptor-独角伶盗龙)库是多平台的C库，提供基于事件循环和异步I/O的支持。


node.js事件循环主要代码：
```c++
do {
  if (env->is_stopping()) break;
  uv_run(env->event_loop(), UV_RUN_DEFAULT);
  if (env->is_stopping()) break;

  platform->DrainTasks(isolate);

  more = uv_loop_alive(env->event_loop());
  if (more && !env->is_stopping()) continue;

  if (EmitProcessBeforeExit(env).IsNothing())
    break;

  {
    HandleScope handle_scope(isolate);
    if (env->RunSnapshotSerializeCallback().IsEmpty()) {
      break;
    }
  }

  // Emit `beforeExit` if the loop became alive either after emitting
  // event, or after running some callbacks.
  more = uv_loop_alive(env->event_loop());
} while (more == true && !env->is_stopping());

```
uv_run(env->event_loop(), UV_RUN_DEFAULT)是跑一轮事件循环。do-while外层循环保证在uv_run处理完事件后，系统新增的时候可以继续被处理。

uv_run的[代码](https://github.com/nodejs/node/blob/v18.12.1/deps/uv/src/unix/core.c#L369-L426)（(以unix系统为例)）。
```c
int uv_run(uv_loop_t* loop, uv_run_mode mode) {
  int timeout;
  int r;
  int ran_pending;

  // 判断有无活跃事件
  r = uv__loop_alive(loop);
  if (!r)
    uv__update_time(loop); // 更新处理loop后的时间

  while (r != 0 && loop->stop_flag == 0) {
    uv__update_time(loop);
    uv__run_timers(loop);
    ran_pending = uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);

    timeout = 0;
    if ((mode == UV_RUN_ONCE && !ran_pending) || mode == UV_RUN_DEFAULT)
      // 获取最近会触发的定时器时间
      timeout = uv_backend_timeout(loop);

    uv__io_poll(loop, timeout);

    /* Run one final update on the provider_idle_time in case uv__io_poll
     * returned because the timeout expired, but no events were received. This
     * call will be ignored if the provider_entry_time was either never set (if
     * the timeout == 0) or was already updated b/c an event was received.
     */
    uv__metrics_update_idle_time(loop);

    uv__run_check(loop);
    uv__run_closing_handles(loop);

    if (mode == UV_RUN_ONCE) {
      /* UV_RUN_ONCE implies forward progress: at least one callback must have
       * been invoked when it returns. uv__io_poll() can return without doing
       * I/O (meaning: no callbacks) when its timeout expires - which means we
       * have pending timers that satisfy the forward progress constraint.
       *
       * UV_RUN_NOWAIT makes no guarantees about progress so it's omitted from
       * the check.
       */
      uv__update_time(loop);
      uv__run_timers(loop);
    }

    r = uv__loop_alive(loop);
    if (mode == UV_RUN_ONCE || mode == UV_RUN_NOWAIT)
      break;
  }

  /* The if statement lets gcc compile it to a conditional store. Avoids
   * dirtying a cache line.
   */
  if (loop->stop_flag != 0)
    loop->stop_flag = 0;

  return r;
}

```
在一次uv_run()循环中处理顺序：
* 定时器（小根堆实现）
* Peng状态的I/O事件（上一次循环被退出）
* 空转事件？
* 准备事件（在阻塞I/O前执行）
* Poll I/O事件
* 复查事件
* 调用关闭回调

如果存在**空转事件**，阻塞I/O的时间为0。Node.js v18.12.1 中，只有一个地方用到了空转事件，那就是 setImmediate()。为什么它能所谓的“immediate”？就是通过一个空的空转事件来让小乘轮回强制不等待 I/O。
```c
static int uv__backend_timeout(const uv_loop_t* loop) {
  if (loop->stop_flag == 0 &&
      /* uv__loop_alive(loop) && */
      (uv__has_active_handles(loop) || uv__has_active_reqs(loop)) &&
      QUEUE_EMPTY(&loop->pending_queue) &&
      QUEUE_EMPTY(&loop->idle_handles) &&
      loop->closing_handles == NULL)
    return uv__next_timeout(loop);
  return 0;
}

int uv_backend_timeout(const uv_loop_t* loop) {
  if (QUEUE_EMPTY(&loop->watcher_queue))
    return uv__backend_timeout(loop);
  /* Need to call uv_run to update the backend fd state. */
  return 0;
}
```

## Node.js模块机制

模块加载的机制类似IIFE(Immediately Invoked Function Expression：立即函数调用表达式)。它通过一个闭包来做到内部变量的一些隔离，然后通过立即执行该闭包来得到相应的结果。这样就可以很方便地通过执行一些复杂逻辑来得到一个所谓的“模块”，而把逻辑变成内部私有形式给隔离开来。
``` javascript
const pub = (function() {
    const priv = "你那不到我";
    return priv.replace('不', '') + '了';
})（）
// 在pub被初始化后，无法拿到priv的闭包内部变量，但可以通过pub对象拿到。
```

**模块依赖规范**
* [CommonJS](https://zh.wikipedia.org/wiki/CommonJS)
* AMD（Asynchornous Module Definition: 异步模块定义）推崇依赖前置，提前执行
* CMD（Common Mondule Definition: 一般模块定义）推崇依赖就近，延迟执行
* UMD（Universal Module Definition: 通用模块定义）

node.js目前有两个模块系统：[CommonJS](https://nodejs.cn/api/modules.html)和[ECMAScript](https://nodejs.cn/api/esm.html)


### CommonJS

Node.js会将一下内容视为CommonJS模块
* 扩展名为.cjs的文件
* 最近的父package.json文件包含值为"commonjs"的顶层字段"type"时，则扩展名为.js的文件
* 扩展名不是 .mjs、.cjs、.json、.node 或 .js 的文件（当最近的父 package.json 文件包含值为 "module" 的顶层字段 "type" 时，这些文件将被识别为 CommonJS 模块，仅当它们被 required，不用作程序的命令行入口点）。

CommonJS模块本质上是一个module对象
```javascript
function Module(id = '', parent) {
  this.id = id;
  this.path = path.dirname(id);
  this.exports = {};
  this.parent = parent;
  updateChildren(parent, this, false);
  this.filename = null;
  this.loaded = false;
  this.children = [];
}
// 在加载模块的过程中会生成一个Module对象
```

**循环**
当有require()调用时，模块在返回时可能未完成执行。
```javascript
// a.js
console.log('a starting')
exports.done = false;
const b = require('./b');
console.log('in a, b.done = %j', b.done);
exports.done = true;
console.log('a done');

// b.js
console.log('b starting');
exports.done = false;
const a = require('./a');
console.log('in b, a.done = %j', a.done);
exports.done = true;
console.log('b done');

// main.js
console.log('main starting');
const a = require('./a');
const b = require('./b');
console.log('in main a.done = %j, b.done = %j', a.done, b.done);

// 执行node main.js结果
/*
main starting
a starting
b starting
in b, a.done = false
b done
in a, b.done = true
a done
in main a.done = true, b.done = true
*/
```

### ECMAScript

ES模块设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，已经输入和输出的变量。CommonJS模块就是对象，输入时必须查找对象属性。
ES模块不是对象，是通过import命令显示的输入代码，export命令显示的输出代码。

import的时候用户需要知道加载的变量名或函数名。export default命令为模块指定默认输出，其他模块在加载默认输出时，可以给默认输出对象指定任意名字。
export对外输出的必须是：函数（Function），类（class），var、let、const声明的变量。
```javascript

// export-default.js
export default function funcDefaukt() {
  console.log('default fuction');
}

export func1() {
  console.log('func1');
}

// test.js

import dfunc, {func1} from './export-default';
dfunc();
func1();
```

**循环加载**
ES6处理"循环加载"的时候与CommonJS有本质的不同。ES6模块是动态引用，如果使用import从一个模块中加载变量，变量不会被缓存，而是成为一个被加载模块的引用，需要保证取值的时候能够取到值。

```javascript
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar);
export let foo = 'foo';
// export function foo() {return 'foo'};


// b.mjs
import {foo} for './a';
console.log('b.mjs');
console.log(foo);
export let bar = 'bar';
// export function bar() {return 'bar'};

// 执行是会报错
/**
 * $ node --experimental-modules a.mjs
 * b.mjs
 * ReferenceError: foo is not defined
 */

// 解决方法是可以将foo和bar写成函数，函数具有提升的作用。

```

CommonJS与ES6差异：
 - CommonJS模块输出的是一个值的拷贝，ES6模块输出值的引用
 - CommonJS模块是运行期加载，ES6模块是编译时输出接口
 - CommonJS模块的require()是同步加载，ES6模块的import命令是异步加载，有一个独立的模块依赖的解析阶段。



