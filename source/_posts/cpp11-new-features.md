---
title: C++11常用特性
tags: [C++, C++11]
---

## 1、关键字及新语法

### 1.1 auto关键字及用法

**A、auto关键字能做什么？**

auto并没有让C++成为弱类型语言，也没有弱化变量什么，只是使用auto的时候，编译器根据上下文情况，确定auto变量的真正类型。

```c++
auto AddTest(int a, int b)
{
    return a+b;
}

int main()
{
    auto index = 10;
    auto str = "abc";
    auto ret = AddTest(1, 2);
    std::cout << "index:" << index << std::endl;
    std::cout << "str:" << str << std::endl;
    std::cout << "res:" << ret << std::endl;
}
```

**B、auto不能做什么？**

auto作为函数返回值时，只能用于定义函数，不能用于声明函数。

```c++
#pragma once
class Test
{
public:
    auto TestWork(int a ,int b);
};
```

如下函数中，在引用头文件的调用TestWork函数是，编译无法通过。

但如果把实现写在头文件中，可以编译通过，因为编译器可以根据函数实现的返回值确定auto的真实类型。如果读者用过inline类成员函数，这个应该很容易明白，此特性与inline类成员函数类似。

```c++
#pragma once
class Test
{
public:
    auto TestWork(int a, int b)
    {
        return a + b;
    }
};
```

### 1.2 nullptr关键字及用法

为什么需要nullptr? NULL有什么毛病？

```c++
class Test
{
public:
    void TestWork(int index)
    {
        std::cout << "TestWork 1" << std::endl;
    }
    void TestWork(int * index)
    {
        std::cout << "TestWork 2" << std::endl;
    }
};

int main()
{
    Test test;
    test.TestWork(NULL);
    test.TestWork(nullptr);
}
```

运行结果：

```
TestWork 1
TestWork 2
```

NULL在c++里表示空指针，看到问题了吧，我们调用test.TestWork(NULL)，其实期望是调用的是void TestWork(int * index)，但结果调用了void TestWork(int index)。但使用nullptr的时候，我们能调用到正确的函数。

### 1.3 for循环语法

OK，直接以简单示例看看用法吧。

```c++
int main()
{
    int numbers[] = { 1,2,3,4,5 };
    std::cout << "numbers:" << std::endl;
    for (auto number : numbers)
    {
        std::cout << number << std::endl;
    }
}
```

以上用法不仅仅局限于数据，STL容器都同样适用。

## 2、STL容器

总结C++11新增的一些容器，以及对其实现做一些简单的解释。

### 2.1 std::array

std::array跟数组并没有太大区别，std::array相对于数组，增加了迭代器等函数（接口定义可参考C++官方文档）。

```c++
#include <array>
int main()
{
    std::array<int, 4> arrayDemo = { 1,2,3,4 };
    std::cout << "arrayDemo:" << std::endl;
    for (auto itor : arrayDemo)
    {
        std::cout << itor << std::endl;
    }
    int arrayDemoSize = sizeof(arrayDemo);
    std::cout << "arrayDemo size:" << arrayDemoSize << std::endl;
    return 0;
}
```

### 2.2 std::forward_list

std::forward_list为C++新增的线性表，与list区别在于它是单向链表。我们在学习数据结构的时候都知道，链表在对数据进行插入和删除是比顺序存储的线性表有优势，因此在插入和删除操作频繁的应用场景中，使用list和forward_list比使用array、vector和deque效率要高很多。

```c++
#include <forward_list>
int main()
{
    std::forward_list<int> numbers = {1,2,3,4,5,4,4};
    std::cout << "numbers:" << std::endl;
    for (auto number : numbers)
    {
        std::cout << number << std::endl;
    }
    numbers.remove(4);
    std::cout << "numbers after remove:" << std::endl;
    for (auto number : numbers)
    {
        std::cout << number << std::endl;
    }
    return 0;
}
```

### 2.3 std::unordered_map

std::unordered_map与std::map用法基本差不多，但STL在内部实现上有很大不同，std::map使用的数据结构为二叉树，而std::unordered_map内部是哈希表的实现方式，哈希map理论上查找效率为O(1)。但在存储效率上，哈希map需要增加哈希表的内存开销。

下面代码为C++官网实例源码实例：

