#Javascript 笔记
1. 原型继承原型链
- 构造器和原型互相指向，原型中存放类的公有方法，一般不会通过实例来显式的访问原型。
-  JavaScript属性设置与检测：设置一个对象的属性会只会修改或新增其自有属性，不会改变其继承的同名属性；调用一个对象的属性会依次检索本身及其继承的属性，直到检测到
- 继承关系由原型链体现：构造器.prototype.__prototype__的链式结构
- 记住两点：1、所有的构造器的constructor都指向Function。2、Function的prototype指向一个特殊匿名函数，而这个特殊匿名函数的__proto__指向Object.
- Function.prototype === Function.__proto__
- Object.prototype.__proto__ === null 
- Function 是自己的实例：Function.__proto__.constructor === Function.prototype.constructor === FunctionOb

2. 关于对象属性
- 分为两种：数据属性和访问器属性。
- 访问器属性被访问时会调用设置好的getter，被设置时会调用设置好的setter
- 访问器属性没有vale，数据属性有value
- 将方法设置为一个访问器属性，这个方法不会有prototype，但是还是Function的实例.
- (另：箭头函数“ ()=>{} ”也没有prototype，但是也是Function的实例) 都可以少创建一个prototype，一般的Function都有prototype，只有有prototype的Function才能被用做Constructor？
- 添加属性的另一种写法：
```JS
var export = { //.....// };
var module = { export };
// module.export === export; //true
```

3. 关于逗号表达式：
可以让方法调用时的this变为全局对象：
```JS
(0, defines[path])(function (sub_path) {
            //....
            //....
        }, module, exports);
```
方法执行时，this指向的是全局对象

defines[path]会返回一个function，如果直接调用：
```JS
defines[path](function (sub_path) {
            //....
            //....
        }, module, exports);
```
则方法执行时传入的this指向的是defines对象。

4. 位运算符
- 所有 JavaScript 数字存储为根为10的64（8比特）浮点数。JavaScript不是类型语言。与许多其他编程语言不同，JavaScript不定义不同类型的数字，比如整数、短、长、浮点等等。
- 在JavaScript内部，数值都是以64位浮点数的形式储存，但是做位运算的时候，是以32位带符号的整数进行运算的，并且返回值也是一个32位带符号的整数。
- 可以用 ”与0按位或“ 的方式操作一个整数，确定会返回32位整形，好处是确定类型，JIT优化效率高，inline cache命中效率高，执行更快。
- 另：尽量不要更改变量存储的数据类型，更改的开销太大。

5. 关于大小端内存问题
- 主机字节序：小端（内存顺序与逻辑相反）。比如：int_32 格式的25535（十六进制63BF）在内存中存在的形式是 BF630000...（地址由低到高增长）。
- 网络字节序：大端（网络传输用）。与小端相反，与逻辑相同。
- 左移/右移的位操作，说的不是内存高低，只与逻辑上的相同：0xff00的内存分布是00ff0000, 将其右移8位后，变为0xff，内存分布是ff000000，左移8位后变为0xff0000，内存分布是0000ff00。（其实左右移在小端机上也是反的）右移时左边空位填充符号位，全f的int32（ffffffff）代表-1.
- 十六进制1位等于二进制4位，内存最小单位是一字节8bit，两位十六进制。
