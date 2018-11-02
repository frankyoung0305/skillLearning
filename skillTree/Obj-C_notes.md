#Obj-C Notes
#trivals
- @class; 避免循环引用，表示引入了一个类。see: https://www.jianshu.com/p/4f5081ef9eb3
- (instancetype); instancetype的作用，就是使那些非关联返回类型的方法返回所在类的类型！（相比id类型）
相同点：都可以作为方法的返回类型。不同点：①instancetype可以返回和方法所在类相同类型的对象，id只能返回未知类型的对象；②instancetype只能作为返回值，不能像id那样作为参数。
- Block；闭包 = 一个函数「或指向函数的指针」+ 该函数执行的外部的上下文变量「也就是自由变量」；Block 是 Objective-C 对于闭包的实现。block会截获局部变量的值，复制到自身数据结构中，默认不能修改局部变量的值；使用__block修饰的局部变量，block通过复制其引用地址实现访问，可以修改外部变量的值。see: https://juejin.im/entry/588075132f301e00697f18e0
