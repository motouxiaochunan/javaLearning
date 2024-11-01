# Spring面试题

## Spring、Spring MVC、SpringBoot之间的关系

**Spring**框架立足于最基础的POJO(简单Java对象)，为构筑应用的POJO提供了各种服务，创造了一套适宜用POJO进行轻量级开发的环境，来简化基于POJO的Java应用程序开发。

Spring框架构建于以IOC容器、IO工具类为基础的Core核心模块上，包含了AOP、Web、对数据的访问服务、对ORM（对象关系映射）的集成支持。

**SpringMVC**是Spring框架中的一个重要模块，它为Web应用程序开发提供了一套可复用的基础设施，使得开发人员免于处理重复的通用逻辑。

**SpringBoot**将Spring框架的配置优化为自动化配置，如省去了XML文件中对于bean的配置。

## Spring

### 对于Spring IOC的理解

IOC模块把编程人员管理对象的职责转移到Spring框架上，由IOC容器统一管理对象。

### 注入Bean的方法有哪些

1. **构造方法注入**，在构造方法的参数中加入需要依赖的对象，IOC容器会在构造对象的同时注入依赖对象。对象被构造完成后，依赖对象就进入就绪状态。

2. **Setter注入**，在类中定义一个setXXX的方法，注入需要依赖的对象。

3. **注解@Autowired**。

### 将类声明为Bean的注解有哪些？

`@Component`：通用的注解，可标注为任意Spring组件。

`@Repository`：对应持久层，主要用于数据库相关操作。

`@Service`：对应服务层，主要设计复杂的业务逻辑，需要用到Dao层。

`@Controller`：对应SpringMVC控制层，接受用户请求并调用Service。

### @Autowired和@Resource的区别是什么？

+ `@Autowired`是Spring内置的注解，默认注入方式为`byType`，当一个接口存在多个实现类时无法正确注入对象。

+ `@Resource`是JDK提供的注解，默认注入方式为`byName`，如果无法通过名称匹配到对应的Bean，注入方式会变成`byType`。

+ `@Autowired`支持在构造函数、方法、字段和参数上使用，`@Resource`在构造函数和参数上无法使用。

### Bean的生命周期

1. **创建Bean的实例**。在内存中创建Bean对象。

2. **Bean属性赋值/填充**。依赖对象注入，@Value注入值等。

3. **检查Aware相关接口**。包括：BeanNameAware设置bean的名字，BeanClassLoaderAware注入类加载器到对象实例，BeanFactoryAware将BeanFacotry注入当前对象。

4. **BeanPostProcessor**进行Bean初始化的前置处理。

5. **对Bean进行初始化**。

6. **BeanPostProcessor进行Bean初始化的后置处理**。

7. **bean销毁前回调逻辑**。

### Bean是线程安全的吗？

singleton作用域下，IOC容器中只有唯一的bean实例，如果bean中包含可变的成员变量对象，那么存在线程安全问题。

**解决方案**：

+ 使用ThreadLocal保存可变成员变量。

+ 使用同步机制。

### 对于Spring AOP的理解

在面向对象编程中，业务需求和具体实现间的关系基本是一对一的。然而，日志记录和异常检查等系统功能的对应关系随着业务对象的增加变成1:n，使得系统开发维护难度大幅增加。

**AOP**中文名为面向方面编程，对日志和安全等系统需求进行模块化组织，使得单个模块能够应对1:n的关系。

### AOP如何实现

1. **Joinpoint**。需要知道系统在哪些执行点上进行织入操作，这些系统执行点称为**Joinpoint**。

2. **Pointcut**。系统参考Pointcut规定的Joinpoint信息，才能知道应该往系统哪些Joinpoint上织入逻辑。

3. **Aspect**。是对系统中横切关注点逻辑进行模块化封装的实体。

### 使用AOP的例子

接口限流、日志处理

## SpringMVC

### 对于Spring MVC的理解

**SpringMVC**是Spring框架中的一个重要模块，它为Web应用程序开发提供了一套可复用的基础设施，使得开发人员免于处理重复的通用逻辑。 

### SpringMVC的核心组件有哪些

+ **DispatcherServlet**：核心中央处理器，负责接收请求、分发，给予客户端响应。

+ **HandlerMapping**：进行Web请求的URL到具体处理类的匹配。

+ **HandlerAdapter**：HandlerMapping可能返回各种类型的Handler，HandlerAdapater统一调用Handler的Handle方法。

+ **Handler**：处理实际请求的处理器。

+ **ViewResolver**：根据Handler返回的视图，解析并渲染真正的视图传递给DispatcherServlet。

## Spring事务

### Spring管理事务的方式有几种

+ 编程式事务管理
  
  `TransactionTemplate`或`TransactionManager`手动管理事务。

