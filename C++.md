---
title: C++
date: 2023-02-11 14:22:46
categories: Computer Science
tags: Learning
---

---
### Introduction
本篇博客的内容是C++学习过程的记录，主要参考资料和帮助工具包括：
- IDE:Microsoft Visual Studio 2017 / Apple Xcode
- MSVC / GCC
- C++ Primer Plus
- Linker、Loader and Library
- [YouTube The Cherno's C++ Series](https://www.youtube.com/@TheCherno)
- [CPP Reference](https://en.cppreference.com/w/)
- [GitHub imgui](https://github.com/ocornut/imgui)
- [GitHub SimpleNES](https://github.com/amhndu/SimpleNES)
- [OpenAI ChatGPT](https://chat.openai.com/)
- [Bjarne Stroustrup's Blog](https://www.stroustrup.com/)

阅读需求：需要一定的C语言基础和编程常识。
阅读提示：章节排序和举例内容大多参照Cherno‘s Video
代码示例风格：
- 每条语句独占一行
- 每个函数都有一个开始和结束的花括号，并且独占一行
- 函数中的语句都相当于花括号缩进
- 与函数名相关的花括号周围没有空格

### C++ 起源
上世纪八十年代贝尔实验室的Bjarne Stroustrup对C语言进行了扩充，最早叫C with Classes，名称C++来自C语言中的自增自减符++，名称C++即表明它是C的扩充版本。随着C++的发展，也对ANSI C的标准产生了影响。

Stroustrup编写的The Programming Language包含65页的参考手册，它成了最初的C++事实标准。

为了提高C++的可移植性、兼容性，1990年美国国家标准局ANSI开始着手制定C++标准，国际标准化组织ISO很快通过自己的委员会加入行列，创建联合组织ANSI/ISO，致力于制定C++标准。

第一个国际标准```ISO/IEC 14882:1998```于1998年获得ISO、IEC、ANSI的批准，该标准常被称为C++98.该标准不仅描述了已有的C++特性，还添加了异常、运行阶段类型识别RTTI，模版、标准模版库STL。

委员会于2003年批准了```ISO/IEC 14882:2003```标准，主要是订正了错误、减少多义性，并没有改变语言特性，被称为C++03，由于没有改动特性常使用C++98表示C++98/C++03。

委员会于2011年批准了```ISO/IEC 14882:2011```标准，C++11标准的目标是消除不一致性。


### C++是如何工作的
#### 一段C++代码示例
```cpp
#include <iostream>   //预处理发生在编译之前
//include的作用是找到名为<iostream>的文件，将其拷贝过来，称为头文件

int main()  //程序入口
{
    std::cout << "hello world!" << std::endl;
    std::cin.get();
    //main函数可以不写返回值，默认返回0
}
```
#### 怎么从C++源代码转换成二进制文件（机器码）？
简单的说就是编译、连接、执行三步：
- **step1**: 将iostream文件中的内容拷贝到代码文件中
- **step2**: 编译器将.cpp文件编译为object file(.obj)
- **step3**: 链接(link)将所有obj文件粘合到一起生成.exe文件
>注：link的作用简单的说就是帮助在多文件项目中寻找函数

### 编译器是如何工作的
最初Stroustrup使用的编译器(C front end)，是从C++转换成C再生成obj代码，随着C++的发展，C++开始有独立的编译器，直接生成obj代码。

对于C++编译器来说没有文件的概念
一个翻译单元不一定等于一个C++文件

编译的第一阶段：预处理阶段，编译之前的过程
编译器遍历预处理语句，粘贴头文件代码
一个例子:编译器会在预处理时会把头文件EndBrace.h的内容复制到Math.cpp
```cpp
//文件Math.cpp
int Mutiply(int a , int b ){
    int result = a*b;
    return result;
#include "EndBrace.h"
```

```cpp
//文件EndBrace.h
}
```
编译器的模式和速度是可以调节的，编译器在编译的过程中会生成额外的部分帮助你debug，返回更多的错误信息。有些时候设置编译器debug状态下的最大化速度，会自动帮你优化代码。


### 连接器是如何工作的
build一个项目是编译+连接的过程
编译过程的错误是C开头，如C2143拼写错误
连接过程的错误是LNK开头，如LNK561缺少主函数

linker帮助我们寻找相同类型、函数名、变量、返回参数的函数，找不到就会报错
linker如果在多个文件中找到同名函数，同样会报错
常见错误：由于头文件的复制，导致出现linker发现同名函数
解决：头文件中的函数可以声明为staic函数以确保这个函数链接时只发生在内部

### 使用CMake C++工程文件
>Visual Studio IDE MSVC编译器下可以不用CMake

不同的IDE所集成的make工具所遵循的规范和标准都不同，也就导致其语法、格式不同，也就不能很好的跨平台编译，会再次使得工作繁琐起来，那么cmake为了解决这个问题而诞生了，其允许开发者指定整个工程的编译流程，在根据编译平台，生成本地化的Makefile和工程文件，最后用户只需make编译即可。

#### 使用过程
针对已有的源代码可以编写```CMakeLists.txt```文件，此文件会被CMake工具解析生成相应的```CMakeFiles``` ```CMakeCaches.txt``` ```cmake_install.cmake```等等配置文件。
>当然也可以在写代码之前直接使用VSCode中CMake的```Quark Stark```快速配置，会创建```build```目录并生成以上文件。

- 省略了camke的配置过程
- 使用cmake命令解析(更新)构建系统，过程中会生成以上文件
```
    cmake ./
```
- 执行make命令编译项目
```
    make
```
- 如果有更多需求可以更改```CMakeLists.txt```中的配置函数，比如下方所示需要配置多个源文件
```shell
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)
# 项目信息
project (Demo2)
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)
# 指定生成目标
add_executable(Demo ${DIR_SRCS})
```
#### 常用的CMake命令：
Generate a Project Buildsystem
```shell
 cmake [<options>] <path-to-source>
 cmake [<options>] <path-to-existing-build>
 cmake [<options>] -S <path-to-source> -B <path-to-build>
```
Build a Project
```shell
 cmake --build <dir> [<options>] [--<build-tool-options>]
```
Install a Project
```shell
 cmake --install <dir> [<options>]
```
Open a Project
```shell
 cmake --open <dir>
```
Run a Script
```shell
 cmake [{-D <var>=<value>}...] -P <cmake-script-file>
```
Run a Command-Line Tool
```shell
 cmake -E <command> [<options>]
```
Run the Find-Package Tool
```shell
 cmake --find-package [<options>]
```
View 
```shell
 cmake --help[-<topic>]
```

### C++头文件
头文件中的```#pragma once```是一个预处理指令，它通常用于防止头文件被重复包含。

在C++中，头文件通常用于存放函数、类、变量和常量等声明，以便在多个源文件中共享它们。当源文件包含一个头文件时，编译器将包含的头文件内容复制并插入到源文件中，以便在编译时处理。如果多个源文件都包含了相同的头文件，则会导致重复定义，这将导致编译器出现错误。

<>或""来指定包含的文件的位置。
<>用于包含系统或编译器提供的头文件，编译器将从标准的系统路径中搜索该文件。
例如，以下是包含标准输入输出库的头文件<iostream>的示例：
```cpp
#include <iostream>
//请注意iostream文件没有扩展名，这是C++标准库的设计
```
""用于包含用户自定义的头文件，编译器将从当前源文件所在的目录开始搜索该文件。

例如，以下是包含自定义头文件myheader.h的示例：
```cpp
#include "myheader.h"
```
如果在当前源文件所在的目录中找不到所需的头文件，则编译器将从指定的系统路径中搜索它。

通常，使用<>来包含系统头文件，而使用""来包含自定义头文件，以避免头文件重名的问题。

需要注意的是，在实践中，使用""包含系统头文件和使用<>包含自定义头文件是可能的，但不建议这样做，因为这可能会导致代码可移植性的问题。

### debug and release
debug模式下编译器会做很多额外的事情帮助你调试程序
    debug的过程黄色箭头、红点
release模式则会更快的帮你生成优化的项目

### Visual Studio 设置
- VS创建项目会生成一个```项目文件夹```和一个```解决方案.sln```文件（本质是一个目录性质的文本文件），项目文件夹中会有```.vcxproj```（本质是一个XML文档）。
- VS自己建立的过滤器视图，是一种虚拟的管理视图，并不会真的在文件目录中那样储存，过滤器的虚拟视图和磁盘上的实际的文件目录没关系。
- 建议自己点击``` 解决方案资源管理器```-```显示所有文件```-右键项目文件```添加```-```新建文件夹```，自己建立一个名为```source```或者``` src```用于管理自己创建的源代码文件、头文件等等文件，用以区别项目文件和可能使用的任何其他资源。
- debug模式build项目的过程中，默认会把生成两个名为debug的文件，一个是包含编译过程的中间文件在项目文件夹内，另一个是生成的可执行文件在与项目文件夹并列在根目录中。
如果不满意这些设定可以在项目设置中更改：
**Output Directory:**```$(SolutionDir)bin\$(Platform)\$(Configuration)\```
**Intermediate Directory:**```$(SolutionDir)bin\intermediates\$(Platform)\$(Configuration)\```
将生成文件放在```bin```文件下，将中间文件放在```bin\intermediates```文件下
并在后面加上平台和配置信息
  
### C++变量
不同变量的唯一区别本质是大小


### 条件分支、循环、控制流语句
if语句速度不快，不建议多用
for
while
do while 
continue
break
return

### C++指针(*)
高端理解(:dog:) :  指针是一个整数，一种储存内存地址的数字。请暂时忘掉哪些所谓的数据类型，不过是以不同字节大小存储的数字，类型只是为了让编程更容易而创造的某种虚构，类型没有意义。

空指针：void指针是一种通用指针类型，可以指向任何类型的数据

### C++引用(&)
高端理解(:dog:) :  引用只是指针的伪装，只是指针的语法糖。

这段代码定义了一个名为 a 的整型变量，初始化为 5。接下来，又定义了一个名为 ref 的整型引用，并将其绑定到 a 上。

引用是一个别名，它提供了对变量的另一个名称。在这个例子中，ref 是 a 的引用，因此 ref 和 a 实际上指向同一个整型变量。因此，当我们修改 ref 的值时，实际上是修改了 a 的值，反之亦然。
```cpp
#include<iostream>
int main()
{
    int a = 5;
    int& ref = a;  //定义一个a的引用
   /*  ref并不是一个真正的变量，本质是一个(*指针常量)，
   意义上等价于*(int* const ref),编译后只有变量a   */
    ref = 2;  //实际的效果相当于给变量a起了个别名叫ref
    std::cout>>a>>std::endl;
    std::cin.get();
}
```
```cpp
2
```
通过引用，我们可以避免在代码中频繁地使用指针或副本，从而提高代码的可读性和效率。在C++中，引用是一种非常有用的特性，可以用于传递函数参数、返回值和对变量的别名操作等场景。
提升效率的例子：传递函数参数时，可以使用引用作为函数参数，将实参的别名传递给函数，以避免函数内部复制参数的开销。
```cpp
void swap(int& x, int& y) {
  int temp = x;
  x = y;
  y = temp;
}
int a = 5, b = 10;
swap(a, b); // a = 10, b = 5
```

### C++类
#### C++ Class和Struct的区别
类定义
结构体定义

>Cherno:
唯一区别在于访问修饰符，Class默认private,Struct默认public。
C++保留结构体的原因是希望能兼容C。
如果只想表示一些数据我会用结构体，如果想要实现大量功能会用类。

一段类代码的示例，很烂，但是帮助你学习逻辑。
```cpp
#include<iostream>
class Log
{
public:
    const int LogLevelError = 0;
    const int LogLevelWarning = 1;
    const int LogLevelInfo = 2;
private:
    int m_LogLevel = LogLevelInfo;
public:
    void SetLevel(int level)
    {
        m_LogLevel = level;
    }
    void Info(const char* message)
    {
        if (m_LogLevel >= LogLevelInfo)
            std::cout << "[INFO]:" << message << std::endl; 
    }
    void Error(const char* message)
    {
        if (m_LogLevel >= LogLevelError)
            std::cout << "[ERROR]:" << message << std::endl; 
    }
    void Warn(const char* message)
    {
        if (m_LogLevel >= LogLevelWarning)
            std::cout << "[WARNING]:" << message << std::endl; 
    }
};
int main()
{
    Log log;
    log.SetLevel(log.LogLevelWarning);
    log.Warn("Hello!");
    log.Info("Hello!");
    log.Error("Hello!");
    std::cin.get();
}
```
### 内存中的区域
栈区
堆区
静态存储区
代码区
### C++ static
静态和非静态是编程中的两个概念，带有static关键词的就是静态的。static有两种含义，一种是在类或结构体外部使用static关键字，另一种是在类或函数内部。
#### 类外的static
类外的static，意味着链接只在内部，只能对你定义它的翻译单元可见。另一个翻译单元会忽略有static关键词的变量或函数。
#### 类里的static
类中的static，意味着该变量实际上将与类的所有实例共享内存。
下面讨论一个在类中没有静态变量的例子：
```cpp
#include<iostream>
struct Entity
{
    int x,y;
    void Print()
    {
        std::cout << x << "," << y << std::endl;
    }
};
int main()
{
    Entity e;
    e.x = 2;
    e.y = 3;
    Entity e1 = {5,8};
    e.Print();
    e1.Print();
    std::cin.get();
}
//2,3
//5,8
```
静态成员变量在编译时存储在静态存储区，即定义过程应该在编译时完成，因此一定要在类外进行定义，但可以不初始化
```cpp
#include<iostream>
struct Entity
{
    static int x,y;
    void Print()
    {
        std::cout << x << "," << y << std::endl;
    }
};
/*
静态成员变量是所有实例共享的，而且其只是在类中进行了声明，并未定义或初始化（！！！未分配内存！！！）。
未定义或初始化的话，类或者类实例就无法访问静态成员变量，比如Entity e1 = {5,8};是错误的。
如果不在类或结构体外部定义显然是不对的，所以必须先在类外部定义，也就是分配内存才可以使用。
否则会发生Link错误
*/
int Entity::x;
int Entity::y;

int main()
{
    Entity e;
    e.x = 2;
    e.y = 3;
    Entity e1;
    e1.x = 5;
    e1.y = 8;
    e.Print();
    e1.Print();
    std::cin.get();
}
//5，8
//5，8    
//打印两次5，8是因为静态成员变量的所有实例是共享的，不同的实例e和e1指向相同的x和y
```
> cherno：比如你有一条信息，想要在所有Entity实例间共享数据，或者将它实际存储在Entity类中，static是有有意义的。想要组织好的代码，最好在类中创建一个静态变量，而不是一些静态的或全局的东西到处乱放。

总结：静态成员是指在类中使用static关键字定义的成员，它属于类，不属于对象，可以直接使用类名调用。而非静态成员则是指没有使用static关键字定义的成员，它属于对象，只能通过对象名来调用。
静态和非静态的区别主要有以下几点:
- 静态成员属于类，不属于对象；非静态成员属于对象，不属于类。
- 静态成员可以直接使用类名调用；非静态成员只能通过对象名来调用。
- 静态成员在内存中只有一份拷贝，被所有对象共享；非静态成员在每个对象中都有一份拷贝。
- 静态成员可以在没有创建任何对象的情况下被访问；非静态成员必须在创建了对象之后才能被访问。
此外，静态方法和静态变量会随着类的定义而被分配和装载入内存中，生命周期跟相应的类一样长。一直到线程结束，静态属性和方法才会被销毁。而非静态方法则是属于对象的，生命周期跟相应的对象一样长。
#### 函数中的static（local static）
声明一个变量时，我们需要考虑变量的作用域和生命周期。
函数中的static和类中的static在声明周期上没有区别，唯一的区别是，在类的作用域中，类的任何东西可以访问它，如果在函数作用域中声明一个静态变量，那么它将是函数的局部变量。
```cpp
#include<iostream>
//int a=0;   --1
void function()
{
    //int a = 0;   --2
    //static int a = 0;   --3
    a++;
    std::cout << a << std::endl;
}
int main()
{
    for(int i=0;i<5;i++)
    {
        function();
    }
    std::cin.get();
}
//--1 1,2,3,4,5
//--2 1,1,1,1,1
//--3 1,2,3,4,5
```
单例模式中的local static
```cpp
#include<iostream>
class Singleton
{
    public:
        static Singleton& Get()
        {
            static Singleton instance;
            return instance;
        }
    void Hello()
    {
        std::cout<<"hello"<<std::endl;
    }
};
int main()
{
    Singleton::Get().Hello();
    std::cin.get();
}
```


### C++ 枚举
```cpp
#include<iostream>

class Log
{
public:
    enum Level
    {
        LevelError=0,LevelWarning,LevelInfo
    };
private:
    Level m_LogLevel = LevelInfo;
public:
    void SetLevel(Level level)
    {
        m_LogLevel = level;
    }
    void Error(const char* message)
    {
        if (m_LogLevel >= LevelError)
            std::cout << "[ERROR]:" << message << std::endl; 
    }
    void Warn(const char* message)
    {
        if (m_LogLevel >= LevelWarning)
            std::cout << "[WARNING]:" << message << std::endl; 
    }
    void Info(const char* message)
    {
        if (m_LogLevel >= LevelInfo)
            std::cout << "[INFO]:" << message << std::endl; 
    }
};
int main()
{
    Log log;
    log.SetLevel(Log::LevelError);
    log.Warn("Hello!!");
    log.Info("Hello!!");
    log.Error("Hello!!");
    std::cin.get();
}
```
### C++ <>操作符
在 C++ 中，<>符号通常用于指定模板参数类型。在模板的定义和使用中，我们使用<>符号将模板参数列表括起来，以指定模板的具体类型。
```cpp
template<typename T>
class MyClass {
   // Class code here
};
```
在这里，<typename T> 中的<>符号表示这是一个模板参数列表，其中typename T表示模板参数类型为T。
C++中的vector实际就是模版类，常用```<>```指定其类型
### C++ 函数
#### 函数类型
#### 构造函数
一种特殊类型的方法，这是一种每次你构造一个对象时都会调用的方法，用以帮助初始化类。
构造函数没有返回类型，名称必须与类名相同。
```cpp
class Entity
{
     public:
    {
        float x,y;
        Entity()
        {
            x = 0.0f;
            y = 0.0f;
        }
    }
}
```
当用new关键词生成一个对象的时候，编译器会自动提供一个构造函数，如果你想删除它，可以用delete关键词
```cpp
    class log
    {
        private:
                log()=delete; //删除默认构造函数
                static void write(){}
    }；
```
#### 构造函数的初始化成员列表
构造函数中的函数名后加```:```，添加成员，每调用这个函数会执行一次
```cpp
#include <iostream>
class Example
{
public:
    Example()
    {
        std::cout << "Created Entity" << std::endl;
    }
    Example(int x)
    {
        std::cout << "Created Entity" << x << std::endl;
    }
};
class Entity
{
private:
    std::string m_Name;
    Example m_Example;
public:
    Entity()
        //m_Example(Example(8))
    {
        m_Name = std::string("Unknown");
        m_Example = Example(8);
    }
    Entity(const std::string& name)
        :m_Name(name)
    {
    }
};
int main()
{
    Entity e0;
}
/*
Created Entity
Created Entity8
如果使用列表：
Created Entity8
*/
```
#### 析构函数 destructor
析构函数，帮助你销毁对象，清理内存的东西。
```cpp
#include<iostream>
class Entity
{
     public:
        float X,Y;
        Entity()
        {
            X = 0.0f;
            Y = 0.0f;
            std::cout<<"Created Entity"<<std::endl;
        }
        ~Entity()   //构造一个destructor,形式上就是在构造函数前加上'~'
        {
            std::cout<<"Destoryed Entity"<<std::endl;
        }
        void print()
        {
            std::cout<< X <<","<< Y <<std::endl;
        }
};
void Function()
{
    Entity e;
    e.print();
    e.~Entity(); //有趣的是，当你在函数内部调用析构函数时，并没有销毁对象，它就像一个普通函数那样执行了而已
}
int main()
{
    Function();
    std::cin.get();
}
/* result:
Created Entity
0,0
Destoryed Entity
Destoryed Entity
*/
```
### 继承
待补充
### 虚函数 Virtual Function
首先：强调一个概念
定义一个函数为虚函数，不代表函数为不被实现的函数。
定义他为虚函数是为了允许用基类的指针来调用子类的这个函数。
定义一个函数为纯虚函数，才代表函数没有被实现。
定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数。
```cpp
class A
{
public:
    virtual void foo()
    {
        cout<<"A::foo() is called"<<endl;
    }
};
class B:public A
{
public:
    void foo()
    {
        cout<<"B::foo() is called"<<endl;
    }
};
int main(void)
{
    A *a = new B();
    a->foo();   // 在这里，a虽然是指向A的指针，但是被调用的函数(foo)却是B的!
    return 0;
}
```
这个例子是虚函数的一个典型应用，通过这个例子，也许你就对虚函数有了一些概念。它虚就虚在所谓"推迟联编"或者"动态联编(Dynamic Dispatch)"上，一个类函数的调用并不是在编译时刻被确定的，而是在运行时刻被确定的。由于编写代码的时候并不能确定被调用的是基类的函数还是哪个派生类的函数，所以被成为"虚"函数。
虚函数只能借助于指针或者引用来达到多态的效果。
v table
虚函数的作用，帮你指向正确的override
虚函数并不是没有内存开销的，首先需要额外的内存来储存v table，还要一个成员指针指向v表；其次调用虚函数时，需要遍历v table去确定要映射到哪个函数。
### C++接口（纯虚函数）Pure Virtual Function
C++的接口其实就是一个类，并没有interface关键词。

纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加 =0:
```cpp
virtual void funtion1()=0
```
### C++访问修饰符
> cherno:可见性是让代码更容易维护、理解，不管是阅读代码还是扩展代码，这与性能无关，也不会产生完全不同的代码，可见性不是CPU层面需要理解的东西，只是人发明的概念，为了帮助其他人和自己。

### C++数组
> 数组下标的神奇理解：多数编程语言的数组下标是从```0```开始的,可以把它理解为对内存起始地址的‘**偏移**’

用new关键字生成（堆上创建）的数组将会一直在内存中存在直到你删除它。
原始数组大小的设置需要注意。⚠️
#### array类创建数组
```cpp
#include<array>
std::array<int,5> another;
```
### C++字符串
```cpp

```
### C++字符串字面量 String Literal
[String Literal](https://www.apiref.com/cpp-zh/cpp/language/string_literal.html)

### C++ const
>Cherno:const是我喜欢称之为伪关键字的东西，因为它在改变生成代码方面做不了什么。它有点像类和结构体的可见性，这只是一个机制让我们的代码更干净，并对开发人员写代码强制特定的规则。
const有两层含义，编译期常量和只读变量写
#### const与指针
如果唯一的 const 位于符号*的左侧，表示指针所指数据是常量，数据不可变
如果唯一的 const 位于符号*的右侧，表示指针本身是常量，指针不能指向其他内存地址
在符号*的左右各有一个const时，表示指针和指针所指数据都是常量
```cpp
const int* a = new int; //常量指针，你不能修改改指针指向的内容
int const* a = new int; //与上面是同样的效果 
``` 
```cpp
int* const a = new int; //指针常量，你不能把实际的指针本身重新赋值，指向别的东西
```
#### 类中的const
```cpp
#include <iostream>
class Entity
{
private:
    int* m_X,*m_Y; 
public:
    const int* const GetX() const  //使方法只读,指针指向的不能修改，指针本身也不能修改
    {
        return m_X;
    }
    void SetX(int x)
    {
        m_X = x;
    }
};
int main()
{
    std::cout << "Hello, World!\n";
    return 0;
}
```
常量对象只能调用常量函数

### C++ mutable
2种用法
1，修饰class const方法中class成员变量，使其可以修改。
2，修饰lambda表达式，值捕获时可以直接操作传入参数。(并非引用捕获，依旧值捕获，不修改原值)这种情况很少用

### C++ 三元操作符
三元操作符一般比if else语句更快

### C++ 创建对象与初始化
即使一个空类什么也没有，创建一个对象也要占用至少1个字节的内存

```cpp
using String  = std::string;
class Entity
{
    private:
            String m_Name;
    public:
        Entity() :m_Name("Unknown"){}
        Entity(const String& name) :m_Name(name){}
        const String& GetName() const {return m_Name;}
};
int main()
{
    /*
    Entity entity;  //默认调用了构造函数，已经初始化了对象，这点与C#和JAVA不同
    Entiy entity("Odrin");
    Entity entity = Entity("Odrin");  //与上一行相同，最快初始化的方法，“管控”
    以上都是在栈上分配的内存，生命周期和所在的作用域是一样的。
    */

    //栈通常比较小1Mb～2Mb，如果entity过大，应该分配到堆上
    Entity* entity = new Entity("Odrin"); 
    /*
    new关键字会在堆上生成一个对象
    等号左边的会调用Entity类的构造函数，返回一个Entity*，即对象entity在堆上被分配的地址。
    JAVA和C#（C#的struct可以生成在栈上）全部都是在堆上生成的对象，C++有额外的选择可以在栈上生成对象。
    堆上分配要比栈花费更多的时间，而且堆上分配你必须手动释放
    */
    std::cout << entity.GetName() << std::endl;
}
```
### C++ new

new的本质
```cpp
Entity* e = new Entity();
Entity* e = (Entity*)malloc(sizeof(Entity)); //与上句的区别是，只是分配了内存并返回了该内存的地址，但并没有调用构造函数
```


### C++ 隐式转换与explicit

```cpp
class Entity
{
    private:
        std::string m_Name;
        int m_Age;
    public:
        Entity(const std::string& name)
            :m_Name(name),m_Age(-1){}
        /* explicit */ Entity(int age)   
            :m_Name("Unkown"),m_Age(age){}

};
void PrintEntity(const Entity& entity)
{
    std::cout<<"Entity"<<std::endl;
}
int main()
{
    //例一
    Entity b = 22; //通过构造函数发生了隐式转换，int类型的22变成Entity类型，如果构造函数前有关键词explicit，则必须显示转换，Entity b(22); 或者 Entity b = (Entity)22;
    //例二 
    PrintEntity(Entity("Odrin"))  //需要两次隐式转换，但只能发生一次，第一次从char型数组变成std::string，第二次从string变成Entity，所以加上一次显式转换
    //或者写成 PrintEntity(std::string("Odrin"))  
}
```
此外还有强制转换```cast```

### 运算符及其重载
当我们需要用到运算符重载时，往往是类中的一种特殊类型需要处理或者类本身需要处理。
可以使用```operator```关键字重载操作符
```cpp
std::ostream& operator <<(std::string& stream, const Vector2& other)
{
    stream << other.x << "," << other.y ; 
}  //重载了输出操作符’<<‘，使其可以直接输出一个结构体Vector2(并未写出)
```

### C++ this关键字
this是一个指向当前对象实例的指针，该方法属于这个对象实例。

### 对象的生存周期（栈作用域）
一般的初始化是在栈上分配的，```new```是在堆上分配的
栈分配的内存，在作用域失效后会释放。
下面这个例子利用了栈的特性写了一个自动分配、删除指针的包装器，去管理堆上生成的对象。
```cpp
class Entity
{
     public:
        float X,Y;
        Entity()
        {
            X = 0.0f;
            Y = 0.0f;
            std::cout<<"Created Entity"<<std::endl;
        }
        ~Entity()  
        {
            std::cout<<"Destoryed Entity"<<std::endl;
        }
};
class ScopedPtr()
{
    private:
        Entity* m_ptr;
    public:
        ScopedPtr(Entity* ptr)
            :m_ptr(ptr)
        {
        }
        ~ScopedPtr(Entity* ptr)
        {
            delete m_ptr; 
        }
};
int main()
{
    {
        ScopedPtr e = new Entity();
    }
    std::cin.get();
}
```

### C++ 智能指针

new在堆上分配内存，detele删除内存，智能指针是一种帮助你实现这一过程自动化的方式。

**unique_ptr**
作用域指针，超出作用域时会被销毁，然后调用delete
不能被“复制”的指针，但是低开销
```cpp
std::unique_ptr<Entity> entity = std::make_unique<Entity>();
```
**shared_ptr**
引用计数器记录该指针被“复制”的次数
引用计数器为0时，内存会被释放
shared_ptr需要分配另一块内存，叫做控制块，用来存储引用计数。
所有的引用消除后，才会释放内存
```cpp
class Entity
{
        Entity()
        {
            std::cout<<"Created Entity"<<std::endl;
        }
        ~Entity()  
        {
            std::cout<<"Destoryed Entity"<<std::endl;
        }
};
int main()
{
    {
        std::shared_ptr<Entity> e0;
        {
            std::shared_ptr<Entity> sharedEntity = std::make_shared<Entity>();
            e0 = sharedEntity;
        }
    }
}
```
**weak_ptr**
可以和shared_ptr结合使用，把一个shared_ptr赋值给weak_ptr不会增加引用计数器的次数。
他可以被复制，但同时不会增加额外的控制块用来计数，仅仅用来声明这个指针还活着。
```cpp
int main()
{
    {
        std::shared_ptr<Entity> e0;
        {
            std::shared_ptr<Entity> sharedEntity = std::make_shared<Entity>();
            e0 = sharedEntity;
        }
    }
}
```
### C++ 友元函数
在 C++ 中，友元函数（friend function）是一种特殊的函数，可以访问类中的私有成员和受保护成员，即使它们不是类的成员函数。这样，友元函数可以扩展类的功能，同时保护类的封装性。
友元函数通常在类的声明中进行声明，在函数声明前加上关键字friend。例如：
```cpp
class MyClass {
public:
   // Public members here

private:
   int x; // Private member

   friend void myFriendFunc(MyClass& obj); // Friend function declaration
};
```
在上面的代码中，myFriendFunc是一个友元函数，可以访问 MyClass 的私有成员 x。在函数声明中，使用friend关键字将该函数声明为 MyClass 的友元函数。
需要注意的是，友元函数并不是类的成员函数，因此不能通过类的对象调用它们。而是通过函数名直接调用。
友元函数有一些使用场景，例如：
- 当需要访问一个类的私有成员时，但又不希望将该成员变成公有成员时，可以使用友元函数。
- 当需要两个或多个类互相访问对方的私有成员时，可以使用友元函数。

### C++ 复制与拷贝构造函数 
一个基本的String类中的浅拷贝过程
```cpp
#include <iostream>
#include <string>
class String
{
private:
    char* m_Buffer;
    unsigned int m_Size;
public:
    String(const char* string)
    {
        m_Size = strlen(string);
        m_Buffer = new char[m_Size+1];   //加上终止字符‘/0’
        memcpy(m_Buffer,string,m_Size+1);
    }
    ~String()
    {
        delete[] m_Buffer;   //释放内存
    }
    char& operator[](unsigned int index)
    {
        return m_Buffer[index];
    }
    friend std::ostream& operator<<(std::ostream& stream,const String& string);
};

std::ostream& operator<<(std::ostream& stream,const String& string)
{
    stream << string.m_Buffer;  //重载‘<<’操作符
}
char& operator[](unsigned int index)
    {
        return m_Buffer[index];
    }
int main()
{
    String string = "Odrin";
    String second = string;    //注意两个对象的m_Buffer指针是一样的,只拷贝看了指针的内容这个过程是“浅拷贝”，两个指针指向同一个内存块
    second[2] = 'a'; //如果我们希望不同时修改第一个字符串的内容时，需要“深拷贝”
    std::cout << string << std::endl;
    std::cout << second << std::endl;
    std::cin.get();
} 
/*注意结果同时修改了两个字符串的内容
odain
odain
*/
```
想要达到“深拷贝”的效果，即复制整个对象，就需要拷贝构造函数
C++默认提供拷贝构造函数但是只有“浅拷贝”的效果
```cpp
//默认拷贝构造函数，只有浅拷贝效果
String(const String& other)
    :m_Buffer(other.m_Buffer),m_Size(other.m_Size)
{
}
```
自己构造一个“深拷贝”拷贝函数
```cpp
String(const String& other)
    :m_Size(other.m_Size)
{
    m_Buffer = new char[m_Size + 1];
    memcpy(m_Buffer,other.m_Buffer,m_Size+1);
}
/*加入上述代码后结果
odrin
odain
*/
```
但是当拷贝函数被多次调用时候，又会有浪费内存的现象
```cpp
//拷贝构造函数中加入了一句
//std::cout << "Copied String!" << std::endl;
void PrintString(String string)
{
    std::cout << string << std::endl;
}
int main()
{
    String string = "Odrin";
    String second = string;
    second[2] = 'a'; //1
    PrintString(string); //2
    PrintString(second); //3
    std::cin.get();
}
/* 拷贝函数被多次调用
Copied String!  //调用发生在1处
Copied String!  //调用发生在2处
Odrin
Copied String!  //调用发生在3处
Odain
*/
```
如何解决这个问题，可以修改```PrintString```函数的参数，通过```const reference```的方式去传递对象
```cpp
void PrintString(const String& string)
{
    std::cout << string << std::endl;
}
/* 
Copied String!
Odrin
Odain
*/
```
如果你想要禁用拷贝构造函数(类似unique_ptr)，可以这样：
```cpp 
String(const String& other) = delete;
```

### C++ 箭头操作符 ->
```cpp
class Entity
{
    void Print() const {}
};
int main()
{
    Entity e;
    Entity* ptr = &e;
    prt -> Print();  //相当于(*ptr).print(); 逆引用指针
}
```

### C++ 动态数组 vector
vector并不是指向量这个字面意思，它更像一个不强制其实际元素有唯一性的集合。
（叫向量的原因可能是因为他的一端可以无限延长）
cherno:使用动态数组存储指针是你最后的选择。
```cpp
#include<iostream>
#include<string>
#include<vector>

struct Vertex
{
    float x,y,z;
};

std::ostream& operator<<(std::ostream& stream,const Vertex& vertex)
{
    stream << vertex.x << "," << vertex.y << "," << vertex.z ;
    return stream;
}
int main()
{
    std::vector<Vertex> vertices;
    vertices.push_back({1,2,3});
    vertices.push_back({4,5,6});
    for(int i = 0; i < vertices.size();i++)
    {
        std::cout << vertices[i] << std::endl;
    }
    vertices.erase(vertices.begin()+1);
    for (Vertex& v : vertices)  //使用引用防止复制，很重要
    {
        std::cout << v << std::endl;
    }
    std::cin.get();
}

```


### C++ std::vector优化 
vector一般比较慢
vector是创建在堆上的数据结构
当vector需要加入新元素，但容量不够的时候，会有一个复制到新内存地址删除旧内存地址的过程。如果不断的重新分配地址就是把代码拖慢的原因。
所以我们优化复制的过程，特别是对vector的对象

以下代码会展示两个优化点：
1.传递复制：从main函数中构造的Vertex复制到Vector开辟的内存中的过程可以优化
2.扩容复制：vector的扩容过程中复制可以优化
```cpp
#include <iostream>
#include <string>
#include <vector>

struct Vertex
{
    float x,y,z;
    
    Vertex(float x,float y,float z)
        : x(x),y(y),z(z)
    {
    }
    Vertex(const Vertex& vertex)
        : x(vertex.x),y(vertex.y),z(vertex.z)
    {
        std::cout << "Copied!" << std::endl;
    }
};

int main()
{
    std::vector<Vertex> vertices;
    vertices.push_back(Vertex(1,2,3)); //copy 1次
    vertices.push_back(Vertex(4,5,6)); //copy 2次
    vertices.push_back(Vertex(7,8,9)); //copy 3次
    
    std::cin.get();
}
/*
Copied!
Copied!
Copied!
Copied!
Copied!
Copied!
*/
```
```reserve()```方法设置vector大小，省去了扩容过程的复制
```emplace_back()```方法，只传递构造函数的参数列表，并不执行，省去从vertex到vector的复制
优化后代码：
```cpp
int main()
{
    std::vector<Vertex> vertices;
    vertices.reserve(5); //优化了第2、3、4、5句扩容过程中产生复制
    vertices.emplace_back(1,2,3); //0 扩容复制次数
    vertices.emplace_back(4,5,6); //1
    vertices.emplace_back(7,8,9); //2
    vertices.emplace_back(10,11,12); //0
    vertices.emplace_back(13,14,15); //4
    std::cin.get();
}
/*
无输出了
*/
```
此外，注意到扩容复制的次数分别是0，1，2，0，4，进行多次实验后发现vector需要扩容时，以2的x次方扩容，x为已经扩容的次数
```cpp
vertices.emplace_back(1,2,3); //0
vertices.emplace_back(4,5,6); //1
vertices.emplace_back(7,8,9); //2
vertices.emplace_back(10,11,12); //0
vertices.emplace_back(13,14,15); //4
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//8
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//16
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0
vertices.emplace_back(13,14,15);//0  
```
### C++ 使用静态库

示例链接二进制库，GLFW Binary Library
GLFW is a open source libray for OpenGL.

库通常包含两部分includes和library，即包含目录和库目录。
包含目录(includes)是一堆头文件，以便于我们可以实际使用预构建的二进制文件中的函数。
库目录(library)有预构建的二进制文件，通常包含两种链接方式，使用动态库或静态库。

静态库文件glfw3.lib

静态链接是在编译时被链接的，意味着这个库会被放进你的可执行文件中，它在你的.exe文件中。
动态链接是在运行时被链接的，所以你仍然有一些链接，选择在程序运行时，装载动态链接库。
比如loadLibrary()函数，你可以在WindowsAPI中使用它，它会载入你的动态库，从中拉出函数，然后开始调用函数。也可以在应用程序启动时加载你的dll文件。 

静态链接在技术上更快，因为编译器或链接器实际上可以执行链接时优化之类的。

cherno:静态库使用过程：头文件提供声明，告诉我们哪些函数是可用的，然后库文件为我们提供了定义，这样我们就可以链接到那些函数，并在C++中调用函数时使用正确的代码。

### C++ 使用动态库 cherno留了个问题

动态库文件:```glfw3dll.lib```、```glfw3.dll```前者是一堆指向后者的指针。

```cpp
#include <iostream>
#include <GLFW/glfw3.h>

int main()
{
    int a = glfwInit(); //需要在VS IDE中去设置Linker-Input-Addtional Dependencies，加入帮我们找dll的glfw3dll.lib
    //并且需要把glfw3.dll文件与.exe文件放在同一目录下，当然这只是个默认设置，不喜欢可以更改。
    std::cout << a  << std::endl;
 
    std::cin.get();
}
```
### C++ 处理多个不同类型的返回
用结构体

### C++ templates
模版的作用，有点类似于元编程meta programming
```cpp
#include<iostream>
#include<string>
template<typename T>  //会在编译期被evaluate（计算）
//typename可以替换为class，在这里是同义词
void Print(T value)
{
    std::cout << value << std::endl;
}
int main()
{
    Print(5);
    Print("hello");
    Print(5.5f); //在调用不同类型的参数时，模版实际上帮你重载了一个构造函数
    std::cin.get();
}
```
模版的小技巧，因为模版会在编译期被evaluate，而声明一个数组需要知道具体的大小，模版可以很好的解决这个问题
```cpp
#include<iostream>
#include<string>
template<typename T,int N>
class Array
{
    private:
        T m_Array[N];
    public:
        int GetSize() const {return N;}
};
int main()
{
    Array<std::string,50> array;
    std::cout << array.GetSize() << std::endl;
    std::cin.get();
}
```

### 栈与堆内存比较
栈通常是一个预定义大小的内存区域，2MB左右
堆也是一个预定义了默认值的区域，但是可以生长
两种内存的实际位置都在我们的内存中

在栈上分配的距离内存更近，他们可以更方便的放入CPU缓存线(CPU Cache的最小单位)上

在堆上分配的，可能会是一些cache miss（CPU要访问的数据，在Cache中有，称为hit，没有称为miss），对于cache miss的情况，通常数量不大，可以忽略不计

两种分配最大的区别，是堆分配的慢

栈分配 汇编
堆分配 汇编

### C++ 宏
宏定义发生在编译前，实际上做的是改变了文本生成的方式。也是一个复制替换的过程

```cpp
#include<iostream>
#include<string>

#define WAIT std::cin.get()
int main()
{
    char a  = WAIT;
    std::cout << a << std::endl;
}
```
一般在记录系统日志的时候使用
项目属性-C/C++-预处理-deBug模式下-预处理定义—PR_DEBUG，可以定义的你在debug模式下想进行的操作，release模式下-预处理定义-PR_RELEASEPR_RELEASE进行另一种操作。
```cpp
#ifdef PR_DEBUG
#define LOG(X) std::cout << x << std::endl;
#else
#define LOG(X)
#endif
```

### C++ auto
自动识别变量类型的关键字
但并不意味着C++是弱类型语言
一般用于类型特别长的情况下，简化书写，自动识别
```cpp
for (std::vector<std::string>::iterator it = strings.begin(); it != strings.end();it++)
{
    std::cout << *it << std::endl;
}
//可以改为
for (auto it = strings.begin(); it != strings.end();it++)
{
    std::cout << *it << std::endl;
}
```

### C++ 静态数组 std::array

std::array和C语言风格的数组哪个好用呢？
std::array vs ide有边界检查（可选）
```cpp
#include<iostream>
#include<array>
int main()
{
    std::array<int,4> data;
    data[5] = 2;   //Visual Studio越界提醒，取决于你的debug level
    int dataOld[5];
    dataOld[5]=0;   //有些编译器会提醒
}
```

### C 函数指针

函数指针是将一个函数赋值给一个变量的方法，目的就是使函数参数化
拓展：也可将一个函数作为参数专递给其他函数

函数其实就是CPU指令，当我们编译代码时，函数就在二进制文件当中的某个地方，函数指针其实就是获取了函数的地址。
```cpp
#include<iostream>
void HelloWorld(int a)
{
    std::cout << "Hello World!" << a << std::endl;
}
int main()
{
    auto function = HelloWorld; //函数名就是地址
    //等同于 auto function = &HelloWorld; 
    function(5); //function类型 void (*)(int)	
    std::cin.get();
}
```
上述函数指针也可以写成
```cpp
int main()
{
    void(*function)(int) = HelloWorld;
    std::cin.get();
}
```
一个实际更体现函数指针作用的例子：
```cpp
#include <iostream>
#include <vector>

void PrintValue(int value)
{
    std::cout << "Value:" << value << std::endl;
}
void ForEach(const std::vector<int>& values,void(*func)(int))
{
    for (int value : values)
        func(value);
}
int main()
{
    std::vector<int> values = {1,5,4,2,3};
    ForEach(values,PrintValue);
    
    std::cin.get();
}
```
### C++ 匿名函数 lambda ----待续

>Cherno:lambda就像是一个快速的一次性函数,展示下需要运行的代码，我们更想将它视为一个变量，而不是更像一个正式的函数那样，在我们实际编译的代码中作为一个符号那样存在。理解如何使用它和何时使用它是完全不同的事情。


### 为什么不使用 #using namespace std？
>Cherno:方便区分是否是C++标准库的内容，因为一些大厂，比如EA，有自己的改造的模版库，叫EASTL，如果不写```std::```，在阅读代码时，如果遇到重名函数，你可能会迷惑它来自哪个库。此外假如不同命名空间可能有同名函数，同时使用这些库会造成调用混乱。

如果你一定要使用```using namespace```，请在一个小区域内。

### C++ namespace
类本身也是一种命名空间
命名空间可以嵌套、赋值
```cpp
using namespace::std;
//使用std
using std::cout;
//只使用std中的cout函数
namespace <#name#> = <#namespace#>;
//赋值
```
>Cherno:我认为如果是一个非常严肃的项目，你应该把代码写在```namespace```后面

### C++ 线程

### C++ 计时
STD chrono库 
windows OS
记录程序实际运行的时间
基准测试

### C++ 编写桌面程序的推荐方法
ImGUI:Dear ImGui is a bloat-free graphical user interface library for C++.