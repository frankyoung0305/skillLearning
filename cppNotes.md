# const类成员函数
1. 要声明一个const类型的类成员函数，只需要在成员函数参数列表后加上关键字const，如：
  - char get() const;  
2. 在类体之外定义const成员函数时，还必须加上const关键字，
  - char Screen::get() const {  
       return _screen[_cursor];  
    }  
3. 若将成员成员函数声明为const，则该函数不允许修改类的数据成员。
4. 小结：
  - const成员函数可以访问非const对象的非const数据成员、const数据成员，也可以访问const对象内的所有数据成员；（确认不会更改）
  - 非const成员函数可以访问非const对象的非const数据成员、const数据成员，但不可以访问const对象的任意数据成员；（！）
  - 作为一种良好的编程风格，在声明一个成员函数时，若该成员函数并不对数据成员进行修改操作，应尽可能将该成员函数声明为const 成员函数。
