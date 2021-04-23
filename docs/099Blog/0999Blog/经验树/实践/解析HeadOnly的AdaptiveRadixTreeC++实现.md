# 解析HeadOnly的AdaptiveRadixTreeC++实现

代码请看这里：
[Fork来的代码](https://github.com/cuteyoko/adaptive-radix-tree/blob/master/include/art.hpp)

## 动机

- 学习headOnly的处理方法
- 了解artree的实现
- 熟悉模板

不管是否通用，了解其思想，明白为什么要或者为什么不要。

## 概念说明

### HeadOnly
Header Only Library 所有实现在头文件中实现，不依赖cpp
优点：
1. 集成简单，仅头文件
2. 利于编译器优化，不依赖二进制
3. 不能使用全局变量或静态变量，抽象性好，所有代码对使用者可见
4. 避免abi接口变更风险

缺点：
1. 编译代价大，所有修改必须重新编译（所以尽量功能稳定）
2. 合理划分编译单元，尽量避免无关模块同时包含。
3. 需要保证实现尽量平台特性无关，否则对使用者不友好。

基础实践-创建HeadOnly仓库
- 实践：模块组织成Header Only Library,但使用一个编译单元把它与其它部分隔离.常用的隔离技术比如PIMPL可以在这里使用.

争论：
- 编译代价
- 问题暴露时机
- abi问题
- 引入库的复杂度
- 对cpp没有module的妥协

一些常用的headonly库
- [AwesomeHpp](https://github.com/p-ranav/awesome-hpp)


## 解析

### 目录结构
```
--include
  |--art
     |--art.hpp
     |--child_it.hpp
     |--inner_node.hpp
     |--leaf_node.hpp
     |--node.hpp
     |--node_16.hpp
     |--node_256.hpp
     |--node_4.hpp
     |--node_48.hpp
     |--tree_it.hpp
  |--art.hpp
```
### art.hpp

首先是头文件，采用目录加一个头文件包含其他头文件的方式，从编译角度看就是所有头文件包含进来。
一个修改倒置所有编译，pimpl方式可以一定程度解决这个问题——其实仔细想想，只要包含这个的编译单元不涉及就不会。所以影响也不会太大。

```
/**
 * @file adaptive radix tree
 * @author Rafael Kallis <rk@rafaelkallis.com>
 */

#ifndef ART_HPP
#define ART_HPP

#include "art/art.hpp"
#include "art/child_it.hpp"
#include "art/inner_node.hpp"
#include "art/leaf_node.hpp"
#include "art/node.hpp"
#include "art/node_16.hpp"
#include "art/node_256.hpp"
#include "art/node_4.hpp"
#include "art/node_48.hpp"
#include "art/tree_it.hpp"

#endif
```

### art/

#### node.hpp

先看依赖最少的,被别人依赖最多的。
```
#include <algorithm>
#include <array>
#include <cassert>
#include <cstring>
#include <iostream>
#include <iterator>
#include <stdexcept>
```
可以看到包含的头文件仅系统头文件，且都是c++标头。看他。

根据名字可以知道，这个文件定义了artree的基本node结构。
首先，已`namespace art`来进行一个作用范围的划定。

然后，定义一个模板类。 主要有is_leaf check_prefix的两个接口，后者非virtual，期待不重新实现。
附带一个实现

```
template <class T> class node {
public:
  virtual ~node() = default;

  node() = default;
  node(const node<T> &other) = default;
  node(node<T> &&other) noexcept = default;
  node<T> &operator=(const node<T> &other) = default;
  node<T> &operator=(node<T> &&other) noexcept = default;

  virtual bool is_leaf() const = 0;

  int check_prefix(const char *key, int key_len) const;

  char *prefix_ = nullptr;
  uint16_t prefix_len_ = 0;
};

template <class T>
int node<T>::check_prefix(const char *key, int /* key_len */) const {
  return std::mismatch(prefix_, prefix_ + prefix_len_, key).second - key;
}
```

#### leaf_node.hpp

实现一个子类，继承自node。

```
template <class T> class art; // 为什么这里有一个莫名其妙的前置？

template <class T> class leaf_node : public node<T> {
public:
  explicit leaf_node(T *value);
  bool is_leaf() const override;

  T *value_;
};

template <class T>
leaf_node<T>::leaf_node(T *value): value_(value) {}

template <class T> 
bool leaf_node<T>::is_leaf() const { return true; }
```

#### child it

```
template <class T> class inner_node;

template <class T> class child_it {
public:
  child_it() = default;
  child_it(const child_it<T> &other) = default;
  child_it(child_it<T> &&other) noexcept = default;
  child_it<T> &operator=(const child_it<T> &other) = default;
  child_it<T> &operator=(child_it<T> &&other) noexcept = default;

  explicit child_it(inner_node<T> *n);
  child_it(inner_node<T> *n, int relative_index);

  using iterator_category = std::bidirectional_iterator_tag;
  using value_type = const char;
  using difference_type = int;
  using pointer = value_type *;
  using reference = value_type &;

  reference operator*() const;
  pointer operator->() const;
  child_it &operator++();
  child_it operator++(int);
  child_it &operator--();
  child_it operator--(int);
  bool operator==(const child_it &rhs) const;
  bool operator!=(const child_it &rhs) const;
  bool operator<(const child_it &rhs) const;
  bool operator>(const child_it &rhs) const;
  bool operator<=(const child_it &rhs) const;
  bool operator>=(const child_it &rhs) const;

private:
  inner_node<T> *node_;
  char cur_partial_key_;
  int relative_index_;
};

template <class T> child_it<T>::child_it(inner_node<T> *n) : child_it<T>(n, 0) {}

template <class T>
child_it<T>::child_it(inner_node<T> *n, int relative_index)
    : node_(n), cur_partial_key_(0), relative_index_(relative_index) {
  if (relative_index_ < 0) {
    /* relative_index is out of bounds, no seek */
    return;
  }

  if (relative_index_ >= node_->n_children()) {
    /* relative_index is out of bounds, no seek */
    return;
  }

  if (relative_index_ == node_->n_children() - 1) {
    cur_partial_key_ = node_->prev_partial_key(127);
    return;
  }

  cur_partial_key_ = node_->next_partial_key(-128);
  for (int i = 0; i < relative_index_; ++i) {
    cur_partial_key_ = node_->next_partial_key(cur_partial_key_ + 1);
  }
}

template <class T>
typename child_it<T>::reference child_it<T>::operator*() const {
  if (relative_index_ < 0 || relative_index_ >= node_->n_children()) {
    throw std::out_of_range("child iterator is out of range");
  }

  return cur_partial_key_;
}

template <class T>
typename child_it<T>::pointer child_it<T>::operator->() const {
  if (relative_index_ < 0 || relative_index_ >= node_->n_children()) {
    throw std::out_of_range("child iterator is out of range");
  }

  return &cur_partial_key_;
}

template <class T> child_it<T> &child_it<T>::operator++() {
  ++relative_index_;
  if (relative_index_ == 0) {
    cur_partial_key_ = node_->next_partial_key(-128);
  } else if (relative_index_ < node_->n_children()) {
    cur_partial_key_ = node_->next_partial_key(cur_partial_key_ + 1);
  }
  return *this;
}

template <class T> child_it<T> child_it<T>::operator++(int) {
  auto old = *this;
  operator++();
  return old;
}

template <class T> child_it<T> &child_it<T>::operator--() {
  --relative_index_;
  if (relative_index_ == node_->n_children() - 1) {
    cur_partial_key_ = node_->prev_partial_key(127);
  } else if (relative_index_ >= 0) {
    cur_partial_key_ = node_->prev_partial_key(cur_partial_key_ - 1);
  }
  return *this;
}

template <class T> child_it<T> child_it<T>::operator--(int) {
  auto old = *this;
  operator--();
  return old;
}

template <class T> bool child_it<T>::operator==(const child_it<T> &rhs) const {
  return node_ == rhs.node_ && relative_index_ == rhs.relative_index_;
}

template <class T> bool child_it<T>::operator<(const child_it<T> &rhs) const {
  return node_ == rhs.node_ && relative_index_ < rhs.relative_index_;
}

template <class T> bool child_it<T>::operator!=(const child_it<T> &rhs) const {
  return !((*this) == rhs);
}

template <class T> bool child_it<T>::operator>=(const child_it<T> &rhs) const {
  return !((*this) < rhs);
}

template <class T> bool child_it<T>::operator<=(const child_it<T> &rhs) const {
  return (rhs >= (*this));
}

template <class T> bool child_it<T>::operator>(const child_it<T> &rhs) const {
  return (rhs < (*this));
}
```


## 总结