```c++
#include <iostream>
#include <string>
#include <unordered_map>
int main()
{
    std::unordered_map<std::string, std::string> mymap =
    {
        { "house","maison" },
        { "apple","pomme" },
        { "tree","arbre" },
        { "book","livre" },
        { "door","porte" },
        { "grapefruit","pamplemousse" }
    };
    unsigned n = mymap.bucket_count();
    std::cout << "mymap has " << n << " buckets.\n";
    for (unsigned i = 0; i<n; ++i) 
    {
        std::cout << "bucket #" << i << " contains: ";
        for (auto it = mymap.begin(i); it != mymap.end(i); ++it)
            std::cout << "[" << it->first << ":" << it->second << "] ";
        std::cout << "\n";
    }
    return 0;
}
```

### 2.4 std::unordered_set

std::unordered_set的数据存储结构也是哈希表的方式结构，除此之外，std::unordered_set在插入时不会自动排序，这都是std::set表现不同的地方。

```c++
#include <iostream>
#include <string>
#include <unordered_set>
#include <set>
int main()
{
    std::unordered_set<int> unorder_set;
    unorder_set.insert(7);
    unorder_set.insert(5);
    unorder_set.insert(3);
    unorder_set.insert(4);
    unorder_set.insert(6);
    std::cout << "unorder_set:" << std::endl;
    for (auto itor : unorder_set)
    {
        std::cout << itor << std::endl;
    }

    std::set<int> set;
    set.insert(7);
    set.insert(5);
    set.insert(3);
    set.insert(4);
    set.insert(6);
    std::cout << "set:" << std::endl;
    for (auto itor : set)
    {
        std::cout << itor << std::endl;
    }

}
```

## 3、多线程

在C++11以前，C++的多线程编程均需依赖系统或第三方接口实现，一定程度上影响了代码的移植性。C++11中，引入了boost库中的多线程部分内容，形成C++标准，形成标准后的boost多线程编程部分接口基本没有变化，这样方便了以前使用boost接口开发的使用者切换使用C++标准接口，把容易把boost接口升级为C++接口。

### 3.1 std::thread

std::thread为C++11的线程类，使用方法和boost接口一样，非常方便，同时，C++11的std::thread解决了boost::thread中构成参数限制的问题，我想着都是得益于C++11的可变参数的设计风格。

我们通过如下代码熟悉下std::thread使用风格。

```c++
#include <thread>
void threadfun1()
{
    std::cout << "threadfun1 - 1\r\n" << std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout << "threadfun1 - 2" << std::endl;
}

void threadfun2(int iParam, std::string sParam)
{
    std::cout << "threadfun2 - 1" << std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(5));
    std::cout << "threadfun2 - 2" << std::endl;
}

int main()
{
    std::thread t1(threadfun1);
    std::thread t2(threadfun2, 10, "abc");
    t1.join();
    std::cout << "join" << std::endl;
    t2.detach();
    std::cout << "detach" << std::endl;
}
```

t1.join()会等待t1线程退出后才继续往下执行，t2.detach()并不会并不会把，detach字符输出后，主函数退出，threadfun2还未执行完成，但是在主线程退出后，t2的线程也被已经被强退出。

### 3.2 std::atomic

std::atomic为C++11分装的原子数据类型。

什么是原子数据类型？

从功能上看，简单地说，原子数据类型不会发生数据竞争，能直接用在多线程中而不必我们用户对其进行添加互斥资源锁的类型。从实现上，大家可以理解为这些原子类型内部自己加了锁。

我们下面通过一个测试例子说明原子类型std::atomic_int的特点。

下面例子中，我们使用10个线程，把std::atomic_int类型的变量iCount从100减到1。

```c++
#include <thread>
#include <atomic>
#include <stdio.h>
std::atomic_bool bIsReady = false;
std::atomic_int iCount = 100;
void threadfun1()
{
    if (!bIsReady) {
        std::this_thread::yield();
    }
    while (iCount > 0)
    {
        printf("iCount:%d\r\n", iCount--);
    }
}

int main()
{
    std::atomic_bool b;
    std::list<std::thread> lstThread;
    for (int i = 0; i < 10; ++i)
    {
        lstThread.push_back(std::thread(threadfun1));
    }
    for (auto& th : lstThread)
    {
        th.join();
    }
}
```

### 3.3 std::condition_variable

C++11中的std::condition_variable就像Linux下使用pthread_cond_wait和pthread_cond_signal一样，可以让线程休眠，直到别唤醒，现在在从新执行。线程等待在多线程编程中使用非常频繁，经常需要等待一些异步执行的条件的返回结果。

