# 1. 基本配置

>Maven的核心配置文件在"\\conf\\settings.xml"

1. 指定本地仓库

>本地仓库默认值：用户目录/.m2/repository

````xml
<localRepository>指定的仓库地址</localRepository>
````

![[Attachments/Images/Pasted image 20230112160709.png]]

2. 配置镜像仓库

>Maven 下载 jar 包默认访问境外的中央仓库，而国外网站速度很慢
>改成阿里云提供的镜像仓库，访问国内网站，可以让 Maven 下载 jar 包的时候速度更快
>配置的方式如下:

```xml
<mirror>
	<id>nexus-aliyun</id>
	<mirrorOf>central</mirrorOf>
	<name>Nexus aliyun</name>
	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

![[Attachments/Images/Pasted image 20230112161736.png]]

3. 配置 Maven 工程的基础 JDK 版本

>如果按照默认配置运行,Java 工程使用的默认 JDK 版本是 1.5,修改配置的方式是：将 profile 标签整个复制到 settings.xml 文件的 profiles 标签内

```xml
<profile>  
  <id>jdk-17</id>  
  <activation>  
    <activeByDefault>true</activeByDefault>  
    <jdk>17</jdk>  
  </activation>  
  <properties>  
    <maven.compiler.source>17</maven.compiler.source>  
    <maven.compiler.target>17</maven.compiler.target>  
    <maven.compiler.compilerVersion>17</maven.compiler.compilerVersion>  
  </properties>  
</profile>
```

4. 配置环境变量

> Maven 是一个用 Java 语言开发的程序，它必须基于 JDK 来运行，需要通过 JAVA_HOME 来找到 JDK 的安装位置

首先需要配置JAVA_HOME和MAVEN_HOME为根路径 :

![[Attachments/Images/Pasted image 20230112162357.png]]

然后需要在path中添加环境变量 : 

![[Attachments/Images/Pasted image 20230509173800.png]]

在命令行中输入mvn -v 检查是否配置完成 : 

![[Attachments/Images/Pasted image 20230112162538.png]]

# 2. 坐标

Maven使用三个向量在Maven的本地仓库中唯一的定位到一个jar包,分别是

+  groupId：公司或组织的 id
	+ 公司或组织域名的倒序，通常也会加上项目名称
+ artifactId：一个项目或者是项目中的一个模块的 id
	+ 模块的名称，将来作为 Maven 工程的工程名
+  version：版本号
	+ SNAPSHOT 表示快照版本，正在迭代过程中，不稳定的版本
	+ RELEASE 表示正式版本

坐标和仓库中 jar 包的存储路径之间的对应关系 : 

````xml
<groupId>javax.servlet</groupId>
<artifactId>servlet-api</artifactId>
<version>2.5</version>
````

>这样的一个jar包在本地仓库的存储路径为`Maven本地仓库根目录\javax\servlet\servlet-api\2.5\servlet-api-2.5.jar`

# 3. Pom文件

>POM：Project Object Model，项目对象模型
>和 POM 类似的是：DOM（Document Object Model），文档对象模型
>它们都是模型化思想的具体体现
>POM 理念集中体现在 Maven 工程根目录下 pom.xml 这个配置文件中

使用mvn archetype:generate自动生成的pom.xml的解读

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>  
  
  <!-- 当前Maven工程的坐标 -->  
  <groupId>quan</groupId>  
  <artifactId>pro01</artifactId>  
  <version>1.0-SNAPSHOT</version>  
  
  <!-- 当前Maven工程的打包方式，可选值有下面三种： -->  
  <!-- jar：表示这个工程是一个Java工程  -->  
  <!-- war：表示这个工程是一个Web工程 -->  
  <!-- pom：表示这个工程是“管理其他工程”的工程 -->  
  <packaging>jar</packaging>  
  
  <name>pro01</name>  
  <url>http://maven.apache.org</url>  
  
  <properties>  
    <!-- 工程构建过程中读取源码时使用的字符集 -->  
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>  
  </properties>  
  
  <!-- 当前工程所依赖的jar包 -->  
  <dependencies>  
      
    <!-- 使用dependency配置一个具体的依赖 -->  
    <dependency>  
        
      <!-- 在dependency标签内使用具体的坐标依赖我们需要的一个jar包 -->  
      <groupId>junit</groupId>  
      <artifactId>junit</artifactId>  
      <version>3.8.1</version>  
      <!-- scope标签配置依赖的范围 -->  
      <scope>test</scope>  
    </dependency>  
  </dependencies>  
</project>
```

