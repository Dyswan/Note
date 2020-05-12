# C++ 动态内存

## 智能指针
在C++中，动态内存的管理是通过**new**和**delete**这两个运算符来完成的。但动态内存的使用很容易出问题，因为确保在正确的时间释放内存是十分困难的。因此，为了更容易也更安全的使用动态内存，新标准的C++提供了两种智能指针类型来管理动态对象。<br>

智能指针与常规指针重要的区别在于它负责**自动释放所指向的对象**。<br>

刚刚提到新标准的C++提供了两种智能指针类型，这两个指针类型分别是**share_ptr**，**unique_ptr**。这两种智能指针的区别在于管理底层指针的方式，**shate_ptr**允许多个指针指向同一个对象，**unique_ptr**则只能“独占”所指对象。

以下是智能指针的都支持的操作：

![1](https://img-blog.csdnimg.cn/20200302164722384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
---
## share_ptr类
share_ptr特有的操作：
![2](https://img-blog.csdnimg.cn/2020030216494133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)