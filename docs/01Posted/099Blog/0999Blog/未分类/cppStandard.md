## cpp11特性

具体列表和特性： see [This](https://en.cppreference.com/w/cpp/11).

### 弃用

```c++
char *str = "hey"; // 弃用警告
正确：
    const char *str = "hey";
    auto str = "hey";
```

```c++
unexpected_handler
set_unexpected()
noexcept
```

* auto_ptr 被弃用，应使用 unique_ptr

* bool 类型的 ++ 操作被弃用。

* 如果一个类有析构函数，为其生成拷贝构造函数和拷贝赋值运算符的特性被弃用了。

* C 语言风格的类型转换被弃用（即在变量前使用 (convert_type)），应该使用 static_cast、reinterpret_cast、const_cast 来进行类型转换。

* 最新的 C++17 标准中弃用了一些可以使用的 C 标准库，例如 `<ccomplex>、<cstdalign>、<cstdbool> 与 <ctgmath>` 等

### cpp11语言特性

#### `nullptr`

部分编译器将NULL定义为`((void*)0)`，部分直接是0
前者从c++禁止`void*`向其他类型的隐式转换可以推导出`char* buf = NULL`不符合语法。
后者从重载角度，会导致`int foo(char* buf)`调用`foo(NULL)`时调用重载的`int foo(int size)`。
为了进行区分，将nullptr用以空指针,0就是0 。

#### `auto` and `decltype`

`auto` 原见于`auto int`,与`register`对应指示一个变量的建议存储类型，但其实没什么实际功用。
`register`弃用后，其语义变动，成为类型推导的一个关键字。
基本应付的场景如下：
```c++
for(vector<int>::const_iterator it = vec.cbegin(); itr != vec.cend(); ++it)
```
具体使用场景如下：
```c++
auto it = vec.cbegin();
auto i = 5;
auto arr = new auto(10);
```

`decltype` 为解决`auto`只对变量类型进行类型推导而提出，与`typeof`类似，比如这样使用：`decltype (1) x;`
尾返回：为了解决模板的返回值推导，c++11出现如下代码：
```c++11
template<typename T, typename U>
auto add2(T x, U y) -> decltype(x+y){
    return x + y;
}
```

#### `default and deleted functions`

问题：
1.禁用copy则必须`private`拷贝构造和赋值运算符。
2.同时想要使用默认构造函数和自定义。
方案如下：
```c++
class Magic {
    public:
    Magic() = default; // 显式声明使用编译器生成的构造
    Magic& operator=(const Magic&) = delete; // 显式声明拒绝编译器生成构造
    Magic(int magic_number);
}
```

#### `final` & `override`

### cpp11库特性
