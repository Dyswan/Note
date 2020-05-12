# tuple类型
tuple的操作：
![1](https://img-blog.csdnimg.cn/20200309155827624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

---
## 定义和初始化tuple
定义tuple和pair差不多，要注意的是tuple的构造函数时explicit的，因此我们必须使用直接初始化语法。
```cpp
tuple<int,int,int>p={1,2,3};//错误
tuple<int,int,int>p{1,2,3};//正确
```
同时tuple还有两个辅助类模板来查询成员的数量和类型，分别是tuple_size和tuple_element。
```cpp
tuple<int,string,int>p{1,"abc",3};

size_t sz=tuple_size<decltype(p)>::value;

tuple_element<1,decltype(p)>::type x=get<1>(p);
```

# 正则表达式
![2](https://img-blog.csdnimg.cn/2020030917332527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
- regex表示一个正则表达式
- regex_match和regex_search的区别在于整个串和子串

![3](https://img-blog.csdnimg.cn/20200309173614138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
### 指定regex对象的选项
对于regex我们可以对其进行一些设定来影响regex对象的操作。
![4](https://img-blog.csdnimg.cn/20200309185346477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
![5](https://img-blog.csdnimg.cn/20200309185407280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)