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
- __blank___

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
 
