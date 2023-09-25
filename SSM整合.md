## 1. 导入依赖

```xml
<packaging>war</packaging>  
<properties>  
    <spring.version>5.3.1</spring.version>  
</properties>  
<dependencies>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-context</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-beans</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <!--springmvc-->  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-web</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-webmvc</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-jdbc</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-aspects</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-test</artifactId>  
        <version>${spring.version}</version>  
    </dependency>  
    <!-- Mybatis核心 -->  
    <dependency>  
        <groupId>org.mybatis</groupId>  
        <artifactId>mybatis</artifactId>  
        <version>3.5.7</version>  
    </dependency>  
    <!--mybatis和spring的整合包-->  
    <dependency>  
        <groupId>org.mybatis</groupId>  
        <artifactId>mybatis-spring</artifactId>  
        <version>2.0.6</version>  
    </dependency>  
    <!-- 连接池 -->  
    <dependency>  
        <groupId>com.alibaba</groupId>  
        <artifactId>druid</artifactId>  
        <version>1.0.9</version>  
    </dependency>  
    <!-- junit测试 -->  
    <dependency>  
        <groupId>junit</groupId>  
        <artifactId>junit</artifactId>  
        <version>4.12</version>  
        <scope>test</scope>  
    </dependency>  
    <!-- MySQL驱动 -->  
    <dependency>  
        <groupId>mysql</groupId>  
        <artifactId>mysql-connector-java</artifactId>  
        <version>8.0.16</version>  
    </dependency>  
    <!-- log4j日志 -->  
    <dependency>  
        <groupId>log4j</groupId>  
        <artifactId>log4j</artifactId>  
        <version>1.2.17</version>  
    </dependency>  
    <dependency>  
        <groupId>com.github.pagehelper</groupId>  
        <artifactId>pagehelper</artifactId>  
        <version>5.2.0</version>  
    </dependency>  
    <!-- 日志 -->  
    <dependency>  
        <groupId>ch.qos.logback</groupId>  
        <artifactId>logback-classic</artifactId>  
        <version>1.2.3</version>  
    </dependency>  
    <!-- ServletAPI -->  
    <dependency>  
        <groupId>javax.servlet</groupId>  
        <artifactId>javax.servlet-api</artifactId>  
        <version>3.1.0</version>  
        <scope>provided</scope>  
    </dependency>  
    <!--json数据处理-->
    <dependency>  
        <groupId>com.fasterxml.jackson.core</groupId>  
        <artifactId>jackson-databind</artifactId>  
        <version>2.12.1</version>  
    </dependency> 
    <!--文件上传--> 
    <dependency>  
        <groupId>commons-fileupload</groupId>  
        <artifactId>commons-fileupload</artifactId>  
        <version>1.3.1</version>  
    </dependency>  
    <!-- Spring5和Thymeleaf整合包 -->  
    <dependency>  
        <groupId>org.thymeleaf</groupId>  
        <artifactId>thymeleaf-spring5</artifactId>  
        <version>3.0.12.RELEASE</version>  
    </dependency>  
</dependencies>
```

## 2. 配置web.xml

```xml
<!-- 配置Spring的编码过滤器 -->  
<filter>  
    <filter-name>CharacterEncodingFilter</filter-name>  
    <filterclass>org.springframework.web.filter.CharacterEncodingFilter</filter-class>  
    <init-param>  
        <param-name>encoding</param-name>  
        <param-value>UTF-8</param-value>  
    </init-param>  
    <init-param>  
        <param-name>forceEncoding</param-name>  
        <param-value>true</param-value>  
    </init-param>  
</filter>  
<filter-mapping>  
    <filter-name>CharacterEncodingFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
</filter-mapping>  
<!-- 配置处理请求方式PUT和DELETE的过滤器 -->  
<filter>  
    <filter-name>HiddenHttpMethodFilter</filter-name>  
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filterclass>  
</filter>  
<filter-mapping>  
    <filter-name>HiddenHttpMethodFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
</filter-mapping>  
<!-- 配置SpringMVC的前端控制器 -->  
<servlet>  
    <servlet-name>DispatcherServlet</servlet-name>  
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servletclass>  
    <!-- 设置SpringMVC的配置文件的位置和名称 -->  
    <init-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>classpath:SpringMVC.xml</param-value>  
    </init-param>  
    <load-on-startup>1</load-on-startup>  
</servlet>  
<servlet-mapping>  
    <servlet-name>DispatcherServlet</servlet-name>  
    <url-pattern>/</url-pattern>  
</servlet-mapping>  
<!-- 设置Spring的配置文件的位置和名称 -->  
<context-param>  
    <param-name>contextConfigLocation</param-name>  
    <param-value>classpath:Spring.xml</param-value>  
</context-param>  
<!-- 配置Spring的监听器 -->  
<listener>  
    <listenerclass>org.springframework.web.context.ContextLoaderListener</listener-class>  
</listener>
```

