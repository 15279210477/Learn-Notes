# Spring Boot Notes

---------

## 一、springBoot入门

#### 1.1、SpringBoot的优点

- 创建独立Spring应用
- 内嵌web服务器
- 自动starter依赖，简化构建配置
- 自动配置Spring以及第三方功能
- 提供生产级别的监控、健康检查及外部化配置
- 无代码生成、无需编写XML

#### 1.2、依赖管理

> 几乎声明了所有开发中常用的依赖的版本号，自动版本仲裁机制，无需关注版本号。引入非版本仲裁的jar，要写版本号。

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
</parent>
<!-- 父项目 -->
 <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
  </parent>
<!-- 开发导入starter场景启动器即可 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```

#### 1.3、自动配置

## 

--------

## 二、SpringBoot注解

@**Conditional**：条件装配，满足指定的条件，则进行组件注入。

@**ImportResource**：导入spring的配置文件（classpath：bean.xml）。





















