# JDK

## 下载

下载地址：https://www.oracle.com/java/technologies/javase-downloads.html

- #### 在Java Archive中找早期版本

- #### Java SE Development Kit 8u202，Windows x64，jdk-8u202-windows-x64.exe（可能需要登录）

## 安装

1. 环境相关软件统一安装在Environment文件夹下，如`D:\Environment\jdk8u202\`
2. 设置环境变量
   1. 右键我的电脑-->属性-->高级系统设置
   2. 新建名为JAVA_HOME的系统变量，变量值为安装路径即`D:\Environment\jdk8u202`
   3. 配置Path变量，两个bin目录，%%为取JAVA_HOME变量的值
      - `%JAVA_HOME%\bin`
      - `%JAVA_HOME%\jre\bin`
      - 把`JAVA_HOME`的`bin`目录添加到`PATH`中是为了在任意文件夹下都可以运行`java`相关指令
3. cmd，输入`java -version`验证

## 卸载

1. 右键我的电脑-->属性-->高级系统设置
2. 环境变量JAVA_HOME，删除路径所指文件夹
3. 清理（删除）环境变量
   - JAVA_HOME
   - Path里与JAVA_HOME相关的
4. cmd输入`java -version`验证

# Maven

## 下载

下载地址：http://maven.apache.org/index.html

- Link的apache-maven-3.6.3-bin.zip

## 安装

解压到Environment文件夹

1. 设置MAVEN_HOME环境变量
2. 系统变量Path添加`%MAVEN_HOME%\bin`
3. `mvn --version`验证

## 配置

1. 配置Maven本地仓库

   - 安装目录下新建maven-repo文件夹作为仓库文件夹

2. conf文件夹中settings.xml配置

   ```xml
   // 下载的jar包存放在哪里
   <localRepository>D:\Environment\apache-maven-3.6.3\maven-repo</localRepository>
   
   // 阿里云镜像加速
   <mirror>
       <id>alimaven</id>
       <name>aliyun maven</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
       <mirrorOf>central</mirrorOf>        
   </mirror>
   ```

# Tomcat

## 下载

下载地址：https://tomcat.apache.org/download-80.cgi

- Core
  - 64-bit Windows zip

必须配置有JAVA_HOME系统变量

## 启动

1. bin/startup.bat双击启动
2. catalina run 这个命令启动Tomcat
   - 哪怕有错误，也可以清楚的看到tomcat失败的原因，不会一闪而过。

## 关闭

第一种：Ctrl+C键 关闭Tomcat服务器

第二种：点击Tomcat窗口的右上角关闭按钮 （暴力停止服务器）

第三种：找到tomcat目录/bin/shutdown.bat文件，双击执行关闭Tomcat。

## 配置

**conf/server.xml**

- 修改端口：修改port值

## 部署

1. 有一个网站（一个文件夹），把文件夹复制到tomcat的webapps目录下。
   - 文件夹的名字，就是网站或者工程的访问目录
2. 网站目录压缩成war包部署到tomcat的webapps目录下中
   - war包其实也是zip，修改后缀即可
3. 如果webapps下面有一个ROOT的项目。那么在访问的时候，直接可以省略项目的名字/ 表示找到root目录

# MySQL

## 下载

下载地址：https://dev.mysql.com/downloads/mysql/

- Looking for previous GA versions？查看之前版本

- **ZIP Archive**后的Download即可

## 安装

解压到Environment文件夹下即可

## 配置



# IDEA

## 下载

下载地址：https://www.jetbrains.com/idea/download/#section=windows

- 选择**Ultimate**版本

## 破解

版本：2020.1.2

下载对应破解文件按照教程走即可

## 配置

安装目录的bin目录下，用文本类软件打开idea64.exe.vmoption，依照电脑内存修改最小和最大值，能提高IDEA运行速度

- -Xms128m
- -Xmx750m

安装目录的bin目录下的idea.properties存储着idea配置文件路径相关等

## 使用设置

设置JDK

- File-->New Projects Settings-->Settings  for New Projects...

## 插件

阿里巴巴开发规范手册：Alibaba Java Coding Guidelines

可视化的字节码查看插件： jclasslib Bytecode viewer

代码智能提示：Codota

确定代码块位置：Rainbow Brackets

根据代码调用链路自动生成时序图：SequenceDiagram

- 在某个类的某个函数中，右键 --> Sequence Diagaram 即可调出

lombok

生成各种ignore文件，一键创建git ignore文件的模板：.ignore

翻译：Translation

根据数据库表生成代码：easy code