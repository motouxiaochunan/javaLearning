# Spring面试题

## Spring、Spring MVC、SpringBoot之间是什么关系

**Spring**框架立足于最基础的POJO(简单Java对象)，为构筑应用的POJO提供了各种服务，创造了一套适宜用POJO进行轻量级开发的环境，来简化基于POJO的Java应用程序开发。

Spring框架构建在以IOC容器、IO工具类为基础的Core核心模块上，包含了AOP、Web、对数据的访问服务、对ORM（对象关系映射）的集成支持。

**SpringMVC**是Spring框架中的一个重要模块，它为Web应用程序开发提供了一套可复用的基础设施，使得开发人员免于处理重复的通用逻辑。

SpringBoot将Spring框架的配置优化为自动化配置，如省去了XML文件中对于bean的配置。

## Spring

### 对于Spring IOC的理解

IOC模块把编程人员管理对象的职责转移到Spring框架上，由IOC容器统一管理对象。

### 注入Bean的方法有哪些

1. 构造方法注入，在构造方法的参数中加入需要依赖的对象，IOC容器会在构造对象的同时注入依赖对象。

2. Setter注入，在类中定义一个setXXX的方法，注入需要依赖的对象。

3. 注解@Autowired。

### Bean的生命周期

1. 实例化bean，在内存中创建bean对象。

2. 检查Aware相关接口。包括：BeanNameAware设置bean的名字，BeanClassLoaderAware注入类加载器到对象实例，BeanFactoryAware将BeanFacotry注入当前对象。

3. BeanPostProcessor进行Bean初始化的前置处理。

4. 对bean进行初始化。

5. BeanPostProcessor进行Bean初始化的后置处理。

6. bean销毁前回调逻辑。

### 对于Spring AOP的理解

在面向对象编程中，业务需求和具体实现间的关系基本是一对一的。然而，日志记录和安全检查等系统功能的对应关系随着业务对象的增加变成1:n，使得系统开发维护难度大幅增加。

AOP中文名为面向方面编程，对日志和安全等系统需求进行模块化组织，使得单个模块能够应对1:n的关系。

### AOP如何实现

### Spring框架中的设计模式

### 使用AOP的例子

**异常处理**（再具体一点）

对checkedException，不同类型的错误需要有针对性地进行处理，而uncheckedException只需要将错误记录下来，因此是一种横切关注点。

**安全检查**（再具体一点）

比如Filter可以为Web应用添加相应的资源访问控制。

## SpringMVC

### 对于Spring MVC的理解

SpringMVC是Spring框架中一个的重要模块，它为Web应用程序开发提供了一套可复用的基础设施，使得开发人员免于处理重复的通用逻辑。 

### SpringMVC的核心组件有哪些

+ **DispatcherServlet**：核心中央处理器，负责接收请求、分发，给予客户端响应。

+ **HandlerMapping**：根据URL匹配能处理的Handler。

+ **HandlerAdapter**：根据找到的Handler进行适配。

+ **Handler**：处理实际请求的处理器。

+ **ViewResolver**：根据Handler返回的视图，解析并渲染真正的视图传递给DispatcherServlet。

### SpringMVC的工作原理

## Spring事务

### Spring管理事务的方式有几种

1. 编程式事务管理
   
   TransactionTemplate或TransactionManager手动管理事务。

2. 声明式事务管理
   
   事务管理代码与业务逻辑代码混杂，声明式事务管理可以避免这种不同系统关注点间的纠缠。
   
   @Transactional

### Spring的事务传播行为

当事务方法被另一个事务方法调用，必须指定事务如何传播。例如：方法可以继续在现有事务中运行，也可以开启一个新事务并在自己的事务中运行。

1. Propagation.REQUIRED，如果外部方法开启事务，则加入。如果没有开启事务，则创建一个新的事务。

2. Propagation.REQUIRES_NEW，不管外部方法是否开启事务都新开启自己的事务。

3. Propagation.NESTED，外部开启事务的情况下，在内部开启一个子事务。

### Spring事务隔离级别

+ `ISOLATION_DEFAULT`，使用数据库默认隔离级别。

+ `ISOLATION_READ_UNCOMMITTED`，读未提交，可能导致脏读、幻读、不可重复读。

+ `ISOLATION_READ_UNCOMMITTED`，读已提交，可能导致幻读、不可重复读。

+ `ISOLATION_REPEATABLE_READ`，可能导致幻读。

+ `TransactionDefinition.ISOLATION_SERIALIZABLE`，串行执行，但严重影响程序性能。
