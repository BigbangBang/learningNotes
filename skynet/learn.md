## 目录结构



* 3rd：提供lua语言支持，jemalloc(内存管理)，md5库，lua等。(.c)
* skynet-src：包含skynet最核心的模块，包含逻辑入口，加载C服务代码的skynet_module模块、运行和管理服务实例的skynet_context模块、skkynet消息队列、定时器和sockket模块等。(.c)  
* service-src：依附于skynet核心模块的C服务，用于日志输出的logger服务，运行lua脚本snlua的C服务等。（.c）
* lualib-src目录：提供C层级的api调用，如调用socket模块的api，调用skyent消息发送，注册回调函数的api，对C服务的调用，并导出lua接口，供lua层使用。(.c)
* servce：lua层服务，依附于snlua这个C服务，包含skynet lua层级的一些基本服务，如启动lua层级服务的bootstrap服务，gate服务，供lua层创建新服务的lacuncher服务等。（.lua）
lualib：包含调用lua服务的辅助函数，方便应用层调用skynet的基本服务；包含对C模块或者lua模块调用的辅助函数。（.lua）


### Malloc
skynet在可以使用jemalloc的平台，hook了原有malloc函数的实现。这种实现时为了灵活切换内存分配的实现，保证接口的一致性。

```c
// skynet_malloc.h
#ifndef skynet_malloc_h
#define skynet_malloc_h

#include <stddef.h>

#define skynet_malloc malloc
//...

void * skynet_malloc(size_t sz);
//...

#endif
```

```c
// skynet.h
#include "skynet_malloc.h"

// malloc_hook.c
#include skynet.h

#ifndef NOUSE_JEMALLOC
#include "jemalloc.h"

// hook : malloc, realloc, free, calloc

void *
skynet_malloc(size_t size) {
	void* ptr = je_malloc(size + PREFIX_SIZE);
	if(!ptr) malloc_oom(size);
	return fill_prefix(ptr, size, PREFIX_SIZE);
}
```

同时存在宏定义 #define skynet_malloc malloc，和函数声明 void * skynet_malloc(size_t sz)。
当同时存在宏定义和函数声明/定义时，编译器会优先使用函数。
函数声明/定义会"屏蔽"同名的宏定义，这是因为函数是实际的符号，而宏只是文本替换。
所以在使用 jemalloc 时：
宏定义 #define skynet_malloc malloc 实际上不会生效，系统会使用 malloc_hook.c 中的函数实现，这个宏定义实际上是为了 NOUSE_JEMALLOC 情况下使用的。
