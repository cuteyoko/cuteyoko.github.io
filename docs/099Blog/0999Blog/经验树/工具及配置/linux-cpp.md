# Linux开发和代码阅读环境

## 基本环境

```bash
sudo apt-get install gcc g++ cmake
```

## clangd

[See This](https://clangd.llvm.org/)

```
What is clangd?
clangd understands your C++ code and adds smart features to your editor: 
code completion, compile errors, go-to-definition and more.

clangd is a language server that can work with many editors via a plugin.
Here’s Visual Studio Code with the clangd plugin, demonstrating code completion:
[假装有图片]
clangd is based on the Clang C++ compiler, and is part of the LLVM project.
```

### clang

那么什么是clang呢？

```
The Clang project provides a language front-end and tooling infrastructure 
for languages in the C language family (C, C++, Objective C/C++, OpenCL, CUDA, and RenderScript) for the LLVM project.
Both a GCC-compatible compiler driver (clang) and an MSVC-compatible compiler driver (clang-cl.exe) are provided.
You can get and build the source today.
```

### 从LLVM谈起

[LLVM每日谈](https://llvm-cn.blog.csdn.net/article/details/8036032)
