> for C++ Primer 5
# Part 1
## Chap 1 ~ 7
### chap1
1. iostream cin cout cerr clog (p5
- cin >> v1;
- cout << v1;
- std::endl 操纵符，结束当前行，并将与设备关联的缓冲区中的内容刷到设备中。

2. 读入数量不定的数据 (p13
- while(std::cin >> value)
- 持续获取直到遇到文件结束符或出错

3. include指令  （p19
- 包含标准库时使用 < >
- 包含其他库使用 “ ”

4. 文件重定向  （p19
- __blank__

### chap2
- python, smalltalk 运行时检查数据类型 （动态
- c++编译时检查数据类型 （静态数据类型语言

1. 内置类型选择 （p32
- 明确知道不为负，使用unsigned；
- 超过int 选long long
- 算数表达式尽量不选用char或bool
- 浮点运算选用double

2. 类型转换 （p35
- 切勿混用 带符号类型 与 无符号类型

3. 字面值常量 （p35
- 20 十进制
- 024 八进制
- 0x14 十六进制

4. 转义字符 与 指定字面值类型 （p37

5. __列表初始化__ （p39
- 使用花括号来赋初始值
- 使用这种方法时，当存在丢失信息风险会报错

6. 声明与定义的关系
- 分离式编译：将程序分割为多个文件，每个文件可以被独立编译

- 声明：使得 名字 为程序所知
- extern int i;  
- 一旦显示初始化，声明即为定义： 
- extern double pi = 3.1416;
- 函数体内部，不能初始化一个extern关键字的变量 （发生重复定义

- 定义：创建与名字关联的实体
- int j;

7. 标识符 （p42
- 必须以字母或下划线开头
- 不能连续两个下划线，不能下划线+大写字母开头，函数体外标识符不能以下划线开头；

- 规范
- 变量用小写
- 自定义类名用大写开头
- 匈牙利命名法

8. 作用域
- { }花括号
- 作用域嵌套：内层作用域里可使用同名局部变量覆盖全局变量（不推荐

9. 复合类型 （p45
- 基于其他类型的定义的类型（指针与引用） 基本数据类型 + 声明符列表， 每个声明符命名变量并指定相关类型

 9.1. __引用__（左值引用）
 - int &ref = val;  //引用即别名
 - 引用本身不是对象， 不能定义引用的引用
 
 9.2. __指针__
 - 指针是一个对象，允许赋值与拷贝，
 - 指针类型要匹配
 - 预处理器（p49） NULL是预处理变量
 - _void *指针_ ：可用于存放任意对象的值，存放一个地址，但不能直接操作所指对象。
 
 9.3. _指向指针的指针 指向指针的引用_
 - int **ppi = &pi;
 - int i = 43;
 - int *p;
 - int *&r = p;  //r是一个对指针p的引用！
 - __注意！要理解r的类型，从右向左阅读r的定义。离变量名最近的符号对变量名有最直接的影响：&---r是一个引用，*---r引用的是指针，int---r引用的是int类型的指针。__
 
10. const限定符
- 为了防止对变量的更改。
- __必须初始化__   默认状态下，const对象仅在文件内有效，想在文件之间共享const对象，必须在前面添加extern关键字
 10.1. const的引用（常量引用）
 - const int ci = 1024;
 - const int &r1 = ci;  //保持常量的属性：不许赋值
 
 __初始化与const的引用（p55__
 - int i = 42;
 - const int &r1 = i; //正确的，常量引用可以绑定普通对象
 - __const int &r2 = 42 //正确的，可以绑定字面常量！！！！但是普通的引用不可以绑定字面常量__
 - __const int &r3 = r1 * 2; //正确的，可以绑定一般表达式！！！__
 
 - __常量引用的绑定原理__
 - 当发现绑定需要进行类型转换时，编译器生成临时量，让常量引用绑定 __临时量对象__ 。
 
 10.2 指针与const
 - 指向常量的指针：（pointer to const）
 - const double pi = 3.1416;  //const pi
 - const double *cptr = &pi; //指向常量的指针cptr，不允许通过此指针修改常量的值，也可以指向非常量对象
 
 - const指针(常量指针)，指针本身是常量，指针本身的值不变，指向不改变
 - int errNumb = 0;
 - int *const curErr = & errNumb; //curErr将一直指向errNumb
 - __技巧，从右向左阅读，离得最近的是const---本身是常量对象，*---是一个常量指针，int---指向int对象__
 
 10.3. 顶层 const
 - 顶层const： 指针本身是常量， 或任意对象是常量
 - 底层const： 指针对象是常量
 - const int &r = ci; //用于声明引用的const都是底层const
 
 10.4. constexpr 常量表达式
 - 值不会改变，在编译过程中就能得到计算结果的表达式。
 - const int i = 20;  //const val
 - const int constexp = i + 1; //const expr 用常量初始化的const对象
 - const int sz = get_size();  //不是常量表达式，只有在运行时才能知道结果
 - __constexpr变量__
 - 将变量声明为constexpr来验证变量的值是否为常量表达式 1）变量一定是常量 2）必须用常量初始化（或使用constexpr函数初始化）
 - （p59）字面值常量
 - constexpr int *q = nullptr;  //q是常量指针！ constexpr是顶层const 指向不改变（p60）
 
 11. 处理类型
 - typedef double wages; //wages 是 double的同义词
 - typedef wages base, *p; //base 是double的同义词， p是double*的同义词
 
 - using SI = Sales_item; //SI是Sales_item的同义词
 -__(p61) 别名指代复合类型，特别注意__
 
 - auto类型声明 
 - 一条语句只能有一个数据类型
 - const保留的性质（p62）
 - decltype 类型指示符，编译器分析类型，并不实际调用（p62）
 - decltype(f()) sum = x;   
 - __decltype()对引用的返回值记得带上引用__ (p63) 解引用操作得到引用类型，双层括号得到引用
 
 12. struct
 - struct定义最后加上分号，因为类体后面可以紧跟变量名定义对象
 
 - 一般在头文件中定义类，不要在函数体里定义类
 - 头文件中包含只能被定义一次的实体：类  const  constexpr等
 - 使用头文件保护符防止重复包含：
 - #ifndef XXX_XXX
 - #define XXX_XXX     <-预处理变量 大写 唯一
 - /*..........*/
 - #endif
 
 ###chap3
 1.using
 - using std::cin;  //using声明，可以直接使用名字cin
 - 头文件中的代码一般不适用using，重复拷贝后，可能产生名字冲突
 
 - string
 - #include <string>
 - using std::string;
 
 - string s4(n, 'c'); //s4初始化为连续n个c的字符串
 
 
 
 
 



 
