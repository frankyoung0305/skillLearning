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

### JSExport
一种需要你实现的协议，继承自<JSExport>，用于将OBJC类，其实例方法，其类方法，以及其属性暴露给JS代码。
    
被注入JS的类其实是一个实现了此协议的类，被注入的JS的实例是一个实现了上述类的实例；
    
OC实例方法->JS方法
OC构造函数—>JS构造器
OC property->JS 属性访问器
OC类方法->JS构造器的方法

从一个OBJC类的实例创建一个JS对象，且这个JSValue没有明确拷贝转换方式时，JSC会创建一个JS wrapper对象。（对于特定类型，JSC会自动将值拷贝到对应的JS类型中；比如NSString实例会变成JS string）

JS里继承是以原型链的形式存在的。对于每一个想要暴露的OBJC对象，JSC会在其所在的 中创建一个prototype。

对于每一个导出的实例方法，JavaScriptCore都会在prototype中创建一个存取器属性。对于每一个导出的类方法，JavaScriptCore会在constructor对象中创建一个对应的JavaScript function。

在JS中调用native代码，native代码的注入可以用Block或JSExport两种方式；

OC中的seletor会自动转换为JS中的方法名字，使用驼峰命名规则，也可以自定义。

### 内存管理
- 每个JSValue对象会持有一个JSContext对象的强引用，只要至少有一个与JSContext关联的JSValue背持有，JSContext就会一直存活。
- 导出的native对象被JS的全局对象所持有。
- JSContext对象引用了JS执行环境中的全局对象。
- 全局对象的某个属性对应了一个导出的native对象。

- 所以：将要导出的native函数或block不可以直接引用JSContext以及JSValue。 否则会造成循环引用，导致JSContext无法被释放。
- 使用[JSContext currentContext]来获取当前context
- 使用JSMangedValue来替代JSvalue

### JSManagedValue
一个JSManagedValue包装了一个JSValue对象，并增加了“有条件的保留”特性以实现值的自动内存管理。主要用于在一个暴露到JS的native对象中存储一个JS的值。

JSManagedValue的“有条件的保留”特性确保了其中的JSValue对象在以下情况满足时被保留：（否则JS中的对应值会变成nil，JSValue对象也会被释放
- 此JS值可以通过JS对象图（object graph）被访问（也就是说，不属于JS垃圾回收[GC]的部分）。
- 此JSManagedValue对象可以通过OC的对象图被访问，需要使用JSVM的实例方法addManagedReference(_:withOwner:)来将本JSMValue加入到JSVM实例中


### 异常处理
JSContext的exceptionHandler属性可用来接收JavaScript中抛出的异常，默认的exceptionHandler会将exception设置给context的exception属性。
因此，默认的表现就是从JavaScript中抛给native的未处理的异常又被抛回到JavaScript中，异常并未被捕获处理。

将context.exception设置为nil将会导致JavaScript认为异常已经被捕获处理。


# JavaScriptCore C APIs
see: https://juejin.im/post/5b3b210df265da0fb01846a0

JSC C API包含六个类：
- JSBase.h  
JavaScriptCore 的接口文件，这个类中 import 了其他的类，简单封装了其他的 C API。
- JSContextRef.h  
JSContextRef 相当于 Objective-C 中的 JSContext，主要提供 JS 执行的上下文环境。
- JSObjectRef.h  
JSObjectRef 相当于 Objective-C 中的 JSObject，它代表一个JavaScript对象，交互的核心放在都在这个类中实现。
- JSStringRef.h  
是 JavaScript 中基本的字符串表示形式。
- JSStringRefCF.h  
包含 CFString 便利的方法。
- JSValueRef.h  
JSValueRef 相当于 Objective-C 中的 JSValue ，对应一个 JavaScript 的值，它是所有JavaScript值的基本类型

## JSBase.h
三个方法：检查脚本的语法错误，执行js脚本，执行GC  数据类型：JSClassRef...

## JSContextRef.h
主要提供 JS 执行所需所有资源和环境

获取某个context的全局对象  JSContextGroup的管理  JSGlobalContext的管理

contextGroup类似虚拟机，但是没有自动GC  globalContext类似JSContext

## JSObjectRef.h
JS对象，提供两部分API：创建JS对象 + 为创建的JS对象添加Callback
- 创建/保持/销毁 JS类
- 创建JS对象、数组、构造器、日期、错误、方法、正则
- 将对象作为构造器进行调用，将对象作为方法进行调用
- 获取对象的可枚举属性，删除对象的属性
- 对对象的private，property, prototype进行操作
- 对对象的属性名进行操作
- JS对象条件判断：是否有属性、是否是方法/构造器

- 对象被初始化时的回调
- 对象作为构造器被调用时执行的回调（new xxx）
- 对象作为方法被调用时执行的回调
- 对象被转换为某种特定JS类型时的回调
- 删除对象属性时的回调
- 被GC之前的回调（finalized
- 获取属性/属性名时的回调
- 设置对象属性时的回调
- 作为“instanceof”参数时的回调，是否有实例
- 是否有属性回调

## JSValueRef.h
一个 JavaScript 值，提供用Object-C的基础数据类型来创建 JS 的值，或者将JS 的值转变为OC的基础数据类型

- 获取JS值的类型，返回一个枚举JSType

- native值创建JS的值

- JS值转变为native值

= 保护JS值不被GC，将这个JS值作为全局变量或是存在堆上。

- 判断JS值是否是某种类型

## JSString.h
JavaScript 对象中字符串对象，公开的api包括如下

- 从Unicode字符数组创建
- 从UTF8数组创建
- 获取JS string的后台存储的内存指针，这个buffer存有Unicode字符
- 获取长度，获取JS string转换成UTF8 string可能占据的最大字节数
- JS字符串转换成UTF8字符串，并把结果拷贝到其他的buffer里
- 判断字符串是否相等

## JSStringRefCF.h
CFString 与 JavaScript string 相互转化（core foundation）

