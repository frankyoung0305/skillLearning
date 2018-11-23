# UIKit
UIKit架构为iOS/tvOS提供底层基础依赖（required infrastructure）。它提供window和view的结构以实现UI，
提供事件处理机制以向app分发如多点触控这样的输入，
提供main run loop来管理用户系统和app之间的交互。
并支持动画支持，文件管理，绘制和打印，等等

注：除非特殊要求，仅在app的主线程或主分发队列使用UIKit类。这个限制特别针对继承自UIResponder的类或与操作app的UI相关的类。



