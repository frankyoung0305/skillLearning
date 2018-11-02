# JavaScriptCore note
JavaScript引擎是专门处理JavaScript脚本的虚拟机

see：https://cloud.tencent.com/developer/article/1004875 ， https://cloud.tencent.com/developer/article/1004876
## JavaScriptCore组成
JavaScriptCore主要由以下模块组成：
- Lexer 词法分析器，将脚本源码分解成一系列的Token
- Parser 语法分析器，处理Token并生成相应的语法树
- LLInt 低级解释器，执行Parser生成的二进制代码
- Baseline JIT 基线JIT（just in time 实施编译）
- DFG 低延迟优化的JIT
- FTL 高通量优化的JIT

see: https://trac.webkit.org/wiki/JavaScriptCore

## JavaScriptCore
JavaScriptCore是一个C++实现的开源项目。使用Apple提供的JavaScriptCore框架，你可以在Objective-C或者基于C的程序中执行Javascript代码，也可以向JavaScript环境中插入一些自定义的对象。
```javascript
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
### JSC的主要几个类
JavaScriptCore的主要几个类：
- JSContext
- JSValue
- JSManagedValue
- JSVirtualMachine
- JSExport

### Hello World
```objc
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
### JSVirtualMachine
一个JSVirtualMachine的实例就是一个完整独立的JavaScript的执行环境，为JavaScript的执行提供底层资源。

这个类主要用来做两件事情：

- 实现并发的JavaScript执行
- JavaScript和Objective-C桥接对象的内存管理

每个JSContext对象都属于一个虚拟机，每个虚拟机可以包含多个context，并且允许在这些context之间传递值（JSValue对象), 每个虚拟机都是完整且独立的，有独立堆空间和GC，GC不能处理其他VM上的对象，所以不能在虚机之间传值。
JSC API都是线程安全的，可以在任意线程创建JSValue或执行JS代码，其他使用本VM的线程都要等待。

### JSContext
一个JSContext对象代表一个JavaScript执行环境。
在objc/swift代码中，创建并使用JSContext去执行JS代码，访问JS中定义或者计算的值，
并使JavaScript可以访问native的对象、方法、函数。

#### JSContext执行JS代码
- 调用evaluateScript函数可以执行一段top-level 的JS代码，并可向global对象添加函数和对象定义
- 其返回值是JavaScript代码中最后一个生成的值
```objc
/* 执行一段JS代码，返回最后生成的一个值 */
(JSValue *)evaluateScript:(NSString *)script;

/* 获取当前执行的JavaScript代码的context */
+ (JSContext *)currentContext;

/* 获取当前执行的JavaScript function*/
+ (JSValue *)currentCallee NS_AVAILABLE(10_10, 8_0);

/* 获取当前执行的JavaScript代码的this */
+ (JSValue *)currentThis;

/* Returns the arguments to the current native callback from JavaScript code.*/
+ (NSArray *)currentArguments;

@property (readonly, strong) JSValue *globalObject;

```

#### JSContext访问JS对象
一个JSContext对象对应了一个全局对象（global object），全局变量是全局对象的属性，可以通过JSValue对象或者context下标的方式来访问。
```objc
JSValue *value = [context evaluateScript:@"var a = 1+2*3;"];

NSLog(@"a = %@", [context objectForKeyedSubscript:@"a"]);  //使用context的实例方法objectForKeyedSubscript
NSLog(@"a = %@", [context.globalObject objectForKeyedSubscript:@"a"]);  //使用context.globalObject的objectForKeyedSubscript实例方法
NSLog(@"a = %@", context[@"a"]);  //通过context的下标

///------///
Output:

a = 7
a = 7
a = 7
```
通过设置JSContext，来在执行上下文中注入native的对象或方法。
```objc
/* 例如：以下代码在JavaScript中创建了一个实现是Objective-C block的function */
context[@"makeNSColor"] = ^(NSDictionary *rgb){
    float r = [rgb[@"red"] floatValue];
    float g = [rgb[@"green"] floatValue];
    float b = [rgb[@"blue"] floatValue];
    return [NSColor colorWithRed:(r / 255.f) green:(g / 255.f) blue:(b / 255.f)         alpha:1.0];
};
JSValue *value = [context evaluateScript:@"makeNSColor({red:12, green:23, blue:67})"];
```

### JSValue
一个JSValue实例是一个JavaScript值的引用，使用JSValue类在JavaScript和native代码之间转换一些基本类型的数据（比如数值和字符串）。你也可以使用这个类去创建包装了自定义类的native对象的JavaScript对象，或者创建由native方法或者block实现的JavaScript函数。(用来创建JS对象和函数)

每个JSValue实例都关联一个JSContext，这个JSContext代表了包含这个JS值的JS执行环境。JSValue持有一个context的强引用，只要有一个值与本context关联，这个上下文就会一直存活。通过调用JSValue的实例方法返回的其他的JSValue对象都属于与最始的JSValue相同的JSContext。

每个JSValue通过context间接关联了一个JSVM对象，可以把JSValue传给相同JSVM管理下的JSValue或JSContext的实例方法。跨JSVM的传递会导致OBJC异常。

Block/函数 和 JS Function之间的转换：
- OBJC的block/函数可以转换为JS中的function对象；
- 这样的JS function可以转换回native block/函数；
- 其他的JS function会被转换为一个空的dictionary（因为JS function 也是一个函数）

OBJC对象 和 JS对象间的转换：
- 对于其他的native对象（除NSDictionary，NSArray，Blocks），JavaScriptCore都会创建一个拥有constructor原型链的wrapper对象，这个对象反映了native类型的继承关系。默认情况下，这个JS wrapper对象不会拥有对应native对象的属性和方法，需要通过JSExport来把属性和方法暴露给JS。
