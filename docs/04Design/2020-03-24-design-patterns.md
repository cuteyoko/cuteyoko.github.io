---
title: 设计模式
description: 设计模式使用学习
categories:
 - DesignPattern
tags:
 - 设计模式
---

## 创建型模式

### Abstract Factory 抽象工厂



### Prototype
### AbstractFactory
### Builder
### Singleton

## 单例

首先，基本的实现如下，存在问题——线程不安全。

```c++
class Singleton{
// @Desc: 私有构造函数，无法创建其他实例
private:
   Singleton() {}
   static Singleton* instance;
public:
    Singleton* GetInstance();
};

// @File: Singleton.cc
singleton* singleton::instance = NULL;
singleton* singleton::GetInstance()
{
    if (instance == NULL)
        instance = new singleton();
    return instance;
}
```

懒汉状态下， 在构造函数中初始化锁，在获取实例时只有一个能够访问资源。（只有运行时才会创建实例是懒汉也，饿汉十分饥渴，我就一直是有的）

```c++
// @Desc: 加锁的懒汉式单例
SingleInstance * SingleInstance::GetInstance()
{
    //  这里使用了两个 if判断语句的技术称为双检锁；好处是，只有判断指针为空的时候才加锁，避免每次调用 GetInstance的方法都加锁，锁的开销毕竟还是有点大的。
    if (m_SingleInstance == NULL) 
    {
        std::unique_lock<std::mutex> lock(m_Mutex); // 加锁
        if (m_SingleInstance == NULL)
        {
            m_SingleInstance = new (std::nothrow) SingleInstance;
        }
    }
    return m_SingleInstance;
}

//  @Desc: 利用static机制的懒汉单例,c++11里面静态变量的方式是线程安全的
singleton* singleton::GetInstance()
{
    singleton instance
    return &instance;
}

// @Desc: 依靠静态的饿汉机制单例, 本身线程安全
singleton* singleton::GetInstance()
{
    return instance;
}
singleton *instance = new singleton();
```