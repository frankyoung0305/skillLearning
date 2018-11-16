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