```c++
#include <iostream>           // std::cout
#include <thread>             // std::thread
#include <mutex>              // std::mutex, std::unique_lock
#include <condition_variable> // std::condition_variable

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void print_id(int id) {
    std::unique_lock<std::mutex> lck(mtx);
    while (!ready) cv.wait(lck);
    // ...
    std::cout << "thread " << id << '\n';
}

void go() {
    std::unique_lock<std::mutex> lck(mtx);
    ready = true;
    cv.notify_all();
}

int main()
{
    std::thread threads[10];
    // spawn 10 threads:
    for (int i = 0; i<10; ++i)
        threads[i] = std::thread(print_id, i);

std::cout << "10 threads ready to race...\n";
go();                       // go!

for (auto& th : threads) th.join();

return 0;

}
```

上面的代码，在14行中调用cv.wait(lck)的时候，线程将进入休眠，在调用33行的go函数之前，10个线程都处于休眠状态，当22行的cv.notify_all()运行后，14行的休眠将结束，继续往下运行，最终输出如上结果。

## 4、智能指针内存管理

在内存管理方面，C++11的std::auto_ptr基础上，移植了boost库中的智能指针的部分实现，如std::shared_ptr、std::weak_ptr等，当然，像boost::thread一样，C++11也修复了boost::make_shared中构造参数的限制问题。

什么是智能指针？网上已经有很多解释，个人觉得“智能指针”这个名词似乎起得过于“霸气”，很多初学者看到这个名词就觉得似乎很难。
简单地说，智能指针只是用对象去管理一个资源指针，同时用一个计数器计算当前指针引用对象的个数，当管理指针的对象增加或减少时，计数器也相应加1或减1，当最后一个指针管理对象销毁时，计数器为1，此时在销毁指针管理对象的同时，也把指针管理对象所管理的指针进行delete操作。

### 4.1、std::shared_ptr

std::shared_ptr包装了new操作符动态分配的内存，可以自由拷贝复制，基本上是使用最多的一个智能指针类型。

```c++
#include <memory>
class Test
{
public:
    Test()
    {
        std::cout << "Test()" << std::endl;
    }
    ~Test()
    {
        std::cout << "~Test()" << std::endl;
    }
};
int main()
{
    std::shared_ptr<Test> p1 = std::make_shared<Test>();
    std::cout << "1 ref:" << p1.use_count() << std::endl;
    {
        std::shared_ptr<Test> p2 = p1;
        std::cout << "2 ref:" << p1.use_count() << std::endl;
    }
    std::cout << "3 ref:" << p1.use_count() << std::endl;
    return 0;
}
```

运行结果：

```
Test()
1 ref:1
2 ref:2
3 ref:1
~Test()
```

上面代码需要了解的是：

1. std::make_shared封装了new方法，boost::make_shared之前的原则是既然释放资源delete由智能指针负责，那么应该把new封装起来，否则会让人觉得自己调用了new，但没有调用delete，似乎与谁申请，谁释放的原则不符。C++也沿用了这一做法。
2. 随着引用对象的增加std::shared_ptr<Test> p2 = p1，指针的引用计数有1变为2，当p2退出作用域后，p1的引用计数变回1，当main函数退出后，p1离开main函数的作用域，此时p1被销毁，当p1销毁时，检测到引用计数已经为1，就会在p1的析构函数中调用delete之前std::make_shared创建的指针。

### 4.2 std::weak_ptr

std::weak_ptr网上很多人说其实是为了解决std::shared_ptr在相互引用的情况下出现的问题而存在的，C++官网对这个只能指针的解释也不多，那就先甭管那么多了，让我们暂时完全接受这个观点。

std::weak_ptr有什么特点呢？与std::shared_ptr最大的差别是在赋值时，不会引起智能指针计数增加。
我们下面将继续如下两点：

1. std::shared_ptr相互引用会有什么后果；
2. std::weak_ptr如何解决第一点的问题。

**A、std::shared_ptr相互引用的问题**

示例：