>Spring提供了监听器ContextLoaderListener，实现ServletContextListener接口，可监听 ServletContext的状态，在web服务器的启动，读取Spring的配置文件，创建Spring的IOC容器。web 应用中必须在web.xml中配置

## 3. SpringMVC配置文件

```xml
<!--扫描组件-->  
<context:component-scan base-package="com.atguigu.ssm.controller"/>  
<!--配置视图解析器-->  
<bean id="viewResolver"  
      class="org.thymeleaf.spring5.view.ThymeleafViewResolver">  
    <property name="order" value="1"/>  
    <property name="characterEncoding" value="UTF-8"/>  
    <property name="templateEngine">  
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">  
            <property name="templateResolver">  
                <bean  
class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">  
                    <!-- 视图前缀 -->  
                    <property name="prefix" value="/WEB-INF/templates/"/>  
                    <!-- 视图后缀 -->  
                    <property name="suffix" value=".html"/>  
                    <property name="templateMode" value="HTML5"/>  
                    <property name="characterEncoding" value="UTF-8" />  
                </bean>  
            </property>  
        </bean>  
    </property>  
</bean>  
<!-- 配置访问首页的视图控制 -->  
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>  
<!-- 配置默认的servlet处理静态资源 -->  
<mvc:default-servlet-handler />  
<!-- 开启MVC的注解驱动 -->  
<mvc:annotation-driven />
```

## 4. 搭建Mybatis环境

- 创建属性文件jdbc.properties
```
jdbc.user=root  
jdbc.password=abc123
jdbc.url=jdbc:mysql://localhost:3306/ssm  
jdbc.driver=com.mysql.cj.jdbc.Driver
```

- mybatis-config.xml
```xml
<configuration>  
    <settings>  
        <!--将下划线映射为驼峰-->  
        <setting name="mapUnderscoreToCamelCase" value="true"/>  
    </settings>  
    <plugins>  
        <!--配置分页插件-->  
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>  
    </plugins>  
</configuration>
```

## 5. Spring的配置文件

```xml
<!--扫描组件-->  
<context:component-scan base-package="com.atguigu.ssm">  
	<context:exclude-filter type="annotation"  
							expression="org.springframework.stereotype.Controller"/>  
</context:component-scan>  
<!-- 引入jdbc.properties -->  
<context:property-placeholder location="classpath:jdbc.properties"/> 
<!-- 配置Druid数据源 -->  
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">  
	<property name="driverClassName" value="${jdbc.driver}"></property>  
	<property name="url" value="${jdbc.url}"></property>  
	<property name="username" value="${jdbc.username}"></property>  
	<property name="password" value="${jdbc.password}"></property>  
</bean>  
<!-- 配置用于创建SqlSessionFactory的工厂bean -->  
<bean class="org.mybatis.spring.SqlSessionFactoryBean">  
	<!-- 设置MyBatis配置文件的路径（可以不设置） -->  
	<property name="configLocation" value="classpath:mybatis-config.xml"/> 
	<!-- 设置数据源 -->  
	<property name="dataSource" ref="dataSource"/>  
	<!-- 设置类型别名所对应的包 -->  
	<property name="typeAliasesPackage" value="com.atguigu.ssm.pojo"/> 
	<!--  
	设置映射文件的路径  
	若映射文件所在路径和mapper接口所在路径一致，则不需要设置  
	-->  
	<!--<property name="mapperLocations" value="classpath:mapper/*.xml"/>-->
	</bean>
<!--  
配置mapper接口的扫描配置  
由mybatis-spring提供，可以将指定包下所有的mapper接口创建动态代理  
并将这些动态代理作为IOC容器的bean管理  
-->  
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">  
	<property name="basePackage" value="com.atguigu.ssm.mapper"></property>  
</bean>  
```