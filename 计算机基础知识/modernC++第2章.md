# Modern C++ 第2章 语言可用性的强化
## 2.1 常量
### nullptr
`NULL`表示空指针，其值定义为0。重载时由于不能区分`NULL`是空指针还是整数0，因此容易歧义。C++11引入了`nullptr`来解决这个问题。`nullptr`是一个关键字，其类型是`std::nullptr_t`，可以用来代替`NULL`。
### constexpr
`constexpr`修饰常量表达式，真正实现了编译期间的常量。与`const`相比，`const` 是运行时的常量，而`constexpr` 是编译期间的常量。`constexpr`修饰的变量可以用于定义数组大小等等。而`const`不能。而且 `constexpr`在编译期间就完成常量计算，因此可以避免运行时开销。
## 2.2 变量及其初始化
### if/switch 变量声明强化
以前只能在 `for` 循环中声明变量，现在在 `if` 和 `switch` 语句中也可以声明变量。
```c++
if (int x = 10) { // 声明变量并初始化
    // 使用 x
}
```
### 初始化列表
统一变量初始化语法，类，数组等可以用`{}`初始化
### 结构化绑定
`bind `方便获取复杂结构中的变量。
```c++
auto [x, y] = someStruct{1, 2}; // 结构化绑定
```
## 2.3 类型推导
### auto
方便声明变量，比如迭代器等太长的变量名。
### decltype
与`auto`相比，`decltype`可以获取表达式的类型。
```c++
int x,y;
decltype(x+y) z; // z的类型是int
```
### 尾返回类型推导
利用 `auto`而不是`decltype`。
```c++
auto f(int x){}; // 尾返回类型推导
decltype(x) f(int x){}; // 错误，不能推导尾返回类型,因为x还未声明
```
## 2.4 控制流
### if constexpr
`if constexpr` 类似于 `if` 语句，但在编译期间进行判断，可以避免运行时开销。但是表达式必须是常量表达式。
```c++
if constexpr (x > 10) {
    // 代码
}
```
### 区间for 迭代
简洁迭代区间。
```c++
for (int i : {1, 2, 3}) {
    // 代码
}
```
## 2.5 模板
### 外部模板
避免在多个编译单元中重复定义模板。从而减少编译时间。
```c++
extern template class vector<int>; // 声明模板
```
### 尖括号 `>`
`>>`以前只表示右移运算符，现在可以表示嵌套模板。
```c++
vector<vector<int>> v; // 声明一个二维向量
```
### 类型别名模板
`typedef`可以为类型定义别名，但是不能为模板定义别名。因此引入`using`。
```c++
template<typename T>
typedef MagicType<T,int> MyType; // 不合法
using MagicType = MagicType<T,int>; // 合法
```
### 变长参数模板
```c++
template<typename... Args>
void f(Args... args){
    cout<<sizeof...(args)<<endl; // 打印参数个数
}
```
对参数进行解包，有两种方法。
1.递归
2.变参模板展开
```c++
template<typename T0,typename... Args>
void print(T0 t0,Args... args)
{
    cout<<t0<<endl;
    if constexpr(sizeof...(args)>0)
    print(args...); // 递归调用
}
```
3.初始化列表展开
1 和 2 需要定义终止条件，而 3 则不需要。
```c++

```
### 折叠表达式
对可变长度的模板参数进行计算。例如求和。
```c++
template<typename... Args>
int sum(Args... args)
{
    return (args+...);
}
cout<<sum(1,2,3)<<endl; // 6
```
## 2.6 面向对象
### 委托构造
类中的一个构造函数可以委托给另一个构造函数,从而简化代码。
### 继承构造
子类可以继承父类的构造函数，从而简化代码。
### 显式虚函数重载
`ovrride`关键字可以显式重载虚函数，从而避免意外重载虚函数。
`final`关键字可以防止虚函数被重载。
### 显式禁用默认函数
`=default` 显式声明默认构造函数，`=delete` 显式禁用默认构造函数。
```c++
class A{
public:
    A() = default; // 显式声明默认构造函数
    A(int x) = delete; // 显式禁用默认构造函数
};
```
### 强类型枚举
`enum` 枚举类型被视作整数，会让两种完全不同的枚举类型相互比较。而 `enum class` 枚举类型则是强类型的枚举类型，实现类型安全。
