---
title: 06.微服务网关整合OAuth2.0授权中心
date: 2023-07-09 03:07:10
categories: [电商系统]
tags: [授权中心]
---

1. 配置授权服务器（AuthorizationServerConfigurerAdapte - 授权码/密码）
    1. DB模式
    2. 内存模式   
2. SpringSecurity（WebSecurityConfigurerAdapter）
3. JWT：头部（header）、载荷（payload）与签名（signature 加盐）
    1. 一次性验证、无状态认证 
    2. 注销续约复杂，不适合会话管理 
4. JWT非对称加密（公钥私钥）
5. 扩展JWT中的存储内容（TokenEnhancer）
6. 接入网关服务（GlobalFilter）
    1. 过滤不需要认证的url 
    2. 校验token（需要从授权服务获取公钥）
        * 不能直接通过@LoadBalancer配置RestTemplate去获取公钥，因为Spring容器启动过程中@LoadBalancer还未生效
    3. 校验通过后，从token中获取的用户登录信息存储到请求头中
