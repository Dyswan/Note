# 控制内存分配
## new和delete的工作机理
### new
1. 调用operator new标准库函数。该函数分配一块足够大的、原始的、未命名的内存空间。
2. 编译器运行相应的构造函数以构造对象，并为其传入初始值。
3. 对象分配空间并构造完成后，返回一个指向对象的指针

### delete
1. 对所指对象执行析构函数或所指数组中的元素
2. 调用名为operator delete的标准库函数释放内存空间

## 重载new和delete
应用程序可以在全局作用域中定义也可以在类中定义operator new和operator delete函数。
### operator new接口和operator delete接口
标准库中定义了operator new函数和operator delete函数的8个重载版本。其中，前四个版本可能抛出bad_alloc异常，其他则不会
```cpp
void *operator new(size_t);
void *operator new[] (size_t);
void *operator delete(void *) noexcept;
void *operator delete[] (void *) noexcept;

void *operator new(size_t,nothrow_t&) noexcept;
void *operator new[] (size_t,nothrow_t&) noexcept;
void *operator delete(void *,nothrow_t&) noexcept;
void *operator delete[] (void *,nothrow_t&) noexcept;

```
### malloc函数和free函数
malloc函数接受一个表示等待分配字节数的size_t，返回指向分配空间的指针或返回0表示分配失败<br>
free函数几首一个void*，它是malloc返回指针的副本，free将相关内存返回给系统。调用free(0)没有任何意义。
```cpp
void * operator new(size_t size){
    if(void *mem = malloc(size))
        return mem;
    else 
        throw bad_alloc();
}
void * operator delete(void *mem) noexcept{
    free(mem);
}
```

### 定位new表达式
定位new的形式如下：
- new ( *place_address* ) *type*
- new ( *place_address* ) *type* ( *initializers* )
- new ( *place_address* ) *type* [ *size* ]
- new ( *place_address* ) *type* [ *size* ] { *braced initializers list* }

place_address必须是一个指针，同时initializers中提供一个（可能为空）以逗号分隔的初始值列表，该初始值列表将用于构造新分配的对象。

# 运行时类型识别
- typeid运算符：返回表达式类型
- dynamic_cast运算符：将基类的指针或引用安全地转化成派生类的指针或引用
--
## typeid运算符
typeid表达式形式是typeid(e)，其中e可以是任何表达式或类型的名字。typeid操作的结果是一个常量的引用，该对象的类型是标准库类型type_info或type_info的共有派生类型。<br>
当运算对象**不属于类类型或者是一个不包含任何虚函数的类**时，typeid运算符指示的时运算对象的静态类型。而当运算对象是**定义了一个虚函数的类的左值**时，typeid的结果直到运行时才会求得。<br>

    注意：
    当typeid作用于指针时（而非指针所指的对象），返回的救国应该是该指针静态编译时的类型

## type_info类
该类会随着编译器的不同略有差异
![1](https://img-blog.csdnimg.cn/20200311221046340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

# 枚举类型
C++ 包含两种枚举：限定作用域和不限定作用域。
- 限定作用域的枚举类型的一般形式：
    
    首先是关键字enum class（或enum struct），随后是枚举类型名字以及花括号括起来的以逗号分隔的枚举成员列表，最后是分号。
    ```cpp
    enum class open_modes{ input, output, append };
    ```
- 不限定作用域的枚举类型的一般形式：
    
    省略关键字class，枚举类型的名字是可选的
    ```cpp
    enum color{ red,yellow,green};
    enum { floatP=6,doubleP=10,double_doubleP=10};//未命名的不限定作用域
    ```
    未命名的话，我们只能在定义该enum时，定义它的对象。
---
### 枚举成员
```cpp
enum color{ red,yellow,green};
enum color1{ red,yellow,green};//错误，重复定义枚举成员
enum class COLOR{ red,yellow,green};

color c = green; //正确
COLOR C = green; //错误COLOR成员不在有效作用域
COLOR C1 = COLOR::green;//正确
```

---
## 指定enum的大小
C++11的新标准中，我们可以在enum的名字后加上冒号以及我们想在enum中使用的类型
```cpp
enum color: unsigned long long{ red,yellow,green};
```
限定作用域的enum成员类型默认是int，不限定则不存在默认类型。

# Union
