# Spring、MVC、Boot学习笔记

## IOC

**IOC的基本思想**

当需要依赖对象时，不用自己构造而是使用框架提供的对象

**IOC的三种依赖注入方式**

1. 构造方法注入，对象可以在构造方法中声明依赖对象的参数列表，让IOC容器知道需要哪些依赖对象。

2. setter方法注入，为依赖对象添加setter方法，通过setter方法将依赖对象设置到被注入对象中。

3. 注解注入，

## IOC Service Provider

**IOC Service Provider的职责**

1. 业务对象的构建管理。业务对象无需关心依赖对象如何构建，这部分职责由IOC Service Provider承担。

2. 业务对象间的依赖绑定。通过结合已构建和管理的业务对象，以及业务对象间的依赖关系，将依赖的对象注入绑定。

**IOC Service Provider如何管理依赖关系**

## Bean Factory
