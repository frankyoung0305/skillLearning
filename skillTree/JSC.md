> JavaScriptCore notes
JavaScript引擎是专门处理JavaScript脚本的虚拟机
# JavaScriptCore组成
JavaScriptCore主要由以下模块组成：
- Lexer 词法分析器，将脚本源码分解成一系列的Token
- Parser 语法分析器，处理Token并生成相应的语法树
- LLInt 低级解释器，执行Parser生成的二进制代码
- Baseline JIT 基线JIT（just in time 实施编译）
- DFG 低延迟优化的JIT
- FTL 高通量优化的JIT
see: https://trac.webkit.org/wiki/JavaScriptCore

# JavaScriptCore
JavaScriptCore是一个C++实现的开源项目。使用Apple提供的JavaScriptCore框架，你可以在Objective-C或者基于C的程序中执行Javascript代码，也可以向JavaScript环境中插入一些自定义的对象。
```
#ifndef JavaScriptCore_h
#define JavaScriptCore_h

#include <JavaScriptCore/JavaScript.h>
#include <JavaScriptCore/JSStringRefCF.h>

#if defined(__OBJC__) && JSC_OBJC_API_ENABLED

#import "JSContext.h"
#import "JSValue.h"
#import "JSManagedValue.h"
#import "JSVirtualMachine.h"
#import "JSExport.h"

#endif

#endif /* JavaScriptCore_h */
```
## JSC的主要几个类
JavaScriptCore的主要几个类：
- JSContext
- JSValue
- JSManagedValue
- JSVirtualMachine
- JSExport

## Hello World
```
//创建虚拟机
JSVirtualMachine *vm = [[JSVirtualMachine alloc] init];

//创建上下文
JSContext *context = [[JSContext alloc] initWithVirtualMachine:vm];

//执行JavaScript代码并获取返回值
JSValue *value = [context evaluateScript:@"1+2*3"];

//转换成OC数据并打印
NSLog(@"value = %d", [value toInt32]);

Output

value = 7
```
