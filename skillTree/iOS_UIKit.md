# UIKit
UIKit架构为iOS/tvOS提供底层基础依赖（required infrastructure）。它提供window和view的结构以实现UI，
提供事件处理机制以向app分发如多点触控这样的输入，
提供main run loop来管理用户系统和app之间的交互。
并支持动画支持，文件管理，绘制和打印，等等

注：除非特殊要求，仅在app的主线程或主分发队列使用UIKit类。这个限制特别针对继承自UIResponder的类或与操作app的UI相关的类。

##关于UIKit开发
https://developer.apple.com/documentation/uikit/about_app_development_with_uikit

###总览
在build app时，Xcode会编译源码文件并为项目创建一个app bundle。app bundle是一个结构化的目录，包含了与app有关的代码和资源文件。资源文件包括image assets，storyboatd文件，字符串文件，以及app元数据。

###所需资源
- App图标
- 应用启动屏Storyboard

当用户点击app图标，系统会马上显示启动屏SB，app准备好后会显示app实际的UI。

###所需元数据
系统从app bundle里的Info.plist文件（information property list）里读取app的配置和能力信息。需要自行添加信息以适应app的需求。
最常见的是修改Info.plist以调整app的软硬件需求。

###UIKit app的代码结构
UIKit提供了很多app需要的核心对象，包括与系统交互的对象，运行app主事件循环的对象，将内容现实到屏幕的对象。大多数情况下我们不会改动这些对象，或只做很小的修改。重要的是知道修改那个对象，何时修改它们。

UIKit app的结构遵循MVC设计模式，对象依据他们的功用来区分。Model对象管理app的数据与业务逻辑。View对象提供数据的显示。Controller对象作为model和view的桥梁，适时的在二者之间传递数据。

你需要提供model对象来存储app的数据结构。UIKit提供了大部分的view对象，你也可以按需添加自定义view对象。viewController和appDelegate对象会协调数据对象和UIKit view之间的数据交互。

![avatar](https://docs-assets.developer.apple.com/published/4e7c26b6ad/ff7aa08f-4857-44ce-88d5-7dacbef84509.png)

UIKit和Foundation架构提供了很多用来创建app model对象的基础类型。UIKit提供UIDocument对象来组织硬盘上文件的数据结构。Foundation框架提供了基础数据结构包括字符串，数字，数组等。

UIKit提供了大部分的controller和view层的对象。尤其是定义了UIView类，负责将内容显示在屏幕上。（也可使用Mental或其他框架进行内容的渲染。）UIApplocation对象执行app的主事件循环（main event loop）并管理app的整个生命循环。

##App 结构
UIKit管理app与系统之间的交互，并提供有助管理app数据和资源的类。
###Core App
https://developer.apple.com/documentation/uikit/core_app

管理app的数据model与其和系统的交互。