```c++
#include <memory>
class TestB;
class TestA
{
public:
    TestA()
    {
        std::cout << "TestA()" << std::endl;
    }
    void ReferTestB(std::shared_ptr<TestB> test_ptr)
    {
        m_TestB_Ptr = test_ptr;
    }
    ~TestA()
    {
        std::cout << "~TestA()" << std::endl;
    }
private:
    std::shared_ptr<TestB> m_TestB_Ptr; //TestB的智能指针
}; 

class TestB
{
public:
    TestB()
    {
        std::cout << "TestB()" << std::endl;
    }

    void ReferTestB(std::shared_ptr<TestA> test_ptr)
    {
        m_TestA_Ptr = test_ptr;
    }
    
    ~TestB()
    {
        std::cout << "~TestB()" << std::endl;
    }
    
    std::shared_ptr<TestA> m_TestA_Ptr; //TestA的智能指针

};

int main()
{
    std::shared_ptr<TestA> ptr_a = std::make_shared<TestA>();
    std::shared_ptr<TestB> ptr_b = std::make_shared<TestB>();
    ptr_a->ReferTestB(ptr_b);
    ptr_b->ReferTestB(ptr_a);
    return 0;
}
```

运行结果：

```
TestA()
TestB()
```

上面代码中，我们创建了一个TestA和一个TestB的对象，但在整个main函数都运行完后，都没看到两个对象被析构，这是什么问题呢？
原来，智能指针ptr_a中引用了ptr_b，同样ptr_b中也引用了ptr_a，在main函数退出前，ptr_a和ptr_b的引用计数均为2，退出main函数后，引用计数均变为1，也就是相互引用。

这等效于说：

> 1. ptr_a对ptr_b说，哎，我说ptr_b，我现在的条件是，你先释放我，我才能释放你，这是天生的，造物者决定的，改不了。
> 2. ptr_b也对ptr_a说，我的条件也是一样，你先释放我，我才能释放你，怎么办？

是吧，大家都没错，相互引用导致的问题就是释放条件的冲突，最终也可能导致内存泄漏。

**B、std::weak_ptr如何解决相互引用的问题**

```c++
#include <memory>
class TestB;
class TestA
{
public:
    TestA()
    {
        std::cout << "TestA()" << std::endl;
    }
    void ReferTestB(std::shared_ptr<TestB> test_ptr)
    {
        m_TestB_Ptr = test_ptr;
    }
    void TestWork()
    {
        std::cout << "~TestA::TestWork()" << std::endl;
    }
    ~TestA()
    {
        std::cout << "~TestA()" << std::endl;
    }
private:
    std::weak_ptr<TestB> m_TestB_Ptr;
};

class TestB
{
public:
    TestB()
    {
        std::cout << "TestB()" << std::endl;
    }

void ReferTestB(std::shared_ptr<TestA> test_ptr)
{
    m_TestA_Ptr = test_ptr;
}
void TestWork()
{
    std::cout << "~TestB::TestWork()" << std::endl;
}
~TestB()
{
    std::shared_ptr<TestA> tmp = m_TestA_Ptr.lock();
    tmp->TestWork();
    std::cout << "2 ref a:" << tmp.use_count() << std::endl;
    std::cout << "~TestB()" << std::endl;
}
std::weak_ptr<TestA> m_TestA_Ptr;

};

int main()
{
    std::shared_ptr<TestA> ptr_a = std::make_shared<TestA>();
    std::shared_ptr<TestB> ptr_b = std::make_shared<TestB>();
    ptr_a->ReferTestB(ptr_b);
    ptr_b->ReferTestB(ptr_a);
    std::cout << "1 ref a:" << ptr_a.use_count() << std::endl;
    std::cout << "1 ref b:" << ptr_a.use_count() << std::endl;
    return 0;
}
```

运行结果：

```
TestA()
TestB()
1 ref a:1
1 ref b:1
~TestA::TestWork()
2 ref a:2
~TestB()
~TestA()
```

由运行结果，可以看到：

1. 所有的对象最后都能正常释放，不会存在上一个例子中的内存没有释放的问题。
2. ptr_a和ptr_b在main函数中退出前，引用计数均为1，也就是说，在TestA和TestB中对std::weak_ptr的相互引用，不会导致计数的增加。在TestB析构函数中，调用std::shared_ptr<TestA> tmp = m_TestA_Ptr.lock()，把std::weak_ptr类型转换成std::shared_ptr类型，然后对TestA对象进行调用。

## 5、其他

### 5.1、std::function、std::bind封装可执行对象

std::bind和std::function也是从boost中移植进来的C++新标准，这两个语法使得封装可执行对象变得简单而易用。此外，std::bind和std::function也可以结合我们一下所说的lamda表达式一起使用，使得可执行对象的写法更加“花俏”。

通过实例一步步了解std::function和std::bind的用法：

```c++
// Test.h
class Test
{
public:
    void Add()
    { }

};

//main.cpp
#include <functional>
#include <iostream>
#include "Test.h"
int add(int a,int b)
{
    return a + b;
}

int main()
{
    Test test;
    test.Add();
    return 0;
}
```

