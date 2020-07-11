# 依赖

**数据库方面**：mysql驱动、数据库连接池

**测试方面**：JUnit

**Servlet方面**：Servlet、JSP、Jstl

**MyBatis**：mybatis、mybatis-spring

**Spring方面**：spring-webmvc、spring-jdbc

# 包结构

resources（放配置文件）

- mybatis-config.xml（mybatis核心配置文件）

- applicationContext.xml（spring配置文件）

# MyBatis

1. **首先，需要有数据库的信息建立连接**：数据库配置文件 **database.properties**

   ```properties
   jdbc.driver=com.mysql.jdbc.Driver
   #如果使用的是MySQL8.0+，增加一个时区的配置; &serverTimezone=Asia/Shanghai
   jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
   jdbc.username=root
   jdbc.password=123456
   ```

2. Java程序使用JDBC接口访问关系数据库的时候，需要以下几步：

   - 创建全局`DataSource`实例，表示数据库连接池；
   - 有了Spring，myBatis就不用去做了

# Spring整合MyBatis







