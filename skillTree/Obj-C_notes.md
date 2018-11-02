#Obj-C Notes
#trivals
- @class; 避免循环引用，表示引入了一个类。see: https://www.jianshu.com/p/4f5081ef9eb3
- (instancetype); instancetype的作用，就是使那些非关联返回类型的方法返回所在类的类型！（相比id类型）
相同点：都可以作为方法的返回类型。不同点：①instancetype可以返回和方法所在类相同类型的对象，id只能返回未知类型的对象；②instancetype只能作为返回值，不能像id那样作为参数。
