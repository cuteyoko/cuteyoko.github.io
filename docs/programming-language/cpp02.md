# 从零开始c++: 从代码到汇编

## 例程

```
#include <iostream>
  
using namespace std;

int add(const int a, const int b)
{
        return a + b;
}

int main()
{
    int ret = add(3, 6);
    cout << ret << endl;
    return ret;
}
```

### 反汇编解析

```asm
0000000000000000 <_Z3addii>:
   0:   f3 0f 1e fa             endbr64
   4:   55                      push   %rbp
   5:   48 89 e5                mov    %rsp,%rbp
   8:   89 7d fc                mov    %edi,-0x4(%rbp)
   b:   89 75 f8                mov    %esi,-0x8(%rbp)
   e:   8b 55 fc                mov    -0x4(%rbp),%edx
  11:   8b 45 f8                mov    -0x8(%rbp),%eax
  14:   01 d0                   add    %edx,%eax
  16:   5d                      pop    %rbp
  17:   c3                      retq


0000000000000018 <main>:
  18:   f3 0f 1e fa             endbr64
  1c:   55                      push   %rbp
  1d:   48 89 e5                mov    %rsp,%rbp
  20:   48 83 ec 10             sub    $0x10,%rsp
  24:   be 06 00 00 00          mov    $0x6,%esi
  29:   bf 03 00 00 00          mov    $0x3,%edi
  2e:   e8 00 00 00 00          callq  33 <main+0x1b>
  33:   89 45 fc                mov    %eax,-0x4(%rbp)
  36:   8b 45 fc                mov    -0x4(%rbp),%eax
  39:   89 c6                   mov    %eax,%esi
  3b:   48 8d 3d 00 00 00 00    lea    0x0(%rip),%rdi        # 42 <main+0x2a>
  42:   e8 00 00 00 00          callq  47 <main+0x2f>
  47:   48 89 c2                mov    %rax,%rdx
  4a:   48 8b 05 00 00 00 00    mov    0x0(%rip),%rax        # 51 <main+0x39>
  51:   48 89 c6                mov    %rax,%rsi
  54:   48 89 d7                mov    %rdx,%rdi
  57:   e8 00 00 00 00          callq  5c <main+0x44>
  5c:   8b 45 fc                mov    -0x4(%rbp),%eax
  5f:   c9                      leaveq
  60:   c3                      retq

```