+ 声明式事务管理
  
  如果使用编程式事务管理，事务管理代码会与业务逻辑代码混杂。声明式事务管理可以避免不同系统关注点之间的纠缠。`@Transactional`

### Spring的事务传播行为

**事务传播行为**是指当事务方法被另一个事务方法调用时，必须指定事务如何传播。事务传播类型有：

+ `Propagation.REQUIRED`，如果外部方法开启事务则加入，如果没有开启事务则创建一个新的事务。

+ `Propagation.REQUIRES_NEW`，不管外部方法是否开启事务，都开启新的事务。

+ `Propagation.NESTED`，外部开启事务的情况下，在内部开启一个子事务。

### Spring事务隔离级别

+ `ISOLATION_DEFAULT`，使用数据库默认隔离级别。

+ `ISOLATION_READ_UNCOMMITTED`，读未提交。

+ `ISOLATION_READ_UNCOMMITTED`，读已提交。

+ `ISOLATION_REPEATABLE_READ`，可重复读。

+ `TransactionDefinition.ISOLATION_SERIALIZABLE`，串行执行。

### 事务注解原理

如果一个类的方法被标注`@Transactional`，在调用被`@Transactional`注解的方法时，实际调用的是`TransactionIntercepter`类中的`invoke`方法。

这个方法在目标方法之前开启事务，在执行过程中遇到异常时回滚事务，方法调用完成后提交事务。

## SpringBoot

### SpringBoot启动流程

- **准备阶段**：`SpringApplication`类的`run()`方法会加载应用程序的初始设置并创建SpringBoot上下文。
  
  1. 首先，调用`StopWatch`对象，记录整个启动过程的耗时。
  
  2. 然后，调用`getRunListeners`获取SpringBoot的各个启动监听器，并调用`starting()`方法通知这些监听器启动过程已经开始。
  
  3. 接着，调用`prepareEnvironment(listeners,applicationArguments)`方法创建应用程序的环境变量`ConfigurableEnvironment`，并传给后面的`createApplicationContext()`方法。
  
  4. 最后，调用`refreshContext(context)`刷新上下文执行各种启动任务包括：创建Web服务器、加载应用程序配置、初始化各种组件等。

- **应用程序上下文创建阶段**：SpringBoot创建应用程序上下文，包括各种配置信息、Bean的加载和初始化等。**SpringBoot通过扫描classpath中的配置文件，自动化加载和配置各种组件和Bean**。
  
  1. 首先，`createApplicationContext()`方法种，SpringBoot判断应用程序类型，如果是Web应用程序则创建`WebApplicationContext`；否则，创建`ApplicationContext`。
  
  2. 然后，调用`BeanUtils.instantiateClass(contextClass)`方法创建应用程序上下文。扫描classpath中的各种配置文件，如`application.yml`等，自动配置各种组件和Bean。

- **刷新上下文阶段**：SpringBoot执行各种启动任务，包括创建Web服务器、加载应用程序配置、初始化各种组件等。**SpringBoot的刷新机制会调用各种初始化器和监听器，执行各种启动任务。其中启动Tomcat就是在这个环节进行**。
  
  1. 调用应用程序的关闭钩子，在应用程序关闭时自动执行清理资源、关闭线程等。
  
  2. `refreshContext()`中调用refresh(applicationContext)执行上下文各种启动任务，包括创建Web服务器，加载应用程序配置，初始化各种组件等。

### SpringBoot如何实现自动配置

### SpringApplication对象如何构建

### SpringBoot事件监听机制

### SpringBoot内置Starter构建

### SpringBoot内置生命周期

### 常用注解

`@SpringBootApplication`：`@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan`注解的集合。

+ `@EnableAutoConfiguration`：启动SpringBoot的自动配置。

+ `@ComponentScan`：扫描被注解的Bean，默认会扫描该类所在的包下所有类。

+ `@Configuration`：允许在Spring上下文注册额外的Bean。

#### Spring Bean相关

1. `@Autowired`

2. `@Component`、`@Repository`、`@Service`、`@Controller`

3. `@RestController`：将方法的返回值直接填入http响应体中，是REST风格的控制器。

4. `@Configuration`，与Component作用相同，但更语义化。

#### HTTP请求相关

`@GetMapping`、`@PostMapping`、`@PutMapping`、`@DeleteMapping`。

#### 前后端传值

1. `@PathVariable`获取路径参数，`@RequestParam`获取查询参数。

2. `@RequestBody`读取Request请求中的Body部分。

#### 读取配置信息

`@Value`读取配置信息。

#### 参数校验

`@NotNull`、`@NotEmpty`等。

`@Validated`告诉Spring检验方法参数。

#### 异常处理

+ `@ControllerAdvice`定义全局异常处理。

+ `@ExceptionHandler`注解异常处理方法。

#### JPA

`@Entity`声明一个数据库实体。

#### 事务

在要开启事务的方法上使用`@Transactional`。

### 
