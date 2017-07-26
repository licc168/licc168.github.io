---
layout:     post
title:      spring 整合guava 实现异步监听
subtitle:   spring+guava
keyword:     spring,guava,异步监听
description : spring 整合guava 实现异步监听
date:       2017-07-13
author:     licc
header-img: img/post_bg_debug.png
catalog: true
tags:
    - spring
    - guava
---
## 概述
   guava中EventBus是一个消息处理总线，基于观察者模式设计和实现。网上有很多关于其原理介绍和用法的文字，但很多用法都是给了已经简单的示例或测试demo，并没有和工程框架相结合。
    本文主要整合spring实现异步监听
    
---    
## maven依赖
```
	<dependency>
			<groupId>com.google.guava</groupId>
			<artifactId>guava</artifactId>
			<version>19.0</version>
		</dependency>
```
---
## 配置spring监听器

```java
public class EsAsyncListener implements InitializingBean {
    private static final Logger LOGGER = LoggerFactory.getLogger(EsAsyncListener.class);
    @Resource
    private AsyncEventBus asyncEventBus;//guava线程总线

   
    @Subscribe
    public  void esLister(AsyncSearchParam asyncSearchParam){
       //TODO:此处异步
    }



    @Override
    public void afterPropertiesSet() throws Exception {
        asyncEventBus.register(this);//注册

    }

}
```
---
## spring xml配置
   ```
      <bean id="esAsyncListener" class="****.EsAsyncListener"/>
   ```
---
## 调用异步监听
``` java
@Service
public class AsyncEsListenerServiceImpl implements AsyncEsListenerService {
    @Resource
    private AsyncEventBus       asyncEventBus;
    @Override
    public void asyncEsListenrer(SearchTempleteEnum searchTempleteEnum, Long... ids) {
        // 同步到搜索引擎
        AsyncSearchParam searchParam = new AsyncSearchParam(searchTempleteEnum, ids);
        asyncEventBus.post(searchParam);
    }
}

```
---


## 踩过的坑
   ```
   因为是异步所以存在事务问题，异步方法如果涉及到事务则需要判断事务有没有提交
  ```
---
## guavaEventBus 相关资料   
> * **[[Google Guava] 11-事件总线](http://ifeve.com/google-guava-eventbus/)**
> * **[Google-Guava-EventBus源码解读](http://vinoyang.com/2015/06/02/Google-Guava-EventBus%E6%BA%90%E7%A0%81%E8%A7%A3%E8%AF%BB/)**
