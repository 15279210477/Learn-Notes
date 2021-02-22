# Spring MVC Notes

## 一、配置及组件

**web.xml**

```xml
<servlet>
	<servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 配置文件地址 -->
    <init-param>
    	<param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!-- 启动容器即开启配置 -->
    <load-on-startup>1</load-on-startup>
	<!-- 配置servlet路径 -->
    <servlet-mapping>
    	<servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</servlet>
```

**springmvc.xml**

```xml
<!-- 开启注解扫描 -->
<context:component-scan base-package="xx.xxx"/>
<!-- 视图解析器对象 -->
<bean id="xxx" class="org.springframework.web.servlet.view.InternalResource">
	<property name="prefix" value="/WEB-INF/pages/"/>
    <property name="suffix" value=".jsp"/>
</bean>
<!-- 开启 springMvc框架注解的支持 -->
<mvc:annotation-drivern/>
```

![image-20210217201405577](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210217201405577.png)

----

## 二、注解

**@RequestMapping**：映射请求，指定控制器处理哪些URL请求。

- method：指定请求的方式，可传数组。
- params：指定限制请求参数的条件，支持简单的表达式。要求请求参数的keyValue必须一致。
- headers：用于限制请求消息头的条件。

**@RequestBody**：获取请求体内容，直接使用得到的是key=value&key=value..结构的数据，get不适用。

- required：是否必须有请求体。默认值：true。当为true时，get请求会报错，取值为false，get请求得到null。

**@PathVaribale**：用于绑定url的占位符。是springMvc支持rest风格URL的一个重要标志。

@**RequestHeader**：用于获取请求头信息。

- value：提供消息头名称

@**CookieValue**：用于把指定cookie名称的值传入控制器方法参数。

- value：提供cookie名称

@**ModelAttribute**；注解在方法上：表示当前方法会在控制器方法之前执行。注解在属性上：获取指定的数据给参数赋值。

@**SessionAttribute**：用于多次执行控制器方法间的参数共享。

----

## 三、配置自定义转换器

**springMvc.xml**

```xml
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
	<property name="converters">
    	<set>
        	<bean class="cn.xxx.xx"/>
        </set>
    </property>
</bean>
<mvc:annotation-drivern conversion-service="conversionService"/>
```





