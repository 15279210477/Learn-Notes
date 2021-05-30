# Spring Boot Notes

---------

## 一、springBoot入门

#### 1.1、SpringBoot的优点

- **约定优于配置**。按照Spring Boot的方式进行简单的配置后，功能就可以直接使用了。
- **自动装配**。Spring Boot在启动时会自动探测类路径下的各种类型，实现类型的自动装配，无需开发者再通过XML或是注解进行显式的类型装配了，这一点要拜@EnableAutoConfiguration注解或是更为全面的@SpringBootApplication注解所赐。
- **内嵌各种Servlet容器**。Spring Boot内嵌了Tomcat、Jetty与Undertow等Servlet容器，使得我们可以像开发普通的Java应用那样直接通过main方法来启动容器，甚至可以直接通过jar包而非war包的形式就能够实现快速部署，不必再依赖于外部的Servlet容器。
- **yml配置的支持**。yml或是yaml的全称是YAML Ain't Markup Language的简称（有意思的是，当初在开发这门语言时，其简称实际上是Yet Another Markup Language），新的简称采取了递归的方式。这种新的语言可以看作是传统的properties配置文件的一种有益补充，它通过缩进的方式来表示层次化的配置项，相比于传统的properties属性文件来说，其层次感会更好一些；当然，顺便也可以让我们少敲一些字母。
- **提供了各种starter便于功能的开箱即用**。围绕着Spring Boot生态圈目前已经涌现出了不计其数的starter，这样我们只需将相应的starter配置项引入到项目中即可很方便地使用对应的功能。
- **提供了各种度量**。Spring Boot提供了各种度量属性，便于我们查看当前项目的运行情况以及系统使用情况。

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
  - [spring官网](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)

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







### 参考文档地址：

[springboot语雀](https://www.yuque.com/atguigu/springboot/qb7hy2)