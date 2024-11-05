# SpringCloud面试题

## SpringCloud常用组件

+ Eureka，提供服务注册和发现功能，是微服务架构中的注册中心。

+ Ribbon，负责客户端负载均衡。

+ Hystrix

+ Feign，通过注解、http请求的方式调用远程服务。

+ Gateway，Gateway是Spring Cloud生态系统中的网关。
  
  1. 客户端请求到达**Gateway**之后，先经过Gateway Handler Mapping，经过路由映射到后端的某个服务。
  
  2. 请求到达Gateway Web Handler，里面有很多过滤器组成过滤器链，对请求进行拦截和修改，将请求转发到实际的后端服务。

## eureka和nacos的区别