# 4. 依赖

## 4.1 依赖的范围

> <\scope><\/scope>
> 标签的可选值：**compile**/**test**/**provided**/system/runtime/**import**

compile与test的对比

|         | main目录(空间) | test目录(空间) | 开发过程(时间) | 部署到服务器(时间) |
|:-------:|:--------------:|:--------------:|:--------------:|:------------------:|
| compile |      有效      |      有效      |      有效      |        有效        |
|  test   |      无效      |      有效      |      有效      |        无效        |

compile 和 provided 对比

|          | main目录(空间) | test目录(空间) | 开发过程(时间) | 部署到服务器(时间) |
|:--------:|:--------------:| -------------- |:--------------:|:------------------:|
| compile  |      有效      | 有效           |      有效      |        有效        |
| provided |      有效      | 有效           |      有效      |        无效        |
|          |                |                |                |                    |

总结 : 
- compile：通常使用的第三方框架的 jar 包这样在项目实际运行时真正要用到的 jar 包都是以 compile 范围进行依赖的。比如 SSM 框架所需jar包
- test：测试过程中使用的 jar 包，以 test 范围依赖进来。比如 junit
- provided：在开发过程中需要用到的“服务器上的 jar 包”通常以 provided 范围依赖进来。比如 servlet-api、jsp-api。而这个范围的 jar 包之所以不参与部署、不放进 war 包，就是避免和服务器上已有的同类 jar 包产生冲突，同时减轻服务器的负担  

## 4.2 依赖的传递性

> A 依赖 B，B 依赖 C，那么在 A 没有配置对 C 的依赖的情况下，A 里面能不能直接使用 C ?

这个问题取决于 B 依赖 C 时使用的依赖范围
-  B 依赖 C 时使用 compile 范围：可以传递
-  B 依赖 C 时使用 test 或 provided 范围：不能传递，所以需要这样的 jar 包时，就必须在需要的地方明确配置依赖才可以

## 4.3 依赖冲突

![[Attachments/Images/Pasted image 20230112201041.png]]

> 配置依赖的排除其实就是阻止某些 jar 包的传递。因为这样的 jar 包传递过来会和其他 jar 包冲突,需要在dependency中加入exclusions标签排除依赖,配置方式如下:

```xml
<dependency>
	<groupId>com.atguigu.maven</groupId>
	<artifactId>pro01-maven-java</artifactId>
	<version>1.0-SNAPSHOT</version>
	<scope>compile</scope>
	<!-- 使用excludes标签配置依赖的排除	-->
	<exclusions>
		<!-- 在exclude标签中配置一个具体的排除 -->
		<exclusion>
			<!-- 指定要排除的依赖的坐标（不需要写version） -->
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

## 4.4 依赖的继承与聚合

>Maven工程之间，A 工程继承 B 工程 ,  本质上是 A 工程的 pom.xml 中的配置继承了 B 工程中 pom.xml 的配置

操作步骤如下 :

1.  创建父工程,修改打包方式为pom, ``<packaging>pom</packaging>``

2.  在父工程目录下创建子工程,会自动生成module标签, 这就是module与父项目的聚合

> 聚合的好处
> 1. 一键执行 Maven 命令：很多构建命令都可以在“总工程”中一键执行. 
> 以 mvn install 命令为例：Maven 要求有父工程时先安装父工程；有依赖的工程时，先安装被依赖的工程。我们自己考虑这些规则会很麻烦。但是工程聚合之后，在总工程执行 mvn install 可以一键完成安装，而且会自按照正确的顺序执行
> 2. 配置聚合之后，各个模块工程会在总工程中展示一个列表，让项目中的各个模块一目了然

```xml
<modules>  
	<module>pro04-maven-module</module>
	<module>pro05-maven-module</module>
	<module>pro06-maven-module</module>
</modules>
```

3. 子工程会自动创建parent标签

```xml
<!-- 使用parent标签指定当前工程的父工程 -->
<parent>
	<!-- 父工程的坐标 -->
	<groupId>com.atguigu.maven</groupId>
	<artifactId>pro03-maven-parent</artifactId>
	<version>1.0-SNAPSHOT</version>
</parent>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 -->
<!-- <groupId>com.atguigu.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->
```

4. 在父工程中配置依赖的统一管理

```xml
<!-- 使用dependencyManagement标签配置对依赖的管理 -->
<!-- 被管理的依赖并没有真正被引入到工程 -->
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-expression</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
	</dependencies>
</dependencyManagement>
```

5. 子工程中引用那些被父工程管理的依赖

```xml
<!-- 子工程引用父工程中的依赖信息时，可以把版本号去掉。	-->
<!-- 把版本号去掉就表示子工程中这个依赖的版本由父工程决定。 -->
<!-- 具体来说是由父工程的dependencyManagement来决定。 -->
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-core</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-beans</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-expression</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
	</dependency>
</dependencies>
```

6. 可以在父工程中声明自定义属性,在需要使用的地方引用自定义的属性名

```xml
<!-- 通过自定义属性，统一指定Spring的版本 -->
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	
	<!-- 自定义标签，维护Spring版本数据 -->
	<atguigu.spring.version>4.3.6.RELEASE</atguigu.spring.version>
</properties>
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-core</artifactId>
		<version>${atguigu.spring.version}</version>
	</dependency>
<dependencies/>
```

# 5. Maven的生命周期

> 为了让构建过程自动化完成，Maven 设定了三个生命周期，生命周期中的每一个环节对应构建过程中的一个操作, 三个生命周期彼此是独立的
> 在任何一个生命周期内部，执行任何一个具体环节的操作，都是从本周期最初的位置开始执行，直到指定的地方

三个生命周期 :

![[Attachments/Images/Pasted image 20230112213259.png]]

# 6. 常用命令

## 6.1 mvn archetype:generate

>生成一个Maven工程,工程下有[[Maven#2.4 使用mvn archetype:generate自动生成的pom.xml的解读|pom.xml]]文件,如果在后面加上参数mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4可以生成一个web的Maven项目

```cmd
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 7:[直接回车使用默认值]
Define value for property 'groupId': quan[设置groupId]
Define value for property 'artifactId': pro01[设置artifactId]
Define value for property 'version' 1.0-SNAPSHOT: :[设置版本号,直接回车使用默认值]
Define value for property 'package' quan: : liar.quan[设置项目中的包]
Confirm properties configuration:
groupId: quan
artifactId: pro01
version: 1.0-SNAPSHOT
package: liar.quan
 Y: :[直接回车，表示确认。如果前面有输入错误，想要重新输入，则输入 N 再回车]
```

## 6.2 mvn compile 和 mvn test-comile

> 主体程序编译结果存放的目录：target/classes
> 测试程序编译结果存放的目录：target/test-classes

## 6.3 mvn clean

> 删除target目录

## 6.4 测试操作

> 测试的报告存放的目录：target/surefire-reports

## 6.5 mvn package

> 根据pom.xml中定义的打包方式进行打包,打包结果存放的目录：target

## 6.6 mvn install

> 安装的效果是将本地构建过程中生成的 jar 包存入 Maven 本地仓库。这个jar 包在 Maven 仓库中的路径是根据它的坐标生成的

>另外，安装操作还会将 pom.xml 文件转换为 XXX.pom 文件一起存入本地仓库。所以我们在 Maven 的本地仓库中想看一个 jar 包原始的 pom.xml 文件时，查看对应 XXX.pom 文件即可，它们是名字发生了改变，本质上是同一个文件

## 6.7 mvn dependency:list

> 以列表的形式查看当前项目的jar包的依赖关系

## 6.8 mvn dependency:tree

> 以树形结构查看当前 Web 工程的依赖信息