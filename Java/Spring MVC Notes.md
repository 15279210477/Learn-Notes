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

**@ReponseBody**：用于将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数据如：json、xml等，通过response响应给客户端。

**@PathVaribale**：用于绑定url的占位符。是springMvc支持rest风格URL的一个重要标志。

**@RequestHeader**：用于获取请求头信息。

- value：提供消息头名称

**@CookieValue**：用于把指定cookie名称的值传入控制器方法参数。

- value：提供cookie名称

**@ModelAttribute**；注解在方法上：表示当前方法会在控制器方法之前执行。注解在属性上：获取指定的数据给参数赋值。

**@SessionAttribute**：用于多次执行控制器方法间的参数共享。

**@RequestAttruibute**：获取请求域中的属性。

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

--------

## 四、Model、ModelMap、ModelAndView的理解

> SpringMVC在调用方法前会创建一个隐含的数据模型，作为模型数据的存储容器， 成为”隐含模型”。
>
> 也就是说在每一次的前后台请求的时候会随带这一个背包，不管你用没有，这个背包确实是存在的，用来盛放我们请求交互传递的值。

#### 4.1、Model和ModelMap

> 无论是Mode还是ModelMap底层都是使用BindingAwareModelMap。
>
> Model是一个接口，它的实现类为ExtendedModelMap，继承ModelMap类
>
> ModelMap继承LinkedHashMap，spring框架自动创建实例并作为controller的入参，用户无需自己创建。

#### 4.2、ModelAndView

> 而是对于ModelAndView顾名思义，ModelAndView指模型和视图的集合，既包含模型又包含视图；ModelAndView的实例是开发者自己手动创建的，这也是和ModelMap主要不同点之一；ModelAndView其实就是两个作用，一个是指定返回页面，另一个是在返回页面的同时添加属性。

```java
@RequestMapping("test")
public ModelAndView test() {
    ModelAndView mv = new ModelAndView();
	// 把user对象存储到mv对象中，也会把user对象存入到request对象
    mv.addObject("user", new user);
    // 跳转到哪个页面
    mv.setViewName("success");
    return mv;
}
```

-------

## 五、SpringMvc中的转发和重定向

#### 5.1、转发：

```java
retrun "forward:/WEB-INF/pages/successs.jsp";
```

#### 5.2、重定向：

```java
retrun "redirect:/index.jsp";
```

-----

## 六、SPringMvc的文件上传

![](..\images\image-20210223221944368.png)

#### 6.1、配置文件解析器对象

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<property name="maxUploadSize" value="10485760" />
</bean>
```

#### 6.2、跨服务器上传文件

```java
public void upload(MultipartFile file) {
    String path = “localhost:8080/uploads/”;
    // 创建客户端对象
    Client client = Client.create();
    // 和图片服务器进行连接
    WebResource webResource = client.resource(path + filename);
    // 上传文件
    webResource.put(file.getBytes());
}
```

------

## 七、拦截器

> 类似Servlet过滤器，用于对处理器进行预处理和后处理。拦截器是AOP思想的具体应用。想要自定义拦截器。要求必须实现HandlerInterceptor接口。

#### 7.1、过滤器和拦截器的区别

- 过滤器是Servlet规范的一部分，拦截器是SpringMvc框架自己的。
- 过滤器可以对所有访问的资源进行拦截，拦截器只会拦截访问的控制器方法。

**实现异常处理器接口，重写方法**

```java
xxx implements HandlerExceptionResolver{
    public ModelAndView resolveException(req, resp, obj, ex){}
}
```

#### 7.2、拦截器的具体实现

#### 7.3、拦截器类

```java
xxx implements HandlerInterceptor {
	preHandle(req, resp, obj) {};	// 预处理，return true：放行
}
```

#### 7.4、配置拦截器

##### 7.4.1、xml**方式**

```xml
<mvc:interceptors>
	<mvc:interceptor>
        <!-- 需要拦截的具体控制器 -->
    	<mvc:mapping path="/user/*" />
        <!-- 不需要拦截的具体控制器 -->
    	<mvc:exclude-mapping path="/user/*" />
        <!-- 配置拦截器对象 -->
        <bean class="cn.xxx.xx" />
    </mvc:interceptor>
</mvc:interceptors>
```

#### 7.4.2、**javaconfig方式**

```java
/**
 * 1、编写一个拦截器实现HandlerInterceptor接口
 * 2、拦截器注册到容器中（实现WebMvcConfigurer的addInterceptors）
 * 3、指定拦截规则【如果是拦截所有，静态资源也会被拦截】
 */
@Configuration
public class AdminWebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")  //所有请求都被拦截包括静态资源
                .excludePathPatterns("/","/login","/css/**","/fonts/**","/images/**","/js/**"); 
    }
}
```

#### 7.5、拦截器原理

1. 根据当前请求，找到**HandlerExecutionChain**【可以处理请求的handler以及handler的所有 拦截器】

2. 先来**顺序执行** 所有拦截器的 preHandle方法

- 如果当前拦截器prehandler返回为true。则执行下一个拦截器的preHandle
- 如果当前拦截器返回为false。直接   倒序执行所有已经执行了的拦截器的  afterCompletion；

3. 如果任何一个拦截器返回false。直接跳出不执行目标方法

4. **所有拦截器都返回True。执行目标方法**

5. **倒序执行所有拦截器的postHandle方法。**

6. **前面的步骤有任何异常都会直接倒序触发** afterCompletion

7. 页面成功渲染完成以后，也会倒序触发 afterCompletion

![2021030102](../\Images\2021030102.png)

