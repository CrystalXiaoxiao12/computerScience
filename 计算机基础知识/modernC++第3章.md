# Modern C++ 第三章 语言运行期的强化
## 3.1 Lambda 表达式
### 基础
匿名函数，不显示定义函数名字。在传递函数参数时使用，可以简化代码。例如 
```c++
sort(v.begin(), v.end(), [](int a, int b) { return a < b; });
```
`[]`捕获外部变量时，分为`[=]`值捕获，`[&]`引用捕获。
### 泛型Lambda
`auto`用于lambda函数参数时，可以自动推导参数类型。从而实现泛型Lambda。例如
```c++
auto f = [](auto x) { return x * x; };
```
## 3.2 函数对象包装器
### function
`function` 包装多种函数，提供统一的接口。用于函数指针。例如
```c++
function<int(int, int)> f = [](int a, int b) { return a + b; };
```
### bind 和 placeholders
`bind` 用于绑定参数，`placeholders` 用于占位符。当函数参数个数不确定时，可以用占位符代替。例如
```c++
auto add=[&](int a, int b) { return a + b; };
auto add2 = bind(add, _1, 2); // 对第一个函数进行占位，第二个参数为2
add2(3); // 输出5
```
## 3.3 右值引用
### 左值、右值的纯右值、将亡值、右值
左值是长期存在的对象，在赋值表达式左边，可以取地址。例如： 普通变量。
右值是临时存在的对象，在赋值表达式右边，不能取地址。例如： 字面值、表达式、函数返回值。
```c++
int x=10;// 普通变量：左值
int y = x+1;// 表达式：右值
string s = "hello";// 字面值：右值
class myclass{};
myclass mc=myclass(); // 临时对象：右值

"hello"="word";// 错误：右值不能被赋值
x+1=20;// 错误：右值不能被赋值
myclass() = nullptr;// 错误：右值不能被赋值
```
右值包括纯右值、将亡值。
### 右值引用和左值引用
右值引用用于移动语义中，减少拷贝。
### 移动语义
资源移动时（例如拷贝构造函数），通常是分配新内存，复制资源到新内存中，然后释放旧内存。移动语义可以避免新内存分配和旧内存释放，实现资源的移动。
```c++
class myclass{
public:
    myclass(myclass& other){// 拷贝构造函数
        this->data = new int[100]; // 分配新内存
        copy(other.data); // 拷贝资源到新内存
    };
    myclass(myclass&& other){ // 移动构造函数
        this->data = other.data; // 指针指向目标数据
        other.data = nullptr; // 目标数据置空,防止析构时释放
        }
```
