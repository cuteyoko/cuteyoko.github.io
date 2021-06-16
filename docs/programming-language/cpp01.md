# 从零开始c++： 第一个程序


## 例程

创建hello.cpp文件，写下我们的第一个程序

```
#include <iostream>

using namespace std;

int main()
{
    cout << "hello world" << endl;
    return 0;
}
```

### 让程序跑起来

#### 编译
程序已经写好了
```
g++ -std=c++11 hello.cpp -o hello.exe
```
上述是编译例程的命令，一般用在linux里面进行可执行文件的生成。

基本解释：使用`g++`这个**编译器**程序，按照`c++11`的**标准**，把`hello.cpp`输出为名为`hello.exe`的程序。（这里不详述预处理，编译，链接等流程）

在windows上可能命令就不一样了，但原理是类似的，取决于用何种工具来进行生成。比如写成下面这种方式：
```
编译程序 编译选项 输入文件 输出文件 采用标准
xx /x xx.cpp /o xx /std c++11
```


#### 执行

执行命令如下：
```
./hello.exe
```
可以观测到`hello world`的输出。

这里不详述（命令行里为什么可以启动一个应用程序）

