---
layout: post
title: "springboot中配置Jackson序列化使对象属性值不存在时不返回该属性"
date: 2022-10-26 15:12:07
categories: 编程技巧
tags: springboot Jackson
author: NanKe
---

* content
{:toc}
>springboot中配置Jackson序列化使对象属性值不存在时不返回该属性



# 问题描述

`返回JSON数据含有null值`

不希望数据返回时将属性值为null的属性值返回，具体情况如下图所示：
![在这里插入图片描述](https://raw.githubusercontent.com/crazymen-nanke/image/master/note/202303181533063.png)
图中yzm等属性值是null，不希望显示这些属性值，预期效果如下图所示：
![在这里插入图片描述](https://raw.githubusercontent.com/crazymen-nanke/image/master/note/202303181533027.png)

---

# 原因分析：
`Java对象序列化未过滤相关对象`

SpringBoot项目默认使用Jackson进行对象序列化操作，默认情况下Jackson会将所有的Java对象属性值进行序列化，可以对Jackson序列化对象操作进行控制

---

# 解决方案：
`方法一：配置类解决`
>在spring boot项目中，可以通过配置类中定制Jackson ObjectMapper来实现序列化时不返回没有值的属性

下面是一个示例配置类：
```java
@Configuration
public class JacksonConfig {

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        return objectMapper;
    }
}
```
在该配置类中，定义了一个名为objectMapper的bean，使用ObjectMapper的实例设置序列化的JsonInclude的参数为JsonInclude.Include.NON_NULL，表示序列化时不返回没有值的属性。 

`方法二：配置文件解决`
>在Spring Boot配置文件中，可以通过在application.properties或application.yml中配置`spring.jackson.default-property-inclusion`来配置Jackson的序列化特性。

下面是一个在application.properties中配置的示例：
```yml
spring:
  jackson:
    default-property-inclusion: non_null
```
