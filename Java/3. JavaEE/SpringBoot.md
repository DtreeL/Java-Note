# 简介

Spring Boot简化Spring应用开发，约定大于配置-->JavaEE一站式解决方案

SpringCloud-->分布式整体解决方案

# Hello World

## pom依赖

```xml
// 父项目
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.1.RELEASE</version>
</parent>

// 需要什么直接从父依赖中取
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

// 该插件可以把应用打包成一个可执行的jar包，打成jar包直接执行浏览器访问
<bulid>
	<plugins>
    	<plugin>
        	<groupId>org.springframwork.boot</groupId>
        	<artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</bulid>
```

## 父项目

点击`spring-boot-starter-parent`进入父项目

```xml
// 还有个父项目是spring-boot-dependencies
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

再点击进入`spring-boot-dependencies`

```xml
// 包含依赖管理器，这才是真正管理依赖的地方
<dependencyManagement>
    <dependencies>
```

## 导入的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

spring-boot-starter：spring boot场景启动器

- 点击进入可以看到里面导入了一些相关依赖，是依赖的一个集合

# 主程序类

```java
@SpringBootApplication
public class SanctuaryShopManagerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SanctuaryShopManagerApplication.class, args);
    }

}
```

@SpringBootApplication：标记在某个类上说明是SpringBoot的主配置类，SpringBoot就应该允许这个类的main方法来启动SpringBoot应用

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

## @SpringBootConfiguration

SpringBoot配置类

- 配置类=配置文件

```java
@Configuration
public @interface SpringBootConfiguration {
```

```java
// 配置类也是容器中的一个组件
@Component
public @interface Configuration {
```

## @EnableAutoConfiguration

开启自动配置

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

```java
// Spring底层注解，给容器中导入一个组件
// 将主配置类下所在的包及子包的组件导入容器中
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```

```java
// 将需要导入的组件以全类名的方式返回，这些组件就会被导入到容器中
// 给容器中导入非常多的自动配置类
@Import(AutoConfigurationImportSelector.class)
```

# 配置文件

application.properties

application.yml

作用在于修改SpringBoot的自动配置的默认值

## yaml语法

```yaml
以空格缩进表示层级，属性与值大小写敏感

普通值：字符串默认不用加双引号（会转义特殊字符）和单引号
k: v

对象k: v
friend:
	name: JJ Lin
	age: 21 
friend: {name: JJ Lin, age: 21}

数组
pets：
 - cat
 - dog
 - pig
pets: [cat,dog,pig]
```

yaml配置文件值获取

```java
类上标记
@Componet
@ConfigurationProperties(prefix = "friend"):从全局配置文件中获取，必须是容器中的组件才能使用
```

@PropertySource：指定配置文件中获取

@importResouce：导入Spring xml配置文件，让配置文件中里面的内容生效

## 位置优先级

config包下大于其他

项目根目录大于classpath路径（java与resources下的一级目录）

# Profile

切换工作环境

1. 使用多个application-dev.properties类似的文件
2. application.properties中spring.profiles.active=dev激活

第二种

yml中使用

```yaml
spring:
	profiles:
		active: dev
---
server:
	port: 8080
spring:
	profiles: dev
---
```

# **==自动配置原理==**

@EnableAutoConfiguration注解，导入了AutoConfigurationImportSelector类：给容器中放入了Bean

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

方法

```java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
        .loadMetadata(this.beanClassLoader);
    
    // getAutoConfigurationEntry方法获取自动配置的键值对
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata,
                                                                              annotationMetadata);
    
    // 返回autoConfigurationEntry.getConfigurations()
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```



```java
protected AutoConfigurationEntry getAutoConfigurationEntry
    // getCandidateConfigurations方法获取候选的配置
	List<String> configurations = getCandidateConfigurations(annotationMetadata, 	attributes);

---
    
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //利用SpringFactoriesLoader.loadFactoryNames
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
                                                                         getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}

---
// 传入的Class是从getSpringFactoriesLoaderFactoryClass()得到，返回的是 EnableAutoConfiguration.class类
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    String factoryTypeName = factoryType.getName();
    return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
}

