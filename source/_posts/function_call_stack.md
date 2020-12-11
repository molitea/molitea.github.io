---
title: 函数调用的栈变化和布局
tags: stack
---

函数调用的栈变化和布局情况：

1. 栈生长方向是高地址到低地址生长；
2. 进入函数中时，执行`push ebp; mov ebp, esp`初始化ebp寄存器为栈底；
3. `sub esp, xxx`为函数内的局部变量分配空间，并使栈顶寄存器esp向低地址移动（生长）；
4. 从上到下，从低地址到高地址，整个函数调用过程中的栈布局是：局部变量、原始调用者的ebp，函数返回地址，函数参数；
5. 使用ebp，esp寄存器访问参数的情况：ebp+xx，esp+xx；
6. 使用ebp，esp寄存器访问局部变量的情况：ebp-xx，esp+xx，esp-xx；
7. 栈溢出时，从上到下，从低地址向高地址溢出，最终覆盖了位于下方高地址的函数返回地址（ret-address）。



![](https://gitee.com/molitea/blog-images/raw/master/img/20201211103735.png)