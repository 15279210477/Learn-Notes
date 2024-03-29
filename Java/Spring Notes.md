# Spring Notes

## 一、控制反转（IOC）

#### 1.1、获取Spring容器

``` java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");	//通过xml配置获取spring容器
context.getBean("xxx");	// 获取指定bean对象
```

``` xml
<bean id="mysqlImpl" class="com.x.xx.xxx"></bean>
<bean id="UserServiceImpl" calss="com.x.xx.xxx">
	<property name="userDao" ref="mysqlImpl"/>	// set
</bean>
!-- ref：引用spring容器创建好的对象 value：具体的值，基本数据类型
```

#### 1.4、IOC创建对象的方式

> - 使用无参构造创建对象，默认！
>
> - 使用有参构造创建对象。
>
>   - 下标赋值
>
>     ``` xml
>     <bean id="user" class="com.xxx.xx.x.User">
>     	<constructor-arg index="0" value="xx值"/>
>     </bean>
>     ```
>
>   - 通过类型（不建议使用）
>
>     ``` xml
>     <bean id="user" class="com.xxx.xx.x.User">
>     	<constructor-arg type="java.lang.String" value="xx值"/>
>     </bean>
>     ```
>
>   - 通过参数名
>
>     ``` xml
>     <bean>
>     	<constructor-arg name="name" value="xx值"/>
>     </bean>
>     ```
>
> 总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！

-----

## 二、Spring 配置

#### 2.1、别名

``` xml
<alias name="user" alias="userNew"/>
```

#### 2.2、Bean的配置

``` xml
<! --
	id：bean的唯一标识符，也就是相当于我们学的对象名
	class：bean对象所对应的全限定名：包名+类型
	name：也是别名，可取多个别名
	-- >
<bean id="userT" class="com.xx.pojo.userT" name="user2 u2;u3,u4">
	<property name="name" value="value值"/>
</bean>
```

#### 2.3、import

``` xml
<! -- 一般用于团队开发使用，可以将多个配置文件，导入合并为一个 -- >
<import resource="bean.xml"/>
<import resource="bean2.xml"/>
```

----------

## 三、依赖注入

#### 3.1、构造器注入

​	以上

#### 3.2、Set方式注入【重点】

``` xml
<bean id="Student" class="com.xx.pojo.Student">
    <property name="name" value="值"/>	// 普通值注入，value
    <property name="address" ref="address" />	// Bean注入，ref
    <property name="books">		// 数组注入
        <array>
        	<value>红楼梦</value>
            <value>西游记</value>
        </array>
    </property>
    // list:value|map:entry|set:value|null:null标签|props:prop
</bean>
```

#### 3.3、拓展方式注入

​	可以使用p命令空间和c命令空间进行注入

```xml
<beans	xmlns:p="" xmlns:c="">
    <!-- p命名空间注入，可以直接注入属性的值：property -->
	<bean id="user" class="com.kuang.pojo.User" p:name="xx" p:age="18"/>
    <!-- c命名空间注入，通过构造器注入：construst-args -->
    <bean id="user" class="com.kuang.pojo.User" c:name="xx" c:age="18"/>
</beans>
```

#### 3.4、bean的作用域

![image-20210210154004604](../Images/image-20210210154004604.png)

> 1. 单例模式（Spring默认机制）
>
>    ```xml
>    <bean id="user" class="com.kuang.pojo.User" scope="singleton"/>
>    ```
>
> 2. 原型模式：每次从容器get时都会产生新的对象
>
>    ```xml
>    <bean id="user" class="com.kuang.pojo.User" scope="prototype"/>
>    ```
>
> 3. 其余的request、session、application，只能在web开发中使用到。

------

## 四、Bean的自动装配

> - 自动装配是Spring满足bean依赖的一种方式
> - Spring会在上下文中自动寻找，并自动给bean装配属性

**在spring中的三种装配方式**

1. xml中显示的配置
2. java代码中显示的配置
3. 隐式的自动装配bean【重要】

#### 4.1、ByName自动装配

> 会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid！
>
> 注意：需要保证所有的bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一一致！

``` xml
<bean class="xxx"/>
<bean class="xxx"/>
<bean id="people" class="com.xxx.xx.People" autowire="byName">
	<property name="name" value="value值"/>
</bean>
```

#### 4.2、ByType自动装配

> 会自动在容器上下文中查找，和自己对象属性类型相同的bean！
>
> 注意 ：bytype的时候，需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性的类型一致！

```xml
<bean id="people" class="com.xxx.xx.People" autowire="byType">
	<property name="name" value="value值"/>
</bean>
```

#### 4.3、使用注解实现自动装配

> 使用注解须知：
>
> 1. 导入约束：context约束
> 2. 配置注解的支持：`context:annitation-config/`

##### @Autowired

 	如果Autowired自动装配的环境比较复杂，自动装配无法通过一个注解`@Autowried`完成的时候，可以搭配使用`@Qualifier(value="xxx")`去指定一个唯一的bean对象注入。

##### @Resource

​	jdk自带注解，自动装配优先byname，其次bytype。

```
1.如果@Resource注解中指定了name属性，那么则只会根据name属性的值去找bean，如果找不到则报错。
2.如果@Resource注解没有指定name属性，那么会先判断当前注入点名字（属性名字或方法参数名字）是不是存在Bean，如果存在，则直接根据注入点名字取获取bean，如果不存在，则会走@Autowired注解的逻辑，会根据注入点类型去找Bean*
```



小结（两个注解区别）：

> - 都是用来自动装配的，都可以作用在属性字段上。
> - **@Autowried通过bytype的方式实现的，而且必须要求这个对象存在。@Rusource默认通过byname的方式实现，如果找不到则通过bytype实现，都不行则报错。**
> - 执行顺序不同：@Autowried通过bytype的方式实现。@Resource默认通过byname实现。

-------

## 5、使用注解开发

**@Componet**：组件，代表被srping托管

**@Value**：注解属性上代表赋值

**@Scpoe**：注解类上代表bean的作用域

**@ComponentScan("com.xxx.pojo")**：显示的扫描包注解

**@Configration**：代表是个配置类，本身属于@component

- proxyBeanMethod：代理bean方法，默认true（从容器里取bean对象）

**@Import(xxx.class)**：导入其他配置类（同xml）

**@Bean**：注册一个bean（同xml），方法名同bean标签id属性，返回值同bean标签的class属性（用于java类方式注册）

**xml重要标签：**

​	**<context:componet-scan base-package="com.xxx">**：指定需要扫描的包，这个包下的注解就会生效。

**使用Java方式配置Spring**：

```java
// 完全使用配置类方式，就只能通过AnnotationConfig上下文获取容器，通过配置类的class对象来加载
ApplicationContext context = new AnnotationConfigApplicationContext(xxx.class);
```

-----

## 6、代理模式

​	代理模式的分类：

- 静态代理
- 动态代理

#### 6.1、静态代理

#### 6.2、动态代理

​	需要了解两个类：Proxy：代理，InvocationHandler：调用处理程序

​	**好处**：一个动态代理类代理的是一个接口，对应一类业务，即可以代理多个类。

------

## 7、AOP

#### 7.1、Aop在Spring中的作用

<u>提供声明式事务，允许用户自定义切面</u>

- 横切关注点：跨越应用程序多个模块的方法或功能。需要关注的部分，就是横切关注点，如日志、安全、缓存、事务等。

- 切面（Aspect）：横切关注点被模块化的特殊对象，即它是一个类。

- 通知 （Advice）：切面必须要完成的工作，即它是类中的一个方法。

- 目标（Target）：被通知的对象。

- 代理（Proxy）：向目标对象应用通知之后创建的对象。

- 切入点（PointCut）：切面通知，执行的“地点”的定义。

- 连接点（JointPoint）：与切入点匹配的执行点。

  ![image-20210216101212236](../images/image-20210216101212236.png)

**SpringAop中，通过Advice定义横切逻辑，Spring中支持五种类型的Adivce：**

![image-20210216101336630](../images/image-20210216101336630.png)

#### 7.2、使用Spring实现Aop

###### 方式一：使用原生Spring API接口【接口实现】

```xml
<!-- 配置Aop：需要导入aop的约束-->
<aop:config>
	<!-- 切入点：expression：表达式，execution(要执行的位置！ * * * * *) -->
    <aop:pointcut id="pointcut" expression="execution(* com.xxx.xxxServiceImpl.*(..))"/>
    <!-- 执行环绕增加！ -->
    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
</aop:config>
```

**方式二：自定义类来实现Aop【切面定义】**

```xml
<bean id="dity" class="xxx.xx.x"/>
<aop:config>
	<aop:aspect ref="dity">
        <!--切入点-->
    	<aop:pointcut id="point" expression="execution(* com.xxx.xx.Impl.*(..))"/>
    	<!--通知-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:before method="after" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```

#### 方式三：使用注解实现Aop

**开启注解支持：**<aop:aspectj-autoproxy>

**@Aspect**：标注类是一个切面类

**@ Before、@After：**前置通知、后置通知

@**Around**：环绕通知

- ProceedingJoinPoint：给定入参，代表获取的切入的点
  - Signature：获得签名（切入点）
  - proceed：执行方法

----

## 8、声明式事务

#### 8.1、**配置声明式事务**

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

#### 8.2、**结合AOP实现事务的织入**

​	**配置事务的通知**

```xml
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<!-- 给方法配置事务 -->
    <tx:attributes>
    	<tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```

​	**配置事务切入**

```xml
<aop:config>
	<aop:pointcut id="txPointCut" expression="execution(* com.xxx.xx.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
</aop:config>
```

#### 8.3、事务的传播特性

```xml
<tx:advice id="xxAdvice" transaction-manager="xxTransactionManager">
    <tx:attributes>
        <tx:method name="hello*" propagation="NESTED"/>
        <tx:method name="*" propagation="REQUIRES"/>
    </tx:attributes>
</tx:advice>
```

````java
Propagation propagation() default Propagation.REQUIRED;

REQUIRED(0),
SUPPORTS(1),
MANDATORY(2),
REQUIRES_NEW(3),
NOT_SUPPORTED(4),
NEVER(5),
NESTED(6)
````

1. #### REQUIRED（默认）

```
- 如果当前存在事务，则其他方法则加入这个事务，成为一个整体。
- 如果当前方法没有事务，若子方法有事务注解标识，则新建一个事务，并且该子方法运行在该事务中。
- 当前方法存在事务，则加入这个事务，使用同一个事务，所有方法事务回滚。
```

2. #### SUPPORTS

```
- 如果当前没有事务，则不使用事务。
- 如果当前有事务，则使用事务
```

3. #### MANDATORY

```
- 该传播属性强制必须存在一个事务，如果不存在，则抛出异常
```

4. #### REQUIRES_NEW（独立事务）

```
- 如果当前没有事务，则自己新建一个事务，并运行在该事务中
- 如果当前有事务，则将事务挂起，直到新的事务提交或回滚
```

5. #### NOT_SUPPORTED

```
- 如果当前有事务，则把事务挂起，自己不使用该事务。
- 如果当前没有事务，则也不执行事务。
```

6. #### NEVER

```
- 如果当前有事务存在，则抛出异常。（同上。不一样的地方是会抛异常）
```

7. #### NESTED（嵌套事务）

```
- 如果当前没有事务，则自己新建一个事务
- 如果当前有事务，则开启子事务（嵌套事务），当前事务异常，则都会回滚。
- 嵌套事务开始执行时, 它将取得一个savepoint，如果这个嵌套事务失败，我们将回滚到此savepoint（非事务回滚），嵌套事务是外部事务的一部分，只有外部事务结束后它才会被提交或回滚。
外部事务存在时：内部方法异常会回滚至savepoint，同时异常抛至外部，然后才是主事务的rollback；外部方法异常时，会回滚主事务（包括子事务的事务回滚）。
外部事务不存在时：相当于REQUIRES_NEW，内部为一个新事务。
```





