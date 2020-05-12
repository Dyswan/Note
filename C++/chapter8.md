# C++ IO库
<!-- TOC -->

- [C++ IO库](#c-io库)
    - [IO类](#io类)
        - [IO对象无拷贝或赋值](#io对象无拷贝或赋值)
        - [IO状态](#io状态)
        - [查询流的状态](#查询流的状态)
        - [管理输出缓冲](#管理输出缓冲)
        - [刷新缓冲区的操作符](#刷新缓冲区的操作符)
        - [unitbuf操纵符](#unitbuf操纵符)
        - [关联输入和输出流](#关联输入和输出流)
    - [文件输入输出](#文件输入输出)
        - [文件模式](#文件模式)
    - [string流](#string流)

<!-- /TOC -->

---
## IO类
###  IO对象无拷贝或赋值
IO对象无拷贝或赋值。因此
- 不能将形参或返回类型设置为流类型
- 进行IO操作的函数通常以引用的方式传递或返回流。但不能是const的，因为IO对象会改变其状态。

### IO状态
下面这张表是C++ primer中的表，表列出了IO类所定义的一些函数和标志，可以帮助我们访问和操纵流的条件状态。
![图1](https://img-blog.csdnimg.cn/20200227124253372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)
![图二](https://img-blog.csdnimg.cn/2020022712445015.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)


### 查询流的状态
IO库定义了一个与机器无关的iostate类型，它提供了表达流状态的完整功能。并且IO库定义了4个iostate类型的constexpr值表示特定的位模式。
- badbit表示系统级错误，一旦badbit被置位，流就无法再使用了。
- failbit置位，表示发生了可恢复的错误，这种问题通常是可以修正的，流还可以继续使用。
- 如果到达文件结束位置，eofbit和failbit都会被置位。
- goodbit的值为0，表示流未发生错误。
- 如果badbit，failbit和eofbit任意一个被置位，则检测流状态的条件会失败。

### 管理输出缓冲
每一个输出流都管理一个缓冲区，用来保存程序读写的数据。有了缓冲区，操作系统就可以将程序的多个输出操作变成单一的系统级写操作，这样能带来很大的性能提升。<br>
文本串可能会立即输出,也可能保存再缓冲区里面。而导致缓冲刷新（即，数据真正的写到输出设备或文件）的原因有很多：

- 程序正常结束，作为main函数的return操作的一部分，缓冲刷新被执行。

- 缓冲区满时，需要缓冲刷新，而后新数据才能继续写入缓冲区。

- 可以使用操作符如endl来显式刷新缓冲区。

- 在每个输出操作之后，可以用操作符unitbuf设置流的状态，来清空缓冲区。默认情况下，对cerr是设置unitbuf的，因此写到cerr的内容都是立刻刷新的。

- 一个输出流被关联到另一个流的时候，在这种情况下，当读写被关联流的时候，关联到的流的缓冲区会被刷新。例如，默认情况下，cin和cerr都关联到
cout。因此，读cin或写cerr都会导致cout缓冲区被刷新。

### 刷新缓冲区的操作符
IO库中与endl相似的操作符还有两个：

- endl，  换行+刷新缓冲区

- flush， 不附加字符+刷新缓冲区

- ends，  空字符+刷新缓冲区

### unitbuf操纵符
使用unitbuf操纵符能让接下来每次写操作之后都进行一次flush操作。而nounitbuf则可以重置流，使其恢复正常的系统管理的缓冲区刷新机制。

### 关联输入和输出流
当一个输入流绑定到一个输出流时，任何试图从输入流读取数据的操作都会先刷新关联的输出流。在标准库中默认将cin和cout绑定在一起。那么，该怎么绑定输入和输出流呢？
C++中提供了tie函数来绑定输入流和输出流。以cin为例
```cpp
//无参数版本
cin.tie();//返回当前绑定的输出流

//接受指向输出流的指针
cin.tie(&out);//将自己绑定到此ostream，返回上一个绑定的输出流
```
每个流最多关联到一个流，但多个流可以同时关联到同一个ostream

---
## 文件输入输出
头文件fstream定义了三个类型来支持文件IO:
- ifstream从一个给定文件读取数据
- ofstream向一个给定文件写入数据
- fstream读写给定文件

### 文件模式
![mode](https://img-blog.csdnimg.cn/20200227222319481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)

---
## string流
sstream头文件提供了三个类型来支持内存IO：
- istringstream从string读取数据
- ostringstream向string写入数据
- stringstream对string进行读写操作

并且stringstream还有以下特有的操作：
![string](https://img-blog.csdnimg.cn/20200227225522852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg1OTcxNg==,size_16,color_FFFFFF,t_70)