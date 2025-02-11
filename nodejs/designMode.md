# 设计模式

**体系架构**

![node.js](https://github.com/BigbangBang/learningNotes/blob/main/picture/node.js.jpeg)


## Node.js文件模块
Node.js文件模块提供了包括异步、同步、Promise化、流式API和文件监听功能。

### 同步API
直接在主线程中调用操作系统的提供的接口，它会导致主线程阻塞。

### 异步API
异步API依赖于Libuv的线程池，Node.js把任务放到线程池然后返回主线程执行其他事务，等到条件满足时线程池通知主线程，后者执行回调。

### Promise化
在Node.js的文件模块中提供了Promise化的接口，可以使用await进行文件操作。
```javascript
const {open} = require('fs').promises;

async function read(filePath) {
    let fileHandler;
    try {
        fileHandler = await open(filePath, 'r');
        console.log('read result:', await fileHandler.readFile({encoding: 'utf-8'}));
    } catch (err) {
        console.error('read error:', err);
    }
}

read('./hello.txt');
```

### 流式API
文件同步、异步、Promise化API都是一次性操作完成。
流式API可以部分读取文件，如果文件较大的情况下可以减少内存的使用。


### 文件监听
Node.js实现方式:
1. 定时轮询文件元信息是否发生变化。
2. 通过订阅/发布机制订阅事件，当文件发生变化时主动通知。

文件监听机制基于inotify(Linux)

## Unix域套接字
Unix域套接字不仅支持传递不同数据，还可以传递文件描述符。

## 进程模块

### 创建子进程
Node.js提供了同步、异步两类创建进程的API，底层实现是相同的，区别主要是同步方式会等待子进程执行完成父进程才能继续执行。异步方式则是父子进程的执行是独立的。

**异步创建进程**


**同步创建进程**