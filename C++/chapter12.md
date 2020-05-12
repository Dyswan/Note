# C++ 动态内存
<!-- TOC -->

- [C++ 动态内存](#c-动态内存)
    - [直接管理内存](#直接管理内存)
        - [使用new动态分配](#使用new动态分配)
        - [内存耗尽](#内存耗尽)
        - [使用delete释放内存](#使用delete释放内存)
    - [智能指针](#智能指针)
    - [share_ptr类](#share_ptr类)
        - [share_ptr的赋值和拷贝](#share_ptr的赋值和拷贝)
    - [unique_ptr类](#unique_ptr类)
        - [传递unique_ptr参数和返回unique_ptr](#传递unique_ptr参数和返回unique_ptr)
        - [向unique_ptr传递删除器](#向unique_ptr传递删除器)
        - [weak_ptr](#weak_ptr)
    - [智能指针与动态数组](#智能指针与动态数组)
    - [allocator类](#allocator类)

<!-- /TOC -->
---
## 直接管理内存
在C++中，动态内存的管理是通过**new**和**delete**这两个运算符来完成的。

### 使用new动态分配
在自由分配空间的内存是无名的，因此new无法为其分配的对象命名，而是返回一个指向该对象的指针。
在默认情况下，动态分配的对象是默认初始化的。但我们也可以使用直接初始化方式和传统的构造方式，也可以使用列表初始化的方式进行初始化。只需要在类型名后面该干嘛干嘛就行了。

### 内存耗尽
自由空间是有限的，一旦一个程序用光了它所有可用的内存，new表达式就会失败。默认情况下，new不能分配所要求的内存，就会抛出一个**bad_alloc**的异常。
我们可以用一种叫**定位new**的操作来阻止它抛出异常
```cpp
int *x = new int;//如果分配失败，抛出bad_alloc异常
int *y = new (nothrow) int; //如果分配失败，返回空指针
```

### 使用delete释放内存
delete表达式通过接受一个指针（指向const的也可以），释放其指向的对象，将动态内存还给系统。要注意的是，我们**传递给delete的指针必须指向动态分配的内存，或是一个空指针。**<br>
通常情况下，编译器不会分辨一个指针指向内存是否已经释放，所以有些delete语句编译器不会报错，尽管它们的错的。

---
## 智能指针
在C++中，动态内存的管理是通过**new**和**delete**这两个运算符来完成的。但动态内存的使用很容易出问题，因为确保在正确的时间释放内存是十分困难的。因此，为了更容易也更安全的使用动态内存，新标准的C++提供了两种智能指针类型来管理动态对象。<br>

**智能指针与常规指针重要的区别在于它负责自动释放所指向的对象**。<br>

刚刚提到新标准的C++提供了两种智能指针类型，这两个指针类型分别是**share_ptr**，**unique_ptr**。这两种智能指针的区别在于管理底层指针的方式，**shate_ptr**允许多个指针指向同一个对象，**unique_ptr**则只能“独占”所指对象。

以下是智能指针的都支持的操作：

![1](https://img-blog.csdnimg.cn/20200302164722384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
---
## share_ptr类
share_ptr特有的操作：
![2](https://img-blog.csdnimg.cn/2020030216494133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
![3](https://img-blog.csdnimg.cn/20200302210842370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
![4](https://img-blog.csdnimg.cn/20200302210934514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

### share_ptr的赋值和拷贝
由于share_ptr允许多个指针同时指向一个对象，因此每一个share_ptr都有一个关联的计数器，通常称为**引用计数**，用来记录有多少个其他的share_ptr指向相同的对象。
而当share_ptr被赋予了一个新值，或share_ptr被销毁（例如离开局部作用域），share_ptr的析构函数会递减其指向对象的引用计数器。当计数器减至零时，析构函数就会销毁对象，并释放它占用的内存。

---
## unique_ptr类
![5](https://img-blog.csdnimg.cn/20200302211240739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
    
由于unique_ptr独占它所指向的对象，因此，**unique_ptr并不支持拷贝或赋值操作**。虽然是这样，但是我们可以通过release或reset将指针的所有权从一个（非const）unique_ptr转移给另一个unique。
```cpp
unique_ptr<string> p1 (new string("xyz"));
p1.release();//错误，p1不会释放内存，并且会丢失指针
unique_ptr<string> p2(p1.release());//p1置为空并将原来指向的对象转移给p2
unique_ptr<string> p3 (new string("123"));
p2.reset(p3.release());//将p3转移给p2,并把p2原来指向的对象释放
```

### 传递unique_ptr参数和返回unique_ptr
虽然unique_ptr不支持拷贝，但是存在例外的情况：我们可以拷贝或赋值一个将要被销毁的unique_ptr。

### 向unique_ptr传递删除器
我们可以像shared_ptr一样重载删除器。但是重载删除器会影响unique_ptr类型以及如何构造(或reset)对象。<br>
与重载关联容器的比较函数类似，我们必须在unique_ptr的尖括号指向类型之后提供删除器类型，并在创建或reset一个这种unique_ptr的时候提供一个指定类型的可调用对象。
```cpp
unique_ptr< objT , delT > p(new objT, func);
```

### weak_ptr
weak_ptr是一种不控制所指对象生存周期的智能指针，它指向一个shared_ptr管理的对象。
weak_ptr绑定到shared_ptr并不会改变其引用计数。并且一旦最后一个指向对象的shared_ptr被销毁，对象就会立即释放。即使有weak_ptr指向对象也是照样销毁。

![5](https://img-blog.csdnimg.cn/20200302220617258.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)   

---
## 智能指针与动态数组
标准库提供了一个可以管理new分配的数组的unique_ptr版本。为了用unique_ptr管理动态数组，必须在对象类型后面跟一对空括号。

```cpp
unique_ptr<int[]> up(new int[10]);
up.release();//自动使用delete[]销毁指针
```
由于up指向一个数组，当up销毁它管理的指针的时候，会自动使用delete[]。
与unique_ptr不同，shared_ptr不直接支持管理动态数组。如果想用shared_ptr管理一个动态数组，必须提供自己定义的删除器。
```cpp
shared_ptr<int> sp(new int[10],[]( int* p) { delete []p; });
sp.reset();
```

---
## allocator类
new的生成动态数组的时候，每一个元素都进行了默认初始化。而allocator类则把内存分配和对象构造分离开来。它提供一种类型感知的内存分配方法，它分配的内存是**原始的，未构造的**。

![7](https://img-blog.csdnimg.cn/20200302233304719.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302235350191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)