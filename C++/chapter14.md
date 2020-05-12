# C++ 操作重载与类型转换

# 输入输出运算符
输入输出运算符必须是非成员函数，因为如果是成员函数的话会强制this在运算符左侧，会增加理解难度。
```cpp
struct node{
	int x,y,sum;
}k;
ostream &operator<<(ostream &os, const node& Out){
	os<<Out.x<<" "<<Out.y<<" "<<Out.sum<<endl;
	return os;
}
istream &operator>>(istream &is, node& In){
	is>>In.x>>In.y;
	if(is)
		In.sum=In.x+In.y;
	else
		In=node();
	return is;
}
```
输入运算符必须处理输入可能失败的情况，而输出运算符不需要。而当读取操作发生错误时，输入运算符应该负责从错误中恢复。<br>
输入时的错误：
- 当流含有错误类型的数据时读取操作可能失败。
- 当读取操作达到文件末尾或遇到输入流的其他错误时也会失败。

# 递增和递减运算符
递增和递减运算符的重载时类似的，但问题在于如何区分前置和后置运算符。通常时这样重载的。
```cpp
struct node{
	int x,y,sum;
	node& operator++();//前置 ++x
	node  operator++(int);//后置 x++

	node& operator--();
	node  operator--(int);
}
```
实际上普通的重载形式无法区分这两种情况。为了解决这个问题，后置版本接受一个额外的（不被使用）的int参数，当我们使用后置运算符的时候，编译器为这个形参提供一个值为0的实参。并且，为了与内置版本一致，后置运算符返回对象的原值，返回形式是一个值而非引用。

# 函数调用运算符
如果类重载了函数调用运算符，则我们就可以像函数一样使用该类的对象。

## lambda是函数对象
在我们编写一个lambda后，编译器将表达式翻译成一个未命名类的未命名对象。在lambda表达式产生的类中含有一个重载的函数调用运算符

## 标准库定义的函数对象 
![1](https://img-blog.csdnimg.cn/20200304202223240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

# 重载、类型转换与运算符
## 类转换运算符
类转换运算符是类的一种特殊成员函数，它负责将一个类类型转换成其他类型。类型转换函数的一般形式	如下所示：<br>
operator *type*() const;
