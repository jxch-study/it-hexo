---
title: 13.Sentinel限流
date: 2023-07-11 09:46:23
categories: [电商系统]
tags: [Sentinel]
---

针对电商系统，在遇到大流量时，更多考虑的是运行阶段如何保障系统的稳定运行，常用的手段：**限流，降级，拒绝服务**。

# 限流

客户端限流
* 好处：可以限制请求的发出，通过减少发出无用请求从而减少对系统的消耗。
* 缺点：当客户端比较分散时，没法设置合理的限流阈值：如果阈值设的太小，会导致服务端没有达到瓶颈时客户端已经被限制；而如果设的太大，则起不到限制的作用。
  
服务端限流
* 好处：可以根据服务端的性能设置合理的阈值
* 缺点：被限制的请求都是无效的请求，处理这些无效的请求本身也会消耗服务器资源。
 
在限流的实现手段上来讲，基于 QPS 和线程数的限流应用最多，最大 QPS 很容易通过压测提前获取。线程数限流在客户端比较有效，例如在远程调用时我们设置连接池的线程数，超出这个并发线程请求，就将线程进行排队或者直接超时丢弃。限流必然会导致一部分用户请求失败，因此在系统处理这种异常时一定要设置超时时间，防止因被限流的请求不能 fast fail（快速失败）而拖垮系统。

限流的方案
* 前端限流
* 接入层nginx限流
* 网关限流     
  * 基于redis+lua脚本限流（RequestRateLimiter过滤器工厂，令牌桶）
  * sentinel限流
    * route维度限流
      * 针对请求属性进行流控
    * API维度限流
    * 规则持久化
      * 启动持久化改造后的Sentinel控制台（指定端口和nacos配置中心地址）
      * 网关规则改造的坑
        * 网关规则实体转换
          * RuleEntity#toRule
          * ApiDefinitionEntity#toApiDefinition
          * GatewayFlowRuleEntity#toGatewayFlowRule
        * json解析丢失数据
          * ApiDefinition的属性`Set<ApiPredicateItem> predicateItems`中元素是接口类型，JSON解析丢失数据
          * 重写实体类MyApiDefinition,再转换为ApiDefinition
* 应用层限流 
  * 微服务接入sentinel
    * 匀速排队限流
      * Sentinel**匀速排队**方式会严格控制请求通过的间隔时间，也即是让请求以均匀的速度通过，对应的是漏桶算法。
      * 这种方式主要用于处理间隔性突发的流量（匀速排队模式暂时不支持 QPS > 1000 的场景）
    * 热点参数限流
      * 热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。
      * 热点规则需要使用`@SentinelResource("resourceName")`注解，否则不生效
      * 参数必须是7种基本数据类型才会生效

# 降级

降级就是当系统的容量达到一定程度时，限制或者关闭系统的某些非核心功能，从而把有限的资源保留给更核心的业务。降级的核心目标是牺牲次要的功能和用户体验来保证核心业务流程的稳定，是一个不得已而为之的举措。

服务降级的策略
* 自动化运维
  * 自动开关降级（超时、失败次数、故障、限流）
  * 手动开关降级
* 功能维度：读服务降级、写服务降级
* 系统层次维度：JS降级开关、接入层降级开关、应用层降级开关（OpenFeign整合Sentinel）

# 拒绝服务

拒绝服务可以说是一种不得已的兜底方案，用以防止最坏情况发生，防止因把服务器压跨而长时间彻底无法提供服务。
* 系统资源过载保护 
* [Nginx过载保护](https://github.com/alibaba/nginx-http-sysguard)
* Sentinel自适应限流
  * Load 自适应（仅对 Linux/Unix­like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 maxQps * minRt 估算得出。设定参考值一般是 CPU cores * 2.5。
  * CPU usage（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-­1.0），比较灵敏。
  * 平均 RT：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
  * 并发线程数：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
  * 入口 QPS：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。
  * 系统规则持久化yml配置
    ```yml
    system‐rules:
      nacos:
      server‐addr: 192.168.65.103:8848
      dataId: ${spring.application.name}‐system‐rules
      groupId: SENTINEL_GROUP
      data‐type: json
      rule‐type: system
    ```

# 总结梳理

## 限流

- 前端限流
    
- 接入层nginx限流
    
- 网关限流
    
    - 基于redis+lua脚本限流（RequestRateLimiter，令牌桶）
        
    - sentinel限流
        
        - route维度限流：针对请求属性进行流控
            
        - API维度限流
            
        - 规则持久化
            
            - 改造Sentinel控制台（指定端口和nacos配置中心地址）
                
            - 网关规则实体转换： RuleEntity#toRule； ApiDefinitionEntity#toApiDefinition； GatewayFlowRuleEntity#toGatewayFlowRule
                
            - json解析丢失数据：重写实体类MyApiDefinition,再转换为ApiDefinition
                
- 应用层限流（微服务接入sentinel）
    
    - 匀速排队限流：处理间隔性突发的流量（漏桶算法）
        
        - 暂时不支持 QPS > 1000 的场景
            
    - 热点参数限流（`@SentinelResource("resourceName")`）
        
        - 参数必须是7种基本数据类型才会生效
            

## 降级

- 自动化运维
    
    - 自动开关降级（超时、失败次数、故障、限流）
        
    - 手动开关降级
        
- 功能维度：读服务降级、写服务降级
    
- 系统层次维度：JS降级开关、接入层降级开关、应用层降级开关（OpenFeign整合Sentinel）
    

## 拒绝服务

- 系统资源过载保护
    
- [Nginx过载保护](https://github.com/alibaba/nginx-http-sysguard)
    
- Sentinel自适应限流
    
    - Load 自适应（Linux/Unix­like）：参考值 CPU cores \* 2.5
        
    - CPU usage：比较灵敏
        
    - 平均 RT：单位是毫秒
        
    - 并发线程数
        
    - 入口 QPS
        
    - 系统规则持久化（system‐rules.nacos）