解释：

- 上面代码中，我们实现了一个add函数和一个Test类，Test类里面有一个Test函数也有一个函数Add。


OK，我们现在来考虑一下这个问题，假如我们的需求是让Test里面的Add由外部实现，如main.cpp里面的add函数，有什么方法呢？
没错，我们可以用函数指针。

修改Test.h

```c++
class Test
{
public:
    typedef int(*FunType)(int, int);
    void Add(FunType fun,int a,int b)
    {
        int sum = fun(a, b);
        std::cout << "sum:" << sum << std::endl;
    }
};
```

修改main.cpp的调用

```c++
....
....
Test test;
test.Add(add, 1, 2);
....
```

运行结果：

```
sum:3
```

到现在为止，完美了吗？

我们把问题升级，假如add实现是在另外一个类内部，如下代码：

```c++
class TestAdd
{
public:
    int Add(int a,int b)
    {
        return a + b;
    }
};

int main()
{
    Test test;
    //test.Add(add, 1, 2);
    return 0;
}
```

假如add方法在TestAdd类内部，那你的Test类没辙了，因为Test里的Test函数只接受函数指针。你可能说，这个不是我的问题啊，我是接口的定义者，使用者应该遵循我的规则。但如果现在我是客户，我们谈一笔生意，就是我要购买使用你的Test类，前提是需要支持我传入函数指针，也能传入对象函数，你做不做这笔生意？

是的，你可以选择不做这笔生意。我们现在再假设你已经好几个月没吃肉了（别跟我说你是素食主义者），身边的苍蝇肉、蚊子肉啊都不被你吃光了，好不容易等到有机会吃肉，那有什么办法呢？
这个时候std::function和std::bind就帮上忙了。

```c++
// Test.h
class Test
{
public:
    void Add(std::function<int(int, int)> fun, int a, int b)
    {
        int sum = fun(a, b);
        std::cout << "sum:" << sum << std::endl;
    }
};
```

解释：

- Test类中std::function<int(int,int)>表示std::function封装的可执行对象返回值和两个参数均为int类型。


```c++
// main.cpp
int add(int a,int b)
{
    std::cout << "add" << std::endl;
    return a + b;
}

class TestAdd
{
public:
    int Add(int a,int b)
    {
        std::cout << "TestAdd::Add" << std::endl;
        return a + b;
    }
};

int main()
{
    Test test;
    test.Add(add, 1, 2);

    TestAdd testAdd;
    test.Add(std::bind(&TestAdd::Add, testAdd, std::placeholders::_1, std::placeholders::_2), 1, 2);
    return 0;

}
```

解释：

- std::bind第一个参数为对象函数指针，表示函数相对于类的首地址的偏移量；

- testAdd为对象指针；

- std::placeholders::_1和std::placeholders::_2为参数占位符，表示std::bind封装的可执行对象可以接受两个参数。


运行结果：

```
add
sum:3
TestAdd::Add
sum:3
```



### 5.2 lamda表达式

在众多的C++11新特性中，个人觉得lamda表达式不仅仅是一个语法新特性，对于没有用过java或C#lamda表达式读者，C++11的lamda表达式在一定程度上还冲击着你对传统C++编程的思维和想法。

先从一个简单的例子来看看lamda表达式：

```
int main()
{
    auto add= [](int a, int b)->int{
        return a + b;
    };
    int ret = add(1,2);
    std::cout << "ret:" << ret << std::endl;
    return 0;
}
```

解释：

- 第3至5行为lamda表达式的定义部分

- []：中括号用于控制main函数与内，lamda表达式之前的变量在lamda表达式中的访问形式；

- int a,int b）：为函数的形参

- ->int：lamda表达式函数的返回值定义

- {}:大括号内为lamda表达式的函数体。


运行结果：

```
ret:3
```

使用lamda表达式修改5.1中的例子看看：

```c++
int main()
{
    Test test;
    test.Add(add, 1, 2);

    TestAdd testAdd;
    test.Add(std::bind(&TestAdd::Add, testAdd, std::placeholders::_1, std::placeholders::_2), 1, 2);

    test.Add([](int a, int b)->int {
        std::cout << "lamda add fun" << std::endl;
        return a + b;
    },1,2);
    
    return 0;
}
```

运行结果：

```
add
sum:3
TestAdd::Add
sum:3
lamada add fun
sum:3
```

