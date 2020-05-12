[模板]

# 定义模板
函数模板和类成员函数的定义通常放在头文件。

---
## 类模板
类模板是用来是生成类的蓝图的。与函数模板不同的是，编译器不能为类模板推断模板参数成员。

### 类模板的成员函数
类模板的成员函数在类模板的内外都可以定义，且定义在模板内的成员函数被隐式声明为内联函数。<br>
声明在类模板外的格式类似下面的
```cpp
template<typename T> ret-type Node<T>:: member-name( parm-list );
```

### 类模板名的别名
新标准允许我们为类模板定义一个类型别名：
```cpp
template<typenmae T>using twin = pair<T,T>
twin<int> t;// t是个 pair<int,int>
```

# 模板实参推断

## 尾置返回类型与类型转换
有时我们可能希望接受序列的迭代器，返回的是序列元素的引用。这是我们可以使用尾置返回类型。
```cpp
template<typename It>
auto fun(It beg ,It end)->decltype(*beg){
    /*...*/
    return *beg;
}
```
### 进行类型转换的标准库模板
上面是想要返回引用，可是如果想要值怎么办呢。问题在于迭代器解引用得到的是元素的引用，这是我我们可以使用标准库的类型转换模板。这个模板在type_traits的头文件中。<br>
模板中有个叫remove_reference的模板，它有一个模板参数和一个名为type的类型成员。
```cpp
template<typename It>
auto fun(It beg ,It end) -> 
remove_reference< decltype(*beg) >::type {
    /*...*/
    return *beg;
}
```
![1](https://img-blog.csdnimg.cn/2020030818382228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

---
## 理解std::move
看之前要理解的是引用折叠，以及特殊的右值引用参数推断。<br>
首先看特殊的右值引用参数推断，当我们将一个左值传递给函数的右值引用参数，且次右值引用指向模板类型参数（如T&&）的时候,编译器将推断T为左值引用，而非左值。<br>
其次，是引用折叠，规律总结起来就是：
- X& &&、x&& &，x& &等于x&
- x&& &&等于x&&

首先是是std::move的定义
```cpp
template <typename T>
typename remove_reference<T>::type&& move(T&& t){
    return static_cast<typename remove_reference<T>::type&& >(t);
}

string s1("hi!"),s2;
s2 = std::move(string("bye!"));//正确，从一个右值移动数据
s2 = std::move(s1);//正确，但在赋值之后，s1的值不确定
```

## forword
forword可以用来保持类型信息。forword必须通过显式模板实参调用。<br>
forword< Type >返回一个Type&&。如果Type是一个左值引用，则返回一个左值引用，如果type是一个右值则返回右值引用。

# 可变参数模板
可变参数模板就是一个接受可变数目参数的模板函数或模板类。可变数目的参数称为参数包，分为函数参数表，模板参数包。
```cpp
template<typename T,typename... Args>
void fun(const T&, const Args& ... rest);
```
---
## sizeof...运算符
sizeof...运算符能返回参数包中有多少个元素
```cpp
sizeof...(Args);
```

---
## 编写一个可变参数模板
可变参数函数通常都是递归的。
```cpp
template<typename T>
ostream &print(ostream &os,const T& t){
    return os<<t;
}
template<typename T,typename... Args>
ostream &print(ostream &os,const T& t, const Args& ... rest){
    os<<t;
    return print(os,rest...);
}
```

---
## 包拓展
对于一个参数包，除了获取大小，我们能对它做的唯一的事情就是扩展。 当扩展一个包是我们还要提供每个扩展元素的模式。其中模式可以理解为每个元素的样式，例如：func(type),const type&等等。
```cpp
template<typename T,typename... Args>
ostream &print(ostream &os,const T& t, const Args& ... rest){//扩展Args，模式为const Args&
    os<<t;
    return print(os,rest...);//扩展rest
}
```

# 模板特例化

## 特例化函数模板
当我们特例化一个函数模板的时候，必须为原模板中的每个模板参数都提供实参.
```cpp
template<typename T>int cmp(const T& ,const T&);
//特例化，其中T对应const char*
template<>
int cmp(const char *const &p1,const char *const & p2){
    return strcmp(p1,p2);
}
```
特例化本质上是实例化一个模板，而非重载。因此，特例化不会影响函数重载。
