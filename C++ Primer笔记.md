# 第二章 变量和基本类型
## 2.4 const限定符
### 顶层const
顶层（top-level）const： 指针本身是一个常量
底层（low-level）const： 指针所指对象是一个常量
```cpp
const int ci = 42;
const int *p2 = &ci; //允许改p2的值，这是low-level
const int *const p3 = p2; //第一个是底层，第二个是顶层
const int &r = ci; //用于引用的const都是底层
int *p = p3; // error p3包括底层const的定义，p没有
```
### constexpr和常量表达式
**常量表达式**表示值不会改变，再编译过程就可以得到的计算结果。
```cpp
const int ci = 42; //is a const expression

int i;    
cin >> i;
const int size = i; // not a const expression
```
C++11 使用 `constexpr` 类型让编译器验证变量是否是一个**常量表达式**。
和`const`相比`constexpr`可以用来修饰变量、函数、构造函数。一旦以上任何元素被`constexpr`修饰，那么等于说是告诉编译器 “请大胆地将我看成编译时就能得出常量值的表达式去优化我”。
C++11中的constexpr指定的函数返回值和参数必须要保证是字面值，而且必须有且只有一行return代码，这给函数的设计者带来了更多的限制，比如通常只能通过return 三目运算符+递归来计算返回的字面值。而C++14中只要保证返回值和参数是字面值就行了，函数体中可以加入更多的语句，方便了更灵活的计算。
**使用constexpr可以提高效率**
## 2.5 处理类型 dealing with types
### 类型别名（type alias）
We can define a types alias in one of two ways.Traditionally,we use a typedef：
```cpp
typedef double wages; // wagesis a synonym同义词 for double
```
C++11 introduced a second way to define a type alias, via an **alias declaration** (别名声明):
```cppS
using SI = Sales_item; // SI is synonym for Sales_item
```
Start with the keyword `using` followed by the alias name and an `=`

### The `auto` Type Specifier 类型说明符
we can let the compilerfigure out the type for us by using the `auto` type specifier. By implication(显然), a variable that uses `auto` as its type specifier must have an initializer:
```cpp
// the type of itemis deduced from(推断出) the type of 
// the result of adding val1 and val2
auto item = val1 + val2; //iteminitialized to the result ofval1 + val2
```
Because a declaration can involve(卷入 涉及) only a single base type, the initializers for all the variables in the declaration must have types that are consistent with(符合；与…一致) each other:
```cpp
auto sz = 0, pi = 3.14; // error: inconsistent types for sz and pi
```
When we use a reference as an initializer, the initializer is the corresponding(相对的相应的) object. The compiler uses that object’s type for `auto`’stype deduction：
```cpp
int i = 0, &r = i;auto a = r;// a is an int (r is an alias for i, which has type int)
```
Second, `auto` ordinarily ignores top-level consts and keeps low-level consts(eg: initializer is a pointer to const)
```cpp
const int ci = i, &cr = ci;
auto b = ci; // b is an int(top-level const in ci is dropped)
auto c = cr; // c is an int(cr is an alias for ci whose const is top-level)
auto d = &i; // d is an int*(& of an int object is int*)
auto e = &ci; // e is const int*(& of a const object is low-level const)

// deduced type to have a top-level const
const auto f = ci; // deduced type of ci is int; f has type const int
```
```cpp
auto &h = 42; // error: we can't bind a plain(平的 普通的) reference to a literal(字面值常量) 
const auto &j = 42; // ok: we can bind a const reference to a literal
```
When we define several variables in the same statement, it is important to remember that a reference or pointer is part of **a particular declarator**(特定声明府) and not part of the base type for the declaration(基本声明类型). 
```cpp
auto k = ci, &l = i; // k is int; l is int& 
auto &m = ci, *p = &ci; // m is a const int&; p is a pointer to const int
 
auto &n = i, *p2 = &ci; // error: type deduced from i is int; 
                        // type deduced from &ci is const int
```
### The `decltype` Type Specifier 类型指示符
 `decltype` returns the type of its operand(操作数/对象).
```cpp
 decltype(f()) sum = x; // sum has whatever type f returns
                        // sum的类型就是 f 的返回类型
```
The compiler does not call f, but it uses the type that such a call would return as the type for sum. _such ... as(这样的……作为)_

When the expression to which we apply `decltype` is a variable, decltype returns the type of that variable, including top-level const and references:
```cpp
const int ci = 0, &cj = ci;
decltype(ci) x = 0; // x has type const int
decltype(cj) y = x; // y has typ econst int& and is bound to x 
decltype(cj) z; // error:z is a reference and must be initialized
```
The **dereference operator**(*) is an example of an expression for which `decltype` returns a reference. As we’ve seen, when we dereference a pointer, we get the object to which the pointer points. 
Thus, the type deduced by decltype(*p) is int&, not plain int.
```cpp
int i = 42, *p = &i, &r = i;
decltype(r + 0) b; // ok: addition yields an int; b is an (uninitialized(未初始化)) int
decltype(*p) c; // error:c is int& and must be initialized
```
Enclosing(封入) the name of a variable in parentheses(括号) affects the type returned by `decltype`. A variable is an expression that can be the left-hand side(左值) of an assignment(赋值语句).As aresult, decltype on such an expression yields(产生) a reference:
```cpp
//decltype of a parenthesized(带括号的) variable is always a reference
decltype((i)) d; // error: d is int& and must be initialized
decltype(i) e; // ok: e is an (uninitialized) int
```
`decltype((variable))` (note, double parentheses) is **always a reference type**, but `decltype(variable)` is a reference type only if variableis a reference.
