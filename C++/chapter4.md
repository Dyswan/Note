# C++ 表达式
<!-- TOC -->

- [C++ 表达式](#c-表达式)
    - [sizeof运算符](#sizeof运算符)
    - [命名的强制类型转换](#命名的强制类型转换)
    - [隐式类型转换](#隐式类型转换)

<!-- /TOC -->

---
## sizeof运算符
sizeof返回的是一个表达式或者一个类型名字所占的字节数。<br>
值得注意的是：

1. **sizeof并不会实际计算其运行对象的值。**因此，如果sizeof运算的对象是一个无效指针的解引用也是合法的。因为sizeof不需要解引用就可以知道解引用对象的类型。

2. sizeof运算符的结果部分地依赖于其作用的类型：<br>
- 对char或类型为char的表达式进行sizeof运算，结果为1<br>

- 对**引用类型**执行sizeof运算得到**被引用对象**所占空间的大小<br>

- 对**指针**执行sizeof运算得到**指针本身**所占空间的大小<br>

- 对**解引用指针**执行sizeof运算得到**指针指向的对象**所占空间的大小，注意，**指针不需有效**<br>

- 对**数组**执行sizeof运算得到**整个数组**所占空间的大小，注意，**sizeof运算不会把数组转换成指针处理**<br>

- 对**string对象或vector对象**执行sizeof运算得到**给类型固定部分**所占空间的大小，不会计算对象中的元素占用空间的大小<br>

---
## 命名的强制类型转换
命名的强制类型转换具有如下形式：<br>
*cast-name < type > ( expression )*<br>
其中type为目标类型，expression则是要转化的值，cast是static_cast,dynamic_cast,const_cast和reinterpret_cast中的一种。<br>

1. static_cast:<br>
任何具有明确定义的类型转换，只要不包含底层const，都可以使用static_cast。

2. const_cast:<br>
const_cast只能运算对象的底层const
    ```cpp
    const char *c;
    char *p = const_cast<char *>(c);
    ```

3. dynamic_cast<br>
将基类的指针或引用安全地转化成派生类的指针或引用。使用形式如下：
- dynamic_cast< *type\** >e
- dynamic_cast< *type&* >e
- dynamic_cast< *type&&* >e


4. reinterpret_cast
这个好像挺危险的，慎用。

---
## 隐式类型转换
- 数组转指针<br>

在大多数用到数组的表达式中，数组自动转换成指向数组首元素的指针。<br>但是，当数组被用作decltype关键字的参数，或用作取地址符（&）、sizeof以及typeid等运算符对象的运算对象时，上述转换不会发生。<br>并且，如果用引用来初始化数组上述转换也不会发生。
```cpp
int a[10];
int (*c)[10] = &a; // 用作取地址符&的运算对象
int (&b)[10] = a; // 用引用初始化数组
```

- 指针的转换<br>

0或字面值nullptr能转换成任意指针类型，指向任意对象的指针能转换成const void*。

- 转换成常量<br>

允许将指向非常量类型的指针转换成相同的常量类型的指针，对于引用也是这样。
```cpp
int i=1;
const int &x = i;
const int *y = &i;
```

- 类类型定义的转换<br>
类类型能定义由编译器自动执行的转换，不过编译器每次只执行一种类类型转换。
详细，见c++对象