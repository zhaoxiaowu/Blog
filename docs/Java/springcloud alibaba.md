![image-20200520195436351](..\images\image-20200520195436351.png)

![image-20200520195530633](..\images\image-20200520195530633.png)



### Nacos 服务注册和发现 

> 一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

[Nacos文档](https://nacos.io/zh-cn/docs/what-is-nacos.html)

eureka + spring cloud config

1.服务的注册与发现

2.nacos负载均衡，服务上下线，权重元信息

引入ribbon实现负载均衡，默认使用轮询  通过权重设置 ，机器流量

元信息：代码一致通过元数据 添加描述信息，区分服务，通过区分做流量区别    

![image-20200521152856511](..\images\image-20200521152856511.png)



元数据 和 权重设置 实现灰度发布

3.feign  webflux服务调用

feign 通过添加注释 成为rest api客户端，实现远程调用

4.spring cloud gateway

提供简单有效的API路由管理

 spring cloud gateway 作为Spring cloud生态系统中的网关。目标是替代zuul

它不仅提供统一的路由管理，并且给予filter链的方式提供网关基本的功能。例如：安全、监控、埋点和限流等（统一异常处理，xss,sql注入，权限控制，黑名单白名单，日志打印）

 `LoadBalancerClient Filter`

5.nicos 配置管理

@RefreshScope

![image-20200521213235967](..\images\image-20200521213235967.png)

默认配置是properties文件

file-extension: yml

namespace: 命名空间

**nicos配置文件共享**:

加载多个：

![image-20200521214311845](..\images\image-20200521214311845.png)

![image-20200521214435987](..\images\image-20200521214435987.png)

**nicos持久化**

默认是嵌入式

**nicos集群配置**

cluster.conf

![image-20200521220409654](..\images\image-20200521220409654.png)

6.sentinel

> 分布式系统的流量防卫兵

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

![Sentinel-features-overview](https://user-images.githubusercontent.com/9434884/50505538-2c484880-0aaf-11e9-9ffc-cbaaef20be2b.png)

![image-20200522123244836](..\images\image-20200522123244836.png)

**限流功能**

**黑名单控制**

**热点限流**

![image-20200522153502406](..\images\image-20200522153502406.png)

**自适应限流**

保障系统稳定的情况 ，在保证吞吐量

![image-20200522155147319](..\images\image-20200522155147319.png)
**规则持久化**

file ,nacos,zk,apllo,redis