---
loadSpringFactories    
	
    // 1. 扫描所有jar包下类路径下的的"META-INF/spring.factories"文件，得到它们的url
   	Enumeration<URL> urls = (classLoader != null ?
					classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
					ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
			result = new LinkedMultiValueMap<>();
			// 遍历把文件包装成Propertie
			while (urls.hasMoreElements()) {
				URL url = urls.nextElement();
				UrlResource resource = new UrlResource(url);
				Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                
                // 从propertie中获取一些值放入result结果中
                for (Map.Entry<?, ?> entry : properties.entrySet()) {
					String factoryTypeName = ((String) entry.getKey()).trim();
					for (String factoryImplementationName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
						result.add(factoryTypeName, factoryImplementationName.trim());
					}
				}
			}
			cache.put(classLoader, result);
			return result;

public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
```

总结

1. @EnableAutoConfiguration注解上的@Import把AutoConfigurationImportSelector类加入到容器中

2. AutoConfigurationImportSelector类通过selectImports方法，把每一个Jar包下的META-INF/spring.factories中的内容封装成Properties类型文件

3. 然后把从Properties中取值，把EnableAutoConfiguration名字类型的值表示的类添加到容器中（即XxxAutoConfiguration），自动配置类进入到容器中，自动配置才起作用

4. 每一个自动配置类进行自动配置功能

   例如：HttpEncodingAutoConfiguration类来解释自动配置原理

   ```java
   // 自动配置类，相当于xml配置文件，可以给容器中添加组件
   @Configuration(proxyBeanMethods = false)
   
   // 	启动对应类的ConfigurationProperties功能
   @EnableConfigurationProperties(HttpProperties.class)
   
   // spring底层@Conditional注解，只有满足条件，整个类才会生效
   @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
   
   // 判断当前项目中有没有CharacterEncodingFilter类
   @ConditionalOnClass(CharacterEncodingFilter.class)
   
   @ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
   public class HttpEncodingAutoConfiguration {
       
       //如果该配置类生效，会把@Bean标记的方法产生的Bean放入容器中
       @Bean
   	@ConditionalOnMissingBean
   	public CharacterEncodingFilter characterEncodingFilter() {
   		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
           // 从属性properties中获取编码设置字符集，而properties属性与@ConfigurationProperties(prefix = "spring.http")绑定
   		filter.setEncoding(this.properties.getCharset().name());
   		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
   		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
   		return filter;
   	}
   ```

   所有能配置的属性都在XxxProperties类中

   ```java
   // 从配置文件中获取指定的值和bean进行属性赋值，该类有什么属性，则配置文件中spring.http就能修改什么值
   @ConfigurationProperties(prefix = "spring.http")
   public class HttpProperties {
   ```

   XxxAutoConfiguration类添加的组件从XxxProperties类中获取值，而XxxProperties类绑定了application.properties中的值

- 从External Libraries中的Maven:org.springframework.boot:spring-boot-autoconfigure中可以找到

# 日志

> 替代System.out.prontln

类似于JDBC，日志分为门面和实现类

- 门面：SLF4j、~~JCL~~（很久没更新，Spring默认日志接口）

- 实现：Logback（需要自己的配置文件）

SpringBoot默认：SLF4j+Logback

## 使用

> 遗留问题：Spring、MyBatis等框架自己的日志使用的是不同的日志框架，统一使用SLF4j需要排除原来的依赖，导入xxx-over-slf4j.jar(里面的类都是原来日志的类包名也相同，排除之后找到的是jar包里的类，狸猫换太子)

SpringBoot整合其他框架时，只需要排除掉框架的内置日志系统即可统一日志

当要使用sout时，使用Logger替代

```java
Logger logger = LoggerFactory.getLoader(getClass());

trace > debug > info > warn > error
logger.trace("");
logger.debug("");
logger.info("");
logger.warn("");
logger.error("");

当项目上线时，并不需要注释不必要的语句，可以调整生效的级别，只会打印高级别的语句
```

application.yml中修改配置

```properties
logging.level.com.DtreeL=true;

# 日志文件在当前项目所在盘符的Spring文件夹下的log文件夹下的spring.log文件
logging.path = /spring/log

# 对应日志输入的格式
logging.path.pattren.console =
logging.path.pattren.file = 
# 不指定路径则在当前项目下，生成文件
logging.file= logging.log
```

# Web开发

SpringBoot MVC相关的都在WebMvcAutoConfiguration类中

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
    CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
    if (!registry.hasMappingForPattern("/webjars/**")) {
        customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                                             .addResourceLocations("classpath:/META-INF/resources/webjars/")
                                             .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
    String staticPathPattern = this.mvcProperties.getStaticPathPattern();
    if (!registry.hasMappingForPattern(staticPathPattern)) {
        customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
                                             .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
                                             .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
}
```

1. webjars：以jar包的方式引入静态资源，可以去官网找maven坐标

   - 所有/webjars/**的请求去classpath:/META-INF/resources/webjars/中寻找

2. /**请求，没有处理则去this.resourceProperties.getStaticLocations()中找到四个路径

   ```
   "classpath:/META-INF/resources/",
   "classpath:/resources/", 
   "classpath:/static/", 
   "classpath:/public/"
   ```

## 模板引擎

> Thymeleaf

html页面放在classpath(resoures下)的templates文件夹里，就能自动渲染，只要不使用@ResponseBody转化为Json，就拼接成classpath:templates/xxx.html

### 语法

<html xmlns:th="http://www.thymeleaf.org">：导入名称空间有提示

```java
// 取出Controller参数Map里的hello键的值，将div里的文本内容设置为Xxx，如果前端直接写了的话动态取值会失效
<div th:text="${hello}"><div>
```

## SpringMVC自动配置

WebMvcAutoConfiguration

扩展Spring MVC

- 配置类实现WebMvcConfiguration
- 配置类会一起起作用

# 国际化

1. 国际化配置文件properties(默认)
   - xxx_zh_CN.properties
   - xxx_en_US.properties

2. SpringBoot自动配置好了管理国际化资源的组件

   ```
   public class MessageSourceAutoConfiguration
   ```

3. 页面获取国际化配置的值
4. 通过链接切换语言
   - 超链接携带请求参数?Locale="zh_CN"
   - 编写解析器类：实现LocaleResolver，实现方法中获得参数，处理后创建Locale返回

# 拦截器

实现HandlerInterceptor

# 修改Tomcat

ServerProperties

# 整合数据

SpringBoot底层使用Spring Data（无论关系型还是非关系型）都能操作

# 缓存

JSR-107：缓存规范，只有接口，不是所有缓存都提供了JSR1.7的实现

- CachingProvider：创建配置获取管理控制多个CacheManager（一对多）
- CacheManager：创建配置获取管理控制多个唯一命名的Cache（一对多）
- Cache：类似于Map
  - Entry：存在Cache中的一个键值对
  - Expire：Entry的有效期

Spring缓存抽象（默认ConcurrentMapCache，保存在ConcurrentHashMap）

- 保留了Cache（Spring提供了各种XxxCache实现类）、CacheManager

注解

- @EnableCaching

- @Cacheable：先看缓存，将方法的结果进行缓存，再获取相同的数据就从缓存中取不运行方法

  - cacheName/value：每一个缓存组件有自己的名字。指定存在名字为什么的Map中

  - key：**默认方法参数的值**，方法参数为key，返回值为值。key=#id意思是取出参数id的值作为key

    ```java
    #root.methodName:取方法名
    #result:返回值
    #root.args[0]:取方法的第一个参数
    #参数名
    ```

- @CacheEvict：按key删除，**执行之后删除缓存**，出现异常则不会清除

  - beforeInvocation=true，执行前清除缓存，不管运不运行都清除

- @CachePut：先调用方法后把结果放入缓存，适用于修改

  ```java
  搜索使用id作为参数，缓存的key是id值
      
  那么更新需要指定key为传入的对象的id
  #参数名.id
  #result.id：因为返回的对象与更新的对象是一个对象，id相等
  ```

  @Caching：指定多个缓存规则：按照名字、邮件都能查到缓存

  @CacheConfig：抽取缓存的公共配置

keyGenerator：缓存数据时key的生成策略

serialize：缓存数据时value序列化的策略

## Redis

1. 引入start，自动配置就生效了，引入了RedisTemplate(k-v都是Object)，StringRedisTemplate
2. 配置redis，spring.redis.host=
3. 保存在Redis的对象默认序列化
   - 转换为json：1. 自己把对象转为Json再保存 2. 改变默认的序列化规则

# 消息