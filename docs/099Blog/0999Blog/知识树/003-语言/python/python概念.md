# 概念详解

## 1. 可迭代、迭代器&生成器

判断
```
print(isinstance(it, Iterable))
print(isinstance(it, Iterator))
 print(isinstance(it, Generator)) # false

```
可迭代：
文件、list tuple set dict str,类中定义了`__iter()__`方法的对象等都为`Iterable`
实现`__getitem__()`的对象可以通过iter函数转化为迭代器，但不是可迭代对象。【在for中运行却不是可迭代对象】

迭代器：
实现了`__iter__()` & `__next__()`对象。

集合和序列对象是可迭代的但不是迭代器

而文件对象是迭代器

列表生成器 & yield
```
g = (x * 2 for x in range(10)) # 0～18的偶数生成器 
```
协程
