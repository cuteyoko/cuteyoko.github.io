# nm命令

## why
本名names,列出某些文件中的符号.
list symbols from object files

## 解读命令

```
xxxxxxxxxxxxxxxxx T _ZNK8xxxxxxxxxxx11XXXXXX18xxxxxxxxERSsRmj
符号值 | 符号类型 | 符号名
```

类型小写一般是local 大写全局,uvw是全局。

|类型|说明|
|---|---|
|A|Absolute |
|B/b| 符号在BSS data section|
|C | common|
|D/d| data section|
|G/g| small object的初始化数据段|
|i|section specific to dll实现(PE file)/ indrect function(ELF file)|
|I|Indirect reference to another symbol|
|N|debugging symbol|
|n|symbol in a read-only data section|
|p|stack unwind section|
|R/r|readonly data section|
|S/s|in an uninitialized/zero-initialized data section for small obj|
|T/t|text(code) section|
|U| undefined|
|u|unique global|
|V/v|一言难尽，详情google|
|W/w|一言难尽，详情google|
|-|stab|
|?|unkown|

## 选项
