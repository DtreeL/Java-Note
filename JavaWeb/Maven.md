# 介绍

> 所需要的jar包都需要复制到WEB-INF/lib下，每创建一个工程可能需要添加同样的jar包，造成大量重复的文件

Maven就是是专门为Java项目打造的管理和构建工具，它的主要功能有：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。

Maven 必须能找到 Java 源文件，下一步才能编译，而编译之后也必须有一个准确的位置保存编译得到的字节码文件

```ascii
a-maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

所有编译、打包生成的文件都放在`target`目录里

# pom.xml

```xml
<project ...>
	<modelVersion>4.0.0</modelVersion>
    
    <!--一个Maven工程就是由groupId，artifactId和version作为唯一标识-->
	<groupId>com.itranswarp.learnjava</groupId>
    <!--项目名-->
	<artifactId>hello</artifactId>
	<version>1.0</version>
    
	<packaging>jar</packaging>
    
	<properties>
        ...
	</properties>
    
	<dependencies>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
	</dependencies>
</project>
```

## 依赖管理

| scope    | 说明                                          | 示例            |
| :------- | :-------------------------------------------- | :-------------- |
| compile  | 编译时需要用到该jar包（默认）                 | commons-logging |
| test     | 编译Test时需要用到该jar包                     | junit           |
| runtime  | 编译时不需要，但运行时需要用到                | mysql           |
| provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |

注：只有以`-SNAPSHOT`结尾的版本号会被Maven视为开发版本，开发版本每次都会重复下载

# 仓库

本地仓库：为当前电脑所有maven服务（一个版本只有唯一的一个jar包，可以被当前电脑所有maven工程引用避免重复下载jar包）

远程仓库

- 私有仓库：局域网内，公司维护（只需要在本地的`~/.m2/settings.xml`中配置好，使用方式和中央仓位没有任何区别）
- 中央仓库：全世界
- 中央仓库镜像：各大洲为中央仓库分担流量

优先级：本地仓库-->远程仓库-->中央仓库

# 构建流程

自动化实现编译，打包，发布

- lifecycle相当于Java的package，它包含一个或多个phase；
- phase相当于Java的class，它包含一个或多个goal；
- goal相当于class的method，它其实才是真正干活的。

大多数情况，我们只要指定phase，就默认执行这些phase默认绑定的goal，只有少数情况，我们可以直接指定运行一个goal，例如，启动Tomcat服务器：

```bash
mvn tomcat:run
```

---

最常用的构建命令是指定phase，然后让Maven执行到指定的phase：

- mvn clean
- mvn clean compile
- mvn clean test
- mvn clean package

通常情况，我们总是执行phase默认绑定的goal，因此不必指定goal。

# 插件

实际上，执行每个phase，都是通过某个插件（plugin）来执行的，Maven本身其实并不知道如何执行`compile`，它只是负责找到对应的`compiler`插件

# 模块管理

大项目拆分成多个模块

Maven可以有效地管理多个模块，我们只需要把每个模块当作一个独立的Maven项目由父Maven管理，它们有各自独立的`pom.xml`

- parent的`<packaging>`是`pom`而不是`jar`，因为`parent`本身不含任何Java代码。编写`parent`的`pom.xml`只是为了在各个模块中减少重复的配置
- 在根目录执行`mvn clean package`时，Maven根据根目录的`pom.xml`找到包括`parent`在内的共4个`<module>`，一次性全部编译。

父pom用 dependencyManagement 标签管理dependencies

- dependencyManagement 标签只负责管理不负责下载依赖，子模块引用时才下载。所以本地仓库无此依赖并且子工程没引用时会标红

# mvnm

`mvnw`是Maven Wrapper的缩写。

Maven Wrapper就是给一个项目提供一个独立的，指定版本的Maven给它使用。

# 发布Artifact