# 第二章 变量和基本类型
## const限定符
### 顶层const
顶层（top-level）const： 指针本身是一个常量
底层（low-level）const： 指针所指对象是一个常量
```cpp
const int ci = 42;
const int *p2 = &ci; //允许改p2的值，这是low-level
const int *const p3 = p2; //第一个是底层，第二个是顶层
const int &r = ci; //用于引用的const都是底层
```