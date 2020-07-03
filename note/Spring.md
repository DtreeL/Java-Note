# 介绍

> Spring是一个支持快速开发Java EE应用程序的框架。
>
> 它提供了一系列底层容器和基础设施，并可以和大量常用的开源框架无缝集成，可以说是开发Java EE应用程序的必备。

Spring Framework主要包括几个模块：

- 支持IoC和AOP的容器；
- 支持JDBC和ORM的数据访问模块；
- 支持声明式事务的模块；
- 支持基于Servlet的MVC开发；
- 支持基于Reactive的Web开发；
- 以及集成JMS、JavaMail、JMX、缓存等其他模块。

总结：容器、SpringMVC、声明式事务、数据访问、缓存等其它锁

# IoC容器

> 容器是一种为某种特定组件的运行提供必要支持的一个软件环境。
>
> 通常来说，使用容器运行组件，除了提供一个组件运行环境之外，容器还提供了许多底层服务。

Spring的核心就是提供了一个IoC容器

- 可以管理所有轻量级的JavaBean组件
- 提供的底层服务包括组件的生命周期管理、配置和组装服务、AOP支持，以及建立在AOP基础上的声明式事务服务等。

## IoC原理

IoC全称Inversion of Control，直译为控制反转。

核心问题是：

1. 谁负责创建组件？
2. 谁负责根据依赖关系组装组件？
3. 销毁时，如何按依赖顺序正确销毁？

解决这一问题的核心方案就是IoC。

## 装配Bean

IoC又称为依赖注入（DI：Dependency Injection）

- 它解决了一个最主要的问题：将组件的创建+配置与组件的使用相分离，并且，由IoC容器负责管理组件的生命周期。

在Spring的IoC容器中，我们把所有组件统称为JavaBean，即配置一个组件就是配置一个Bean。

- 为了能让组件在IoC容器中被“装配”出来，需要某种“注入”机制，setXxx

IoC容器要负责实例化所有的组件，因此，有必要告诉容器如何创建组件，以及各组件的依赖关系。一种最简单的配置是通过XML文件来实现，另一种是注解

### XML

`application.xml`配置文件，告诉Spring的IoC容器应该如何创建并组装Bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userService" class="com.itranswarp.learnjava.service.UserService">
        <!--注入引用类型-->
        <property name="mailService" ref="mailService" />
        <!--注入基本类型-->
        <property name="" value=""/>
    </bean>

    <bean id="mailService" class="com.itranswarp.learnjava.service.MailService" />
</beans>
```

相当于

```java
UserService userService = new UserService();
MailService mailService = new MailService();
userService.setMailService(mailService);
```

只不过Spring容器是通过读取XML文件后使用反射完成的。

---

配置好了，需要创建一个Spring的IoC容器实例，然后加载配置文件，让Spring容器为我们创建并装配好配置文件中指定的所有Bean，这只需要一行代码：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
```

接下来，我们就可以从Spring容器中“取出”装配好的Bean然后使用它：

```java
// 获取Bean:
UserService userService = context.getBean(UserService.class);
// 正常调用:
User user = userService.login("bob@example.com", "password");
```

### ApplicationContext

从其中取Bean，可以看出Spring容器就是`ApplicationContext`

- 它是一个接口，有很多实现类，这里选择`ClassPathXmlApplicationContext`，表示它会自动从classpath中查找指定的XML配置文件。

Spring还提供另一种IoC容器叫`BeanFactory`，使用方式和`ApplicationContext`类似

实际上Application继承自BeanFactory，最大的区别在于ApplicationContext一次创建所有Bean，而BeanFactory只有需要的时候才创建（即getBean时），并且ApplicationContext还支持额外的功能

### 注解

> 使用xml
>
> - 优点是所有的Bean都能一目了然地列出来，并通过配置注入能直观地看到每个Bean的依赖。
> - 缺点是写起来非常繁琐，每增加一个组件，就必须把新的Bean配置到XML中。

`@Component`注解就相当于定义了一个Bean，默认小写开头类名

`@Autowired`相当于指定类型的Bean注入到指定的字段中

- 不但可以写在`set()`方法上，还可以直接写在字段上，甚至可以写在构造方法中

---

`@Configuration`，表示它是一个配置类，因为我们创建`ApplicationContext`时：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

- 使用的实现类是`AnnotationConfigApplicationContext`，必须传入一个标注了`@Configuration`的类名。

`@ComponentScan`，它告诉容器，自动搜索当前类所在的包以及子包，把所有标注为`@Component`的Bean自动创建出来，并根据`@Autowired`进行装配

## 定制Bean

默认Bean为单例

```java
// 声明为Prototype（原型）
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
```

注入List，一个@Autowired标记的List字段

- Spring会自动把所有该字段的类型Bean装配为一个`List`注入进来
- `@Order(1)`标在组件类上，指明注入时的顺序

注意：没有找到对应类型的Bean，它会抛出NoSuchBeanDefinitionException异常

- 可以给`@Autowired`增加一个`required = false`的参数：找不到，就忽略
- **非常适合有定义就使用定义，没有就使用默认值的情况**

---

一个Bean不在我们自己的package管理之类，如何创建？

在`@Configuration`类中编写一个Java方法创建并返回它，注意给方法标记一个`@Bean`注解：

```java
@Configuration
@ComponentScan
public class AppConfig {
    // 创建一个Bean，方法名即变量名
    @Bean
    ZoneId createZoneId() {
        return ZoneId.of("Z");
    }
}
```

Spring对标记为`@Bean`的方法只调用一次，因此返回的Bean仍然是单例。

### 初始化和销毁

一个Bean在注入必要的依赖后，需要进行初始化（监听消息等）。在容器关闭时，有时候还需要清理资源（关闭连接池等）。

我们通常会定义一个`init()`方法进行初始化，定义一个`shutdown()`方法进行清理，然后，引入JSR-250定义的Annotation：

```xml
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```

在Bean的初始化和清理方法上标记`@PostConstruct`和`@PreDestroy`

- Spring只根据Annotation查找*无参数*方法，对方法名不作要求。

---

### 使用别名

在`@Configuration`类中创建了多个同类型的Bean，Spring会报`NoUniqueBeanDefinitionException`异常，意思是出现了重复的Bean定义。这个时候，需要给每个Bean添加不同的名字：

- 可以用`@Bean("name")`指定别名，也可以用`@Bean`+`@Qualifier("name")`指定别名。
- 还有一种方法是把其中某个Bean指定为`@Primary`
  - 如果没有指出Bean的名字，Spring会注入标记有`@Primary`的Bean。

### 使用FactoryBean

Spring也提供了工厂模式，允许定义一个工厂，然后由工厂创建真正的Bean。

- 用工厂模式创建Bean需要实现`FactoryBean`接口

---

当一个Bean实现了`FactoryBean`接口后，Spring会先实例化这个工厂，然后调用`getObject()`创建真正的Bean。

- 如果定义了一个`FactoryBean`，要注意Spring创建的Bean实际上是这个`FactoryBean`的`getObject()`方法返回的Bean。
- 为了和普通Bean区分，我们通常都以`XxxFactoryBean`命名。

## 使用Resources

> 经常会读取配置文件、资源文件等。使用Spring容器时，我们也可以把“文件”注入进来，方便程序读取。

可以像`String`、`int`一样使用`@Value`注入

- ```java
  @Value("classpath:/logo.txt")
  private Resource resource;
  ```

- 直接调用`Resource.getInputStream()`就可以获取到输入流，

---

更简单的`@PropertySource`来自动读取配置文件

在`@Configuration`配置类上再添加一个注解

```java
@PropertySource("app.properties") // 表示读取classpath的app.properties

@Value("${app.zone:Z}")// 还可以写在方法参数中
String zoneId;
```

- `"${app.zone}"`表示读取key为`app.zone`的value，如果key不存在，启动将报错；
- `"${app.zone:Z}"`表示读取key为`app.zone`的value，但如果key不存在，就使用默认值`Z`。

注意：`#{}`表示从JavaBean读取属性。

- `"#{smtpConfig.host}"`的意思是，从名称为`smtpConfig`的Bean读取`host`属性，即调用`getHost()`方法。

## 条件装配

Spring为应用程序准备了Profile这一概念，用来表示不同的环境。例如，我们分别定义开发、测试和生产这3个环境：

- native
- test
- production

创建某个Bean时，Spring容器可以根据注解`@Profile`来决定是否创建。

# Spring Bean的生命周期

Bean 的生命周期概括起来就是 **4 个阶段**：

1. 实例化（Instantiation）；
2. 属性赋值（Populate）；
3. 初始化（Initialization）；
4. 销毁（Destruction）。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/8gSYd924hdtaiacv70zFygFQWuROmn641xfRVSRLibjDAL7zpiasx3UI2NACX2re5Ay4hP3bsoNiaTgtCGMNpjnqUA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

1. 实例化：第 1 步，实例化一个 bean 对象；
2. 属性赋值：第 2 步，为 bean 设置相关属性和依赖；
3. 初始化：第 3~7 步，步骤较多，其中第 5、6 步为初始化操作，第 3、4 步为在初始化前执行，第 7 步在初始化后执行，该阶段结束，才能被用户使用；
4. 销毁：第 8~10步，第8步不是真正意义上的销毁（还没使用呢），而是先在使用前注册了销毁的相关调用接口，为了后面第9、10步真正销毁 bean 时再执行相应的方法。

初始化

- 若 Spring 检测到 bean 实现了 Aware 接口，则会为其注入相应的依赖。所以**通过让bean 实现 Aware 接口，则能在 bean 中获得相应的 Spring 容器资源**。

  Spring 中提供的 Aware 接口有：

  1. BeanNameAware：注入当前 bean 对应 beanName；
  2. BeanClassLoaderAware：注入加载当前 bean 的 ClassLoader；
  3. BeanFactoryAware：注入 当前BeanFactory容器 的引用。

# AOP

> Aspect Oriented Programming，即面向切面编程。
>
> AOP本质上只是一种代理模式的实现方式

OOP把系统看作多个对象的交互，AOP把系统分解为不同的关注点，或者称之为切面（Aspect）。

整个系统还要求关注安全检查、日志、事务等功能，这些功能实际上“横跨”多个业务方法，为了实现这些功能，不得不在每个业务方法上重复编写代码。

在Java平台上，对于AOP的织入，有3种方式：

1. 编译期：在编译时，由编译器把切面调用编译进字节码，这种方式需要定义新的关键字并扩展编译器，AspectJ就扩展了Java编译器，使用关键字aspect来实现织入；
2. 类加载器：在目标类被装载到JVM时，通过一个特殊的类加载器，对目标类的字节码重新“增强”；
3. 运行期：目标对象和切面都是普通Java类，通过JVM的动态代理功能或者第三方库实现运行期动态织入。

## 装配AOP

使用AOP非常简单，一共需要三步：

1. 定义执行方法，并在方法上通过AspectJ的注解告诉Spring应该在何处调用此方法；
2. 标记`@Component`和`@Aspect`；
3. 在`@Configuration`类上标注`@EnableAspectJAutoProxy`。

拦截器有以下类型：

- @Before：这种拦截器先执行拦截代码，再执行目标代码。如果拦截器抛异常，那么目标代码就不执行了；
- @After：这种拦截器先执行目标代码，再执行拦截器代码。无论目标代码是否抛异常，拦截器代码都会执行；
- @AfterReturning：和@After不同的是，只有当目标代码正常返回时，才执行拦截器代码；
- @AfterThrowing：和@After不同的是，只有当目标代码抛出了异常时，才执行拦截器代码；
- @Around：能完全控制目标代码是否执行，并可以在执行前后、抛异常后执行任意拦截代码，可以说是包含了上面所有功能。

### 原理：cglib代理

由于静态代理需要实现目标对象的相同接口，那么可能会导致代理类会非常非常多....不好维护**---->因此出现了动态代理

动态代理也有个约束：**目标对象一定是要有接口的，没有接口就不能实现动态代理**.....----->因此出现了cglib代理

cglib代理也叫子类代理，**从内存中构建出一个子类来扩展目标对象的功能**

-  Spring对接口类型使用JDK动态代理，对普通类使用CGLIB创建子类。如果一个Bean的class是final，Spring将无法为其创建子类。
  - CGLib代理对象运行速度要比JDK的代理对象要快

- 是**单例的最好使用CGLib代理**，如果是多例的最好使用JDK代理

以`LoggingAspect.doAccessCheck()`为例，要把它注入到`UserService`的每个`public`方法中，最简单的方法是编写一个子类，并持有原始实例的引用：

```java
public UserServiceAopProxy extends UserService {
    private UserService target;
    private LoggingAspect aspect;

    public UserServiceAopProxy(UserService target, LoggingAspect aspect) {
        this.target = target;
        this.aspect = aspect;
    }

    public User login(String email, String password) {
        // 先执行Aspect的代码:
        aspect.doAccessCheck();
        // 再执行UserService的逻辑:
        return target.login(email, password);
    }

    public User register(String email, String password, String name) {
        aspect.doAccessCheck();
        return target.register(email, password, name);
    }

    ...
}
```

Spring容器启动时为我们自动创建的注入了Aspect的子类，它取代了原始的`UserService`（原始的`UserService`实例作为内部变量隐藏在`UserServiceAopProxy`中）

### 使用注解装配

使用AOP时，被装配的Bean最好自己能清清楚楚地知道自己被安排了

通过`@Transactional`，某个方法是否启用了事务就一清二楚了。因此，装配AOP的时候，使用注解是最好的方式。

为了监控应用程序的性能，我们定义一个性能监控的注解：

```java
@Target(METHOD)
@Retention(RUNTIME)
public @interface MetricTime {
    String value();
}
```

在需要被监控的关键方法上标注该注解：

```java
@Component
public class UserService {
    // 监控register()方法性能:
    @MetricTime("register")
    public User register(String email, String password, String name) {
        ...
    }
    ...
}
```

然后，我们定义`MetricAspect`：

```java
@Aspect
@Component
public class MetricAspect {
    @Around("@annotation(metricTime)")
    public Object metric(ProceedingJoinPoint joinPoint, MetricTime metricTime) throws Throwable {
        String name = metricTime.value();
        long start = System.currentTimeMillis();
        try {
            return joinPoint.proceed();
        } finally {
            long t = System.currentTimeMillis() - start;
            // 写入日志或发送至JMX:
            System.err.println("[Metrics] " + name + ": " + t + "ms");
        }
    }
}
```

Spring通过CGLIB创建的代理类，不会初始化代理类自身继承的任何成员变量，包括final类型的成员变量！

因此，正确使用AOP，我们需要一个避坑指南：

1. 访问被注入的Bean时，总是调用方法而非直接访问字段；
2. 编写Bean时，如果可能会被代理，就不要编写`public final`方法。

这样才能保证有没有AOP，代码都能正常工作。

# 访问数据库

## 使用JDBC

Java程序使用JDBC接口访问关系数据库的时候，需要以下几步：

- 创建全局`DataSource`实例，表示数据库连接池；
- 在需要读写数据库的方法内部，按如下步骤访问数据库：
  - 从全局`DataSource`实例获取`Connection`实例；
  - 通过`Connection`实例创建`PreparedStatement`实例；
  - 执行SQL语句，如果是查询，则通过`ResultSet`读取结果集，如果是修改，则获得`int`结果。

在Spring使用JDBC

- 首先通过IoC容器创建并管理一个`DataSource`实例
- 然后，Spring提供了一个`JdbcTemplate`，可以方便地让我们操作JDBC，因此，通常情况下，我们会实例化一个`JdbcTemplate`

## 使用声明式事务

> 在Spring中操作事务，没必要手写JDBC事务，可以使用Spring提供的高级接口来操作事务。

Spring提供了一个`PlatformTransactionManager`来表示事务管理器，所有的事务都由它负责管理。而事务由`TransactionStatus`表示

JavaEE除了提供JDBC事务外，它还支持分布式事务JTA（Java Transaction API）

Spring为了同时支持JDBC和JTA两种事务模型，就抽象出`PlatformTransactionManager`。因为我们的代码只需要JDBC事务，因此，在`AppConfig`中，需要再定义一个`PlatformTransactionManager`对应的Bean，它的实际类型是`DataSourceTransactionManager`：

- @EnableTransactionManagement
- 对需要事务支持的方法，加一个`@Transactional`注解
- 如果发生了`RuntimeException`，Spring的声明式事务将自动回滚。

---

事务传播

默认传播级别就是REQUIRED，它的意思是，如果当前没有事务，就创建一个新事务，如果当前有事务，就加入到当前事务中执行。

`SUPPORTS`：表示如果有事务，就加入到当前事务，如果没有，那也不开启事务执行。这种传播级别可用于查询方法，因为SELECT语句既可以在事务内执行，也可以不需要事务；

`MANDATORY`：表示必须要存在当前事务并加入执行，否则将抛出异常。这种传播级别可用于核心更新逻辑，比如用户余额变更，它总是被其他事务方法调用，不能直接由非事务方法调用；

`REQUIRES_NEW`：表示不管当前有没有事务，都必须开启一个新的事务执行。如果当前已经有事务，那么当前事务会挂起，等新事务完成后，再恢复执行；

`NOT_SUPPORTED`：表示不支持事务，如果当前有事务，那么当前事务会挂起，等这个方法执行完成后，再恢复执行；

`NEVER`：和`NOT_SUPPORTED`相比，它不但不支持事务，而且在监测到当前有事务时，会抛出异常拒绝执行；

`NESTED`：表示如果当前有事务，则开启一个嵌套级别事务，如果当前没有事务，则开启一个新事务。

- 默认的`REQUIRED`已经满足绝大部分需求，`SUPPORTS`和`REQUIRES_NEW`在少数情况下会用到，其他基本不会用到，

# 开发Web

## SpringMVC

Spring提供的是一个IoC容器，所有的Bean，包括Controller，都在Spring IoC容器中被初始化，

而Servlet容器由JavaEE服务器提供（如Tomcat），Servlet容器对Spring一无所知，他们之间到底依靠什么进行联系，又是以何种顺序初始化的？