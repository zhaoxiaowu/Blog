Spring5引入webflux 

响应式编程



函数式编程



![image-20200525120324643](..\images\image-20200525120324643.png)





非阻塞 的web开发框架



service 3.1  netty



非关系型



优势：



## 什么是WebFlux

响应式编程的子模块

### 什么是响应式编程

基于数据流（data stream）和变化传递（propagation of change）的声明式（declarative）的编程范式

--  变化传递    异步、非阻塞

-- 数据流和声明式     流式编程

### WebFlux

默认基于Netty为应用服务器

Flux和Momo作为发布者

好处：能够以固定线程来处理高并发（充分发挥机器优化)



从官网的简介中我们能得出什么样的信息？

- 我们程序员往往**根据不同的应用场景选择不同的技术**，有的场景适合用于同步阻塞的，有的场景适合用于异步非阻塞的。而`Spring5`提供了一整套**响应式**(非阻塞)的技术栈供我们使用(包括Web控制器、权限控制、数据访问层等等)。
- 而左侧的图则是技术栈的对比啦；
- 响应式一般用Netty或者Servlet 3.1的容器(因为支持异步非阻塞)，而Servlet技术栈用的是Servlet容器
- 在Web端，响应式用的是WebFlux，Servlet用的是SpringMVC
- .....

总结起来，WebFlux只是响应式编程中的一部分(在Web控制端)，所以一般我们用它与SpringMVC来对比。



展望响应式编程的场景应用：

> 比如一个日志监控系统，我们的前端页面将不再需要通过“命令式”的轮询的方式不断向服务器请求数据然后进行更新，而是在建立好通道之后，数据流从系统源源不断流向页面，从而展现实时的指标变化曲线；
> 再比如一个社交平台，朋友的动态、点赞和留言不是手动刷出来的，而是当后台数据变化的时候自动体现到界面上的。

#### `RouterFunction`

用于将请求路由到相应的`HandlerFunction`

您不是自己编写路由器功能，而是使用`RouterFunctions`实用程序类上的方法 创建一个。 `RouterFunctions.route()`（无参数）为您提供了一个流畅的生成器来创建路由器功能，而`RouterFunctions.route(RequestPredicate, HandlerFunction)`提供了一种直接的方式来创建路由器。

[外行人都能看得懂的WebFlux，错过了血亏！](https://zhuanlan.zhihu.com/p/92460075)



