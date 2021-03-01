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

> 所有自动配置启动的时候会全部加载。按条件装配规则，最终会按需配置。

- 查询自动配置报告。配置文件debug=true。Negative（不生效）\Positive（生效）
- 参照文档修改配置项
  - https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties

--------

## 二、SpringBoot注解

#### 2.1、配置相关：

@**AutoConfigrationPackage**：利用register给容器中导入一系列组件，将指定的一个包下的所有组件导入进来。main程序所在的包下。

@**Conditional**：条件装配，满足指定的条件，则进行组件注入。

@**ImportResource**：导入spring的配置文件（classpath：bean.xml）。

@**ConfiguragionProperties**：配置绑定，可以读取配置文件进行属性的赋值，value为属性名。搭配component使用。

@**EnableConfiguragionProperties**：配置绑定，value为指定绑定的类。可作用在javaconfig配置类上。

------

## 三、Web开发

#### 3.1、请求映射原理

![2021030101](../Images/2021030101.png)

SpringMvc的分析从**org.springframework.web.servlet.DispatcherServlet ->doDispatch()**开始：

**RequestMappingHandlerMapping**：保存了所有@RequestMapping 和handler的映射规则。







