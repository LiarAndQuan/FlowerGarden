## 1. Spring FrameWork介绍

> Spring FrameWork五大功能模块

|        功能模块         |                       功能介绍                       |
|:-----------------------:|:----------------------------------------------------:|
|     Core Container      | 核心容器,在Spring环境下使用任何功能都必须基于IOC容器 |
|       AOP&Aspects       |                     面向切面编程                     |
|         Testing         |         提供了对Junit或TestNG测试框架的整合          |
| Data Access /Interation |             提供了对数据访问/集成的功能              |
|        SpringMVC        |           提供了面向Web应用程序的集成功能            |
|                         |                                                      |

## 2. IOC

> IOC：Inversion of Control，反转控制
> DI：Dependency Injection，依赖注入
> IOC 就是一种反转控制的思想， 而 DI 是对 IOC 的一种具体实现

### 2.1 IOC容器在Spring FrameWork中的实现

> Spring 的 IOC 容器就是 IOC 思想的一个落地的产品实现。IOC 容器中管理的组件也叫做 bean。在创建 bean 之前，首先需要创建 IOC 容器。Spring 提供了 IOC 容器的两种实现方式:

1. BeanFactory
> 这是 IOC 容器的基本实现，是 Spring 内部使用的接口。面向 Spring 本身，不提供给开发人员使用

2. ApplicationContext
> BeanFactory 的子接口，提供了更多高级特性。面向 Spring 的使用者，几乎所有场合都使用 ApplicationContext 而不是底层的 BeanFactory

3. ApplicationContext的主要实现类
![[Attachments/Images/Pasted image 20230119155047.png|700]]
| 类型名                          | 简介                                                                                                                           |
|:------------------------------- |:------------------------------------------------------------------------------------------------------------------------------ |
| ClassPathXmlApplicationContext  | 通过读取类路径下的 XML 格式的配置文件创建 IOC 容器 对象                                                                        |
| FileSystemXmlApplicationContext | 通过文件系统路径读取 XML 格式的配置文件创建 IOC 容 器对象                                                                      |
| ConfigurableApplicationContext  | ApplicationContext 的子接口，包含一些扩展方法 refresh() 和 close() ，让 ApplicationContext 具有启动、 关闭和刷新上下文的能力。 |
| WebApplicationContext           | 专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对 象，并将对象引入存入 ServletContext 域中。                                   |

### 2.2 基于xml管理Bean

#### 基本配置

1. 引入依赖
```xml
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-context</artifactId>  
    <version>6.0.3</version>  
</dependency>  
<dependency>  
    <groupId>junit</groupId>  
    <artifactId>junit</artifactId>  
    <version>4.13.2</version>  
</dependency>
```

2. 创建applicationContext.xml
![[Attachments/Images/Pasted image 20230119164544.png|500]]

```java
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
	   xsi:schemaLocation="http://www.springframework.org/schema/beans    http://www.springframework.org/schema/beans/spring-beans.xsd">   
</beans>
```

3. 在Spring的配置文件中配置bean
```xml
<!--
配置HelloWorld所对应的bean，即将HelloWorld的对象交给Spring的IOC容器管理
通过bean标签配置IOC容器所管理的bean
属性：
	id：设置bean的唯一标识
	class：设置bean所对应类型的全类名
-->
<bean id="helloworld" class="com.atguigu.spring.bean.HelloWorld"></bean>
```

#### 获取Bean

1. 根据id获取
```java
@Test  
public void getBean() {  
    //初始化IOC容器  
	ClassPathXmlApplicationContext application = new  ClassPathXmlApplicationContext("applicationContext.xml");  
    //根据id获取对应的对象  
    Object helloWorld = application.getBean("helloWorld");  
    System.out.println(helloWorld);  
}
```
2. 根据类型获取
```java
@Test  
public void getBean() {  
    //初始化IOC容器  
    ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext("applicationContext.xml");  
    //根据类型获取对应的对象  
    Object helloWorld = application.getBean(HelloWorld.class);  
    System.out.println(helloWorld);  
}
```
3. 根据id和类型获取
```java
@Test  
public void getBean() {  
    //初始化IOC容器  
    ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext("applicationContext.xml");  
    //根据id获取对应的对象  
    Object helloWorld = application.getBean("helloWorld", HelloWorld.class);  
    System.out.println(helloWorld);  
}
```

![[Attachments/Images/Pasted image 20230224233758.png]]

> 注意: Spring底层默认通过反射技术调用组件类的无参构造器来创建组件对象,所以被管理的类必须具有无参构造器

存疑:
![[Attachments/Images/Pasted image 20230224235019.png]]

#### 依赖注入

- setter注入

> 在配置Bean的时候可以通过property标签来指定属性值, 这种方法是通过setXxx( )方法实现的,所以只有实现了这个方法的属性才能够通过这种方法赋值

```xml
<bean id="helloWorld" class="HelloWorld">  
    <property name="id" value="10"/>  
    <property name="name" value="张三"/>  
</bean>
```

- 构造器注入

> 不仅可以通过setter注入,还可以通过调用构造器实现属性注入

```xml
<bean id="helloWorld" class="HelloWorld">  
    <constructor-arg  value="1"/>  
    <constructor-arg  value="李四"/> 
    <!-- index属性：指定参数所在位置的索引（从0开始） name属性：指定参数名-->
</bean>
```

> 赋值时的特殊情况:
> - 如果需要赋值为null,需要使用字标签<null/>
> - 小于号在XML文档中用来定义标签的开始,不能随便使用,需要使用xml实体或者CDATA节

```xml
<bean id="helloWorld" class="HelloWorld">  
    <property name="id" value="10"/>  
    <property name="name">  
        <null/>  
    </property>  
    <property name="name" value="a&lt;b"/>  
    <property name="name">  
        <value><![CDATA[a<b]]></value>  
    </property>  
</bean>
```

-  特殊属性的注入

>类类型的属性可以通过引用IOC容器中某个bean的id为对应的bean的属性赋值

```xml
<bean id="clazzOne" class="com.atguigu.spring.bean.Clazz">
	<property name="clazzId" value="1111"></property>
	<property name="clazzName" value="财源滚滚班"></property>
</bean>
<bean id="studentFour" class="com.atguigu.spring.bean.Student">
	<property name="id" value="1004"></property>
	<property name="name" value="赵六"></property>
	<property name="age" value="26"></property>
	<property name="sex" value="女"></property>
	<!-- ref属性：引用IOC容器中某个bean的id，将所对应的bean为属性赋值 -->
	<property name="clazz" ref="clazzOne"></property>
</bean>
```

> 类类型的属性还可以通过内部bean方式赋值

```xml
<bean id="studentFour" class="com.atguigu.spring.bean.Student">
	<property name="id" value="1004"></property>
	<property name="name" value="赵六"></property>
	<property name="age" value="26"></property>
	<property name="sex" value="女"></property>
	<property name="clazz">
	<!-- 在一个bean中再声明一个bean就是内部bean -->
	<!-- 内部bean只能用于给属性赋值，不能在外部通过IOC容器获取，因此可以省略id属性 -->
		<bean id="clazzInner" class="com.atguigu.spring.bean.Clazz">
			<property name="clazzId" value="2222"></property>
			<property name="clazzName" value="远大前程班"></property>
		</bean>
	</property>
</bean>
```

>还可以通过级联的方式为类类型的属性赋值

```xml
<bean id="studentFour" class="com.atguigu.spring.bean.Student">
	<property name="id" value="1004"></property>
	<property name="name" value="赵六"></property>
	<property name="age" value="26"></property>
	<property name="sex" value="女"></property>
	<!-- 一定先引用某个bean为属性赋值，才可以使用级联方式更新属性 -->
	<property name="clazz" ref="clazzOne"></property>
		<property name="clazz.clazzId" value="3333"></property>
		<property name="clazz.clazzName" value="最强王者班"></property>
</bean>
```

2. 数组类型

>通过array标签为数组类型赋值

```xml
<bean id="studentFour" class="com.atguigu.spring.bean.Student">  
    <property name="id" value="1004"></property>  
    <property name="name" value="赵六"></property>  
    <property name="age" value="26"></property>  
    <property name="sex" value="女"></property>  
    <!-- ref属性：引用IOC容器中某个bean的id，将所对应的bean为属性赋值 -->  
    <property name="clazz" ref="clazzOne"></property>  
    <property name="hobbies">  
        <array>  
            <value>抽烟</value>  
            <value>喝酒</value>  
            <value>烫头</value>  
        </array>  
    </property>  
</bean>
```

3. 集合类型

- List和Set
> 通过list和set标签为其赋值

```xml
<bean id="clazzTwo" class="com.atguigu.spring.bean.Clazz">
	<property name="clazzId" value="4444"></property>
	<property name="clazzName" value="Javaee0222"></property>
	<property name="students">
		<list>
			<ref bean="studentOne"></ref>
			<ref bean="studentTwo"></ref>
			<ref bean="studentThree"></ref>
		</list>
	</property>
</bean>
```

-  Map
> 通过map,entry,key标签为其赋值

```xml
<bean id="teacherOne" class="com.atguigu.spring.bean.Teacher">
	<property name="teacherId" value="10010"></property>
	<property name="teacherName" value="大宝"></property>
</bean>
<bean id="teacherTwo" class="com.atguigu.spring.bean.Teacher">
	<property name="teacherId" value="10086"></property>
	<property name="teacherName" value="二宝"></property>
</bean>
<bean id="studentFour" class="com.atguigu.spring.bean.Student">  
    <property name="teacherMap">  
        <map>  
            <entry>  
                <key>   
                    <value>10010</value>  
                </key>  
                <ref bean="teacherOne"></ref>  
            </entry>  
            <entry>  
                <key>  
                    <value>10086</value>  
                </key>  
                <ref bean="teacherTwo"></ref>  
            </entry>  
        </map>  
    </property>  
</bean>
```

- 集合属性还可以引用集合类型的bean来赋值
```xml
<!--list集合类型的bean-->  
<util:list id="students">  
    <ref bean="studentOne"></ref>  
    <ref bean="studentTwo"></ref>  
    <ref bean="studentThree"></ref>  
</util:list>  
<!--map集合类型的bean-->  
<util:map id="teacherMap">  
    <entry>  
        <key>  
            <value>10010</value>  
        </key>  
        <ref bean="teacherOne"></ref>  
    </entry>  
    <entry>  
        <key>  
            <value>10086</value>  
        </key>  
        <ref bean="teacherTwo"></ref>  
    </entry>  
</util:map>  

<bean id="clazzTwo" class="com.atguigu.spring.bean.Clazz">   
    <property name="students" ref="students"></property>  
</bean>  

<bean id="studentFour" class="com.atguigu.spring.bean.Student">  
<!-- ref属性：引用IOC容器中某个bean的id，将所对应的bean为属性赋值 -->  
	<property name="clazz" ref="clazzOne"></property>  
	<property name="hobbies">  
	    <array>  
	        <value>抽烟</value>  
	        <value>喝酒</value>  
	        <value>烫头</value>  
	    </array>  
	</property>  
<property name="teacherMap" ref="teacherMap"></property>
```

>P命名空间
>引入p命名空间后，可以通过以下方式为bean的各个属性赋值

```xml
<bean id="studentSix" class="com.atguigu.spring.bean.Student"
p:id="1006" p:name="小明" p:clazz-ref="clazzOne" p:teacherMap-ref="teacherMap"/>
```

#### 引入外部属性文件配置数据源

1.  加入依赖
```xml
<dependency>  
    <groupId>mysql</groupId>  
    <artifactId>mysql-connector-java</artifactId>  
    <version>8.0.32</version>  
</dependency>  
<dependency>  
    <groupId>com.alibaba</groupId>  
    <artifactId>druid</artifactId>  
    <version>1.2.16</version>  
</dependency>
```
2. 创建外部属性文件jdbc.properties
3. 引入属性文件并且配置bean
```xml
<!--引入外部属性文件-->  
<context:property-placeholder location="classpath:jdbc.properties"/>  
  
<!--配置bean-->  
<bean id="duridDataSource" class="com.alibaba.druid.pool.DruidDataSource">  
    <property name="url" value="${jdbc.url}"/>  
    <property name="driverClassName" value="${jdbc.driver}"/>  
    <property name="username" value="${jdbc.user"/>  
    <property name="password" value="${jdbc.password"/>  
</bean>
```
4. 测试连接池
```java
@Test  
public void dataSourceTest() {  
	ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("spring-datasource.xml");  
    DruidDataSource druidDataSource = (DruidDataSource) classPathXmlApplicationContext.getBean(DataSource.class);  
    System.out.println(druidDataSource);  
}
```

#### bean的作用域

> 在Spring中可以通过配置bean标签的scope属性来指定bean的作用域范围，各取值含义参加下表

| 取值      | 含义     | 创建对象的实机  |
| --------- | -------- | --------------- |
| sigeton(默认)   | 单实例   | IOC容器初始化时 |
| prototype | 多个实例 | 获取bean时      |
|           |          |                 |

>如果实在WebApplicationContext环境下还会有另外两个作用域(不常用)

| 取值    | 含义                 |
| ------- | -------------------- |
| request | 在一个请求范围内有效 |
| session | 在一个会话范围内有效 |
|         |                      |

#### bean的生命周期

> 具体的生命周期过程

- bean对象创建（调用无参构造器）
- 给bean对象设置属性
- bean对象初始化之前操作（由bean的后置处理器负责）
- bean对象初始化（需在配置bean时指定初始化方法）
- bean对象初始化之后操作（由bean的后置处理器负责）
- bean对象就绪可以使用
- bean对象销毁（需在配置bean时指定销毁方法）
- IOC容器关闭

#### FactoryBean

>FactoryBean是Spring提供的一种整合第三方框架的常用机制。
>和普通的bean不同，配置一个 FactoryBean类型的bean，在获取bean的时候得到的并不是class属性中配置的这个类的对象，而是 getObject()方法的返回值。
>通过这种机制，Spring可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们
>将来我们整合Mybatis时，Spring就是通过FactoryBean机制来帮我们创建SqlSessionFactory对象的

示例: 
创建类UserFactoryBean
```java
import org.springframework.beans.factory.FactoryBean;  
  
public class UserFactoryBean implements FactoryBean<User> {  
    @Override  
    public User getObject() throws Exception {  
        return new User();  
    }  
  
    @Override  
    public Class<?> getObjectType() {  
        return User.class;  
    }  
}
```

#### 基于xml的自动装配

> 根据指定的策略,在IOC容器中匹配某一个bean,自动为指定的bean中所依赖的类类型或者接口类型属性赋值

配置bean

>使用bean标签的autowire属性设置自动装配效果 
>自动装配方式：byType 
>byType：根据类型匹配IOC容器中的某个兼容类型的bean，为属性自动赋值 
>若在IOC中，没有任何一个兼容类型的bean能够为属性赋值，则该属性不装配，即值为默认值 null 
>若在IOC中，有多个兼容类型的bean能够为属性赋值，则抛出异常 NoUniqueBeanDefinitionException
>自动装配方式：byName 
>byName：将自动装配的属性的属性名，作为bean的id在IOC容器中匹配相对应的bean进行赋值

```xml
<bean id="userController"
	class="com.atguigu.autowire.xml.controller.UserController" autowire="byType">
</bean>
<bean id="userService"
	class="com.atguigu.autowire.xml.service.impl.UserServiceImpl" autowire="byName">
</bean>
<bean id="userDao" class="com.atguigu.autowire.xml.dao.impl.UserDaoImpl"></bean>
```

### 2.3 基于注解管理bean

#### 标识组件的常用注解
> @Component：将类标识为普通组件 
> @Controller：将类标识为控制层组件 
> @Service：将类标 识为业务层组件 
> @Repository：将类标识为持久层组件

@Controller、@Service、@Repository这三个注解只是在@Component注解 的基础上起了三个新的名字

#### 扫描组件

1. 基本方式

```xml
<context:component-scan base-package="pers.quan.ssm"/>
```

2. 指定要排除的组件

```xml
<context:component-scan base-package="pers.quan.ssm">  
	<!-- context:exclude-filter标签：指定排除规则 -->
	<!--
		type：设置排除或包含的依据
		type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
		type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
	-->
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>  
    <context:exclude-filter type="assignable" expression="pers.quan.ssm.controller.controller"/>  
</context:component-scan>
```

3. 仅扫描指定组件

```xml
<context:component-scan base-package="pers.quan.ssm" use-default-filters="false">  
	<!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
	<!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
	<!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
	<!--
		type：设置排除或包含的依据
		type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
		type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
	-->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>  
</context:component-scan>
```

> 组件所对应的bean的id:
> 默认情况下类名首字母小写就是bean的id
> 例如：UserController类对应的bean的id就是userController
> 自定义bean的id: 
> 可通过标识组件的注解的value属性设置自定义的bean的id @Service("userService")
> //默认为userServiceImpl    public class UserServiceImpl implements UserService {}

#### 基于注解的自动装配

@Autowired注解
- 在成员变量上直接标记@Autowired注解即可完成自动装配，不需要提供setXxx()方法
- @Autowired注解可以标记在构造器和set方法上
- 根据所需要的组件类型到IOC容器中查找,如果找到的和所需类型匹配的bean不止一个
	- 没有@Qualifier注解：根据@Autowired标记位置成员变量的变量名作为bean的id进行 匹配
	- 使用@Qualifier注解：根据@Qualifier注解中指定的名称作为bean的id进行匹配
- @Autowired中有属性required，默认值为true，因此在自动装配无法找到相应的bean时，会装 配失败
- 可以将属性required的值设置为true，则表示能装就装，装不上就不装，此时自动装配的属性为 默认值

```java
@Autowired 
@Qualifier("userServiceImpl") 
private UserService userService;
```

## 3. AOP

> 概述:
> AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面 向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况 下给程序动态统一添加额外功能的一种技术

### AOP的相关术语

- 横切关注点
> 从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强

- 通知
> 每一个横切关注点上要做的事情都需要写一个方法来实现,这样的方法就叫通知方法,通知又分为:
> 1. 前置通知: 在被代理的目标方法前执行
> 2. 返回通知：在被代理的目标方法成功结束后执行
> 3. 异常通知：在被代理的目标方法异常结束后执行
> 4. 后置通知：在被代理的目标方法最终结束后执行
> 5. 环绕通知：使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置

- 切面
>封装通知方法的类

- 目标
>被代理的目标对象

- 代理
>向目标对象应用通知之后创建的代理对象

- 连接点
![[Attachments/Images/Pasted image 20230226214824.png|600]]
- 切入点
>定位连接点的方式
>每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说） 
>如果把连接点看作数据库中的记录，那么切入点就是查询记录的 SQL 语句 
>Spring 的 AOP 技术可以通过切入点定位到特定的连接点
>切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件

### 基于注解的AOP

#### 基本配置

1. 导入依赖

```xml
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-context</artifactId>  
    <version>6.0.3</version>  
</dependency>  
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-aspects</artifactId>  
    <version>6.0.5</version>  
</dependency>
```

2. 准备被代理的目标资源

3. 创建切面类

```java
    @Aspect  // @Aspect表示这个类是一个切面类
    @Component  // @Component注解保证这个切面类能够放入IOC容器 
    public class LogAspect {  
        @Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.*  
        (..))")  
  
        public void beforeMethod(JoinPoint joinPoint) {  
            String methodName = joinPoint.getSignature().getName();  
            String args = Arrays.toString(joinPoint.getArgs());  
            System.out.println("Logger-->前置通知，方法名：" + methodName + "，参  
                    数："+args);  
        }  
  
        @After("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")  
        public void afterMethod(JoinPoint joinPoint) {  
            String methodName = joinPoint.getSignature().getName();  
            System.out.println("Logger-->后置通知，方法名：" + methodName);  
        }  
  
        @AfterReturning(value = "execution(*  
                com.atguigu.aop.annotation.CalculatorImpl.*(..))", returning = " result")  
  
        public void afterReturningMethod(JoinPoint joinPoint, Object result) {  
            String methodName = joinPoint.getSignature().getName();  
            System.out.println("Logger-->返回通知，方法名：" + methodName + "，结果："+result);  
        }  
  
        @AfterThrowing(value = "execution(*  
                com.atguigu.aop.annotation.CalculatorImpl.*(..))", throwing = " ex")  
  
        public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex) {  
            String methodName = joinPoint.getSignature().getName();  
            System.out.println("Logger-->异常通知，方法名：" + methodName + "，异常：" + ex);  
        }  
  
        @Around("extionecu(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")  
        public Object aroundMethod(ProceedingJoinPoint joinPoint) {  
            String methodName = joinPoint.getSignature().getName();  
            String args = Arrays.toString(joinPoint.getArgs());  
            Object result = null;  
            try {  
                System.out.println("环绕通知-->目标对象方法执行之前");  
				//目标对象（连接点）方法的执行  
                result = joinPoint.proceed();  
                System.out.println("环绕通知-->目标对象方法返回值之后");  
            } catch (Throwable throwable) {  
                throwable.printStackTrace();  
                System.out.println("环绕通知-->目标对象方法出现异常时");  
            } finally {  
                System.out.println("环绕通知-->目标对象方法执行完毕");  
            }  
            return result;  
        }  
    }
```

4. 在Spring的配置文件中配置

```xml
<!--  
基于注解的AOP的实现：  
	1、将目标对象和切面交给IOC容器管理（注解+扫描）  
	2、开启AspectJ的自动代理，为目标对象自动生成代理  
	3、将切面类通过注解@Aspect标识  
-->  
<context:component-scan base-package="pers.quan.ssm"/>  
<aop:aspectj-autoproxy/>
```

#### 通知类型的注解

- 前置通知：使用@Before注解标识，在被代理的目标方法前执行
- 返回通知：使用@AfterReturning注解标识，在被代理的目标方法成功结束后执行
- 异常通知：使用@AfterThrowing注解标识，在被代理的目标方法异常结束后执行
- 后置通知：使用@After注解标识，在被代理的目标方法最终结束后执行
- 环绕通知：使用@Around注解标识，使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置

>通知的执行顺序:
>1. 前置通知
>2. 目标通知
>3. 返回通知或异常通知
>4. 后置通知

#### 切入点表达式

>语法细节:
>1. 用\*号代替“权限修饰符”和“返回值”部分表示“权限修饰符”和“返回值”不限
>2. 在包名的部分，一个"\*"号只能代表包的层次结构中的一层，表示这一层是任意的,例如:  \*.Hello匹配com.Hello,不匹配pers.quan.Hello
>3. 在包名的部分，使用“\*..”表示包名任意、包的层次深度任意
>4. 在类名的部分，类名部分整体用\*号代替，表示类名任意
>5. 在类名的部分，可以使用\*号代替类名的一部分, 例如: \*Service匹配所有名称以Service结尾的类或接口
>6. 类名同方法名
>7. 在方法参数列表部分，使用(..)表示参数列表任意
>8. 在方法参数列表部分，使用(xxx,..)表示参数列表以一个xxx类型的参数开头
>9. 在方法返回值部分，如果想要明确指定一个返回值类型，那么必须同时写明权限修饰符,
>	例如:execution(public int ..Service.\*(.., int))

![[Attachments/Images/Pasted image 20230226225218.png]]
#### 重用切入点表达式

1. 声明

```java
@Pointcut("execution(* com.atguigu.aop.annotation.*.*(..))") 
public void pointCut(){
	
}
```

2. 在同一个切面中使用

```java
@Before("pointCut()") 
public void beforeMethod(JoinPoint joinPoint){ 
	String methodName = joinPoint.getSignature().getName(); 
	String args = Arrays.toString(joinPoint.getArgs()); 
	System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); 
}
```

3. 在不同切面中使用

```java
@Before("com.atguigu.aop.CommonPointCut.pointCut()") 
public void beforeMethod(JoinPoint joinPoint){ 
	String methodName = joinPoint.getSignature().getName();
	String args = Arrays.toString(joinPoint.getArgs());
	System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); }
```

#### 获取通知的相关信息

1. 获取连接点信息
>获取连接点信息可以在通知方法的参数位置设置JoinPoint类型的形参

```java
@Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.*(..))") 
public void beforeMethod(JoinPoint joinPoint){ 
	//获取连接点的签名信息 
	String methodName = joinPoint.getSignature().getName(); 
	//获取目标方法到的实参信息 
	String args = Arrays.toString(joinPoint.getArgs()); 
	System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
}
```

2. 获取目标方法的返回值
>AfterReturning中的属性returning,用来将通知方法的某个形参接受目标方法的返回值

```java
@AfterReturning(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", 
				returning = "result") 
public void afterReturningMethod(JoinPoint joinPoint, Object result){
	 String methodName = joinPoint.getSignature().getName(); 
	 System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result);
}
```

3. 获取目标方法的异常
>@AfterThrowing中的属性throwing，用来将通知方法的某个形参，接收目标方法的异常

```java
@AfterThrowing(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", throwing = "ex") public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){ 
	String methodName = joinPoint.getSignature().getName(); 
	System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex); 
}
```

#### 环绕通知

>由于环绕通知环绕在切入点,所以跟其他通知不同的是我们需要体现出切入点的执行

```java
@Around("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")  
public Object aroundMethod(ProceedingJoinPoint joinPoint){  
	String methodName=joinPoint.getSignature().getName();  
	String args=Arrays.toString(joinPoint.getArgs());  
	Object result=null;  
	try{  
		System.out.println("环绕通知-->目标对象方法执行之前");  
		//目标方法的执行，目标方法的返回值一定要返回给外界调用者  
		result=joinPoint.proceed();  
		System.out.println("环绕通知-->目标对象方法返回值之后");  
	}catch(Throwable throwable){  
		throwable.printStackTrace();  
		System.out.println("环绕通知-->目标对象方法出现异常时");  
	}finally{  
		System.out.println("环绕通知-->目标对象方法执行完毕");  
	}  
	return result;  
}
```

#### 切面的优先级

> 相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序
> - 优先级高的切面：外面
> - 优先级低的切面：里面
> 使用@Order注解可以控制切面的优先级:
> - @Order(较小的数)：优先级高
> - @Order(较大的数)：优先级低

![[Attachments/Images/Pasted image 20230226231702.png|500]]

### 基于XML的AOP

```xml
<context:component-scan base-package="com.atguigu.aop.xml"></context:component-scan>
<aop:config>
	<!--配置切面类-->
	<aop:aspect ref="loggerAspect">
		<aop:pointcut id="pointCut" expression="execution(*	com.atguigu.aop.xml.CalculatorImpl.*(..))"/>
		<aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before>
		<aop:after method="afterMethod" pointcut-ref="pointCut"></aop:after>
		<aop:after-returning method="afterReturningMethod" returning="result"
		pointcut-ref="pointCut"></aop:after-returning>
		<aop:after-throwing method="afterThrowingMethod" throwing="ex" pointcutref="pointCut"></aop:after-throwing>
		<aop:around method="aroundMethod" pointcut-ref="pointCut"></aop:around>
	</aop:aspect>
	<aop:aspect ref="validateAspect" order="1">
	<aop:before method="validateBeforeMethod" pointcut-ref="pointCut"></aop:before>
	</aop:aspect>
</aop:config>

```


## 4. 声明式事务

> Spring框架对JDBC进行封装,使用JdbcTemplate方便实现对数据库操作

### JdbcTemplate基本使用

1. 加入依赖

```xml
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-context</artifactId>  
    <version>6.0.3</version>  
</dependency>  
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-aspects</artifactId>  
    <version>6.0.5</version>  
</dependency>  
<!-- Spring 持久化层支持jar包 -->  
<!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个jar包 -->  
<!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->  
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-orm</artifactId>  
    <version>6.0.4</version>  
</dependency>  
<!--测试相关-->  
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-test</artifactId>  
    <version>6.0.5</version>  
    <scope>test</scope>  
</dependency>
<dependency>  
    <groupId>junit</groupId>  
    <artifactId>junit</artifactId>  
    <version>4.13.2</version>  
</dependency>  
<dependency>  
    <groupId>mysql</groupId>  
    <artifactId>mysql-connector-java</artifactId>  
    <version>8.0.32</version>  
</dependency>  
<dependency>  
    <groupId>com.alibaba</groupId>  
    <artifactId>druid</artifactId>  
    <version>1.2.16</version>  
</dependency>
```

2. 创建jdbc.properties

3. 配置Spring的配置文件

```xml
<!-- 导入外部属性文件 -->
<context:property-placeholder location="jdbc.properties"/>  
<!-- 配置数据源 -->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">  
    <property name="url" value="${jdbc.url}"/>  
    <property name="driverClassName" value="${jdbc.driver"/>  
    <property name="username" value="${jdbc.username"/>  
    <property name="password"  value="${jdbc.password"/>  
</bean>  
<!-- 配置 JdbcTemplate -->
<bean id = "jdbcTemplate" class ="org.springframework.jdbc.core.JdbcTemplate"> 
	<!-- 装配数据源 -->
    <property name="dataSource" ref="druidDataSource"/>  
</bean>
```

4. 测试

```java
@RunWith(SpringJUnit4ClassRunner.class)  
@ContextConfiguration("classpath:applicationContext.xml")  
public class JDBCTemplateTest {  
  
    @Autowired  
    public JdbcTemplate jdbcTemplate;  
  
    @Test  
    public void testUpdate() {  
        String sql = "insert into user(name,age,email) values(?,?,?)";  
        int result = jdbcTemplate.update(sql,"张三",19,"321@qq.com");  
        System.out.println(result);  
    }  
}
```

### 基于注解的声明式事务

>Maven依赖和Spring配置文件如上

1. 添加事务配置
>在Spring的配置文件中添加配置:
>注意tx:annotation-driven导入的空间名称为http://www.springframework.org/schema/tx

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  
    <property name="dataSource" ref="druidDataSource"/>  
</bean>
<!--
开启事务的注解驱动
通过注解@Transactional所标识的方法或标识的类中所有的方法，都会被事务管理器管理事务
-->
<!-- transaction-manager属性的默认值是transactionManager，如果事务管理器bean的id正好就
是这个默认值，则可以省略这个属性 -->
<tx:annotation-driven transaction-manager="transactionManager" />
```

2. 根据需要添加注解
	- @Transactional标识在方法上，只会影响该方法
	- @Transactional标识的类上，会影响类中所有的方法

### 事务的属性

1. readOnly

>对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这样数据库就能够针对查询操作来进行优化
>使用方式:
>@Transactional(readOnly=true)

2. timeout

>超时回滚，释放资源
>使用方式:
>@Transactional(timeout = 3)

3. 回滚

>声明式事务默认只针对运行时异常回滚，编译时异常不回滚
>
>可以通过@Transactional中相关属性设置回滚策略
>- rollbackFor属性：需要设置一个Class类型的对象
>- rollbackForClassName属性：需要设置一个字符串类型的全类名
>- noRollbackFor属性：需要设置一个Class类型的对象
>- noRollbackForClassName属性：需要设置一个字符串类型的全类名
>
>使用方式:
>@Transactional(noRollbackFor=ArithmeticException.Class)
>@Transactional(noRollbackForClassName = "java.lang.ArithmeticException")
>这样在碰到ArithmeticException异常的时候就不会回滚了

4. 事务隔离级别

>隔离级别一共有四种:
>- 读未提交：READ UNCOMMITTED  
>  允许Transaction01读取Transaction02未提交的修改
>- 读已提交：READ COMMITTED  
>  要求Transaction01只能读取Transaction02已提交的修改
>- 可重复读：REPEATABLE READ  
>  确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01执行期间禁止其它 事务对这个字段进行更新
>- 串行化：SERIALIZABLE  
>  确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执行期间，禁止其它 事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，但性能十分低下
>  

>使用方式
>@Transactional(isolation = Isolation.DEFAULT)//使用数据库默认的隔离级别
>@Transactional(isolation = Isolation.READ_UNCOMMITTED)//读未提交
>@Transactional(isolation = Isolation.READ_COMMITTED)//读已提交
>@Transactional(isolation = Isolation.REPEATABLE_READ)//可重复读 
>@Transactional(isolation = Isolation.SERIALIZABLE)//串行化

5. 事务传播行为

>当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行

>使用方法:
>
>@Transactional(propagation = Propagation.REQUIRED)，默认情况，表示如果当前线程上有已经开启的事务可用，那么就在这个事务中运行
>举例:购买图书的方法buyBook()在checkout()中被调用，checkout()上有事务注解，因此在此事务中执行。所购买的两本图书的价格为80和50，而用户的余 额为100，因此在购买第二本图书时余额不足失败，导致整个checkout()回滚，即只要有一本书买不 了，就都买不了
>
>@Transactional(propagation = Propagation.REQUIRES_NEW)，表示不管当前线程上是否有已经开启的事务，都要开启新事务
>举例:同样的场景，每次购买图书都是在buyBook()的事务中执行，因此第一本图 书购买成功，事务结束，第二本图书购买失败，只在第二次的buyBook()中回滚，购买第一本图书不受 影响，即能买几本就买几本

### 基于XML的声明式事务

```xml
<aop:config>
	<!-- 配置事务通知和切入点表达式 -->
	<aop:advisor advice-ref="txAdvice" pointcut="execution(*com.atguigu.spring.tx.xml.service.impl.*.*(..))"/>
</aop:config>
<!-- tx:advice标签：配置事务通知 -->
<!-- id属性：给事务通知标签设置唯一标识，便于引用 -->
<!-- transaction-manager属性：关联事务管理器 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<!-- tx:method标签：配置具体的事务方法 -->
		<!-- name属性：指定方法名，可以使用星号代表多个字符 -->
		<tx:method name="get*" read-only="true"/>
		<tx:method name="query*" read-only="true"/>
		<tx:method name="find*" read-only="true"/>
		<!-- read-only属性：设置只读属性 -->
		<!-- rollback-for属性：设置回滚的异常 -->
		<!-- no-rollback-for属性：设置不回滚的异常 -->
		<!-- isolation属性：设置事务的隔离级别 -->
		<!-- timeout属性：设置事务的超时属性 -->
		<!-- propagation属性：设置事务的传播行为 -->
		<tx:method name="save*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
		<tx:method name="update*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
		<tx:method name="delete*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
	</tx:attributes>
</tx:advice>
```

>基于xml实现的声明式事务,必须引入aspects的依赖

```xml
<dependency>  
	<groupId>org.springframework</groupId>  
	<artifactId>spring-aspects</artifactId>  
	<version>5.3.1</version>  
</dependency>
```



## 1. SpringMVC的基本配置

1. 添加web模块

2. 打包方式:war

3. 导入依赖
```xml
<dependencies>  
    <!-- SpringMVC -->  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-webmvc</artifactId>  
        <version>6.0.5</version>  
    </dependency>  
  
    <!-- 日志 -->  
    <dependency>  
        <groupId>ch.qos.logback</groupId>  
        <artifactId>logback-classic</artifactId>  
        <version>1.4.5</version>  
        <scope>test</scope>  
    </dependency>  
  
    <!-- ServletAPI -->  
    <dependency>  
        <groupId>javax.servlet</groupId>  
        <artifactId>javax.servlet-api</artifactId>  
        <version>3.1.0</version>  
        <scope>provided</scope>  
    </dependency>  
      
    <!-- Spring5和Thymeleaf整合包 -->  
    <dependency>  
        <groupId>org.thymeleaf</groupId>  
        <artifactId>thymeleaf-spring5</artifactId>  
        <version>3.1.1.RELEASE</version>  
    </dependency>  
</dependencies>
```

4. 配置web.xml
```xml
<servlet>  
    <servlet-name>SpringMVC</servlet-name>  
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <!--<init-param>  
	     contextConfigLocation为固定值
		<param-name>contextConfigLocation</param-name>       
		<param-value>classpath:SpringMVC.xml</param-value>   
    </init-param>-->    
    <!--
	作为框架的核心组件，在启动过程中有大量的初始化操作要做
	而这些操作放在第一次请求时才执行会严重影响访问速度
	因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
	-->
    <load-on-startup>1</load-on-startup>  
</servlet>  
<servlet-mapping>  
    <servlet-name>SpringMVC</servlet-name>  
    <!--
	设置springMVC的核心控制器所能处理的请求的请求路径
	/所匹配的请求可以是/login或.html或.js或.css方式的请求路径
	但是/不能匹配.jsp请求路径的请求
	/*可以匹配所有的请求路径包括.jsp
-->
    <url-pattern>/</url-pattern>  
</servlet-mapping>
```

>SpringMVC的配置文件默认位于WEB-INF下，默认名称为<\servlet-name>-servlet.xml,可以通过init-param来指定配置文件路径

5. 配置SpringMVC-servlet.xml
```xml
<!--开启组件扫描-->  
<context:component-scan base-package="pers.quan.SpringMVC"/>  
<!-- 配置Thymeleaf视图解析器 -->  
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
                    <property name="characterEncoding" value="UTF-8"/>  
                </bean>  
            </property>  
        </bean>  
    </property>  
</bean>  
<!--  
处理静态资源，例如html、js、css、jpg  
若只设置该标签，则只能访问静态资源，其他请求则无法访问  
-->  
<mvc:default-servlet-handler/>  
<!-- 开启mvc注解驱动 -->  
<mvc:annotation-driven>  
    <mvc:message-converters>  
        <!-- 处理响应中文内容乱码 -->  
        <bean  
                class="org.springframework.http.converter.StringHttpMessageConverter">  
            <property name="defaultCharset" value="UTF-8"/>  
            <property name="supportedMediaTypes">  
                <list>  
                    <value>text/html</value>  
                    <value>application/json</value>  
                </list>  
            </property>  
        </bean>  
    </mvc:message-converters>  
</mvc:annotation-driven>
```

6. 进行基本测试
```java
@Controller  
public class Hello {  
    @RequestMapping("/")  
    public String index() {  
        return "index";  
    }  
}
```

总结:
>浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器 DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器， 将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的 控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会 被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视 图所对应页面

## 2. @RequestMapping注解

### 2.1 使用方法
>@RequestMapping标识一个类：设置映射请求的请求路径的初始信息
>@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

举例:
```java
@Controller @RequestMapping("/test")
public class RequestMappingController { 
	@RequestMapping("/testRequestMapping") 
	//此时请求映射所映射的请求的请求路径为：/test/testRequestMapping  
	public String testRequestMapping(){ 
		return "success"; 
	} 
}
```

### 2.2 属性

1. **value**
- @RequestMapping注解的value属性通过请求的请求地址匹配请求映射
- @RequestMapping注解的value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址 所对应的请求
- @RequestMapping注解的value属性必须设置，至少通过请求地址匹配请求映射

2. **method**
- @RequestMapping注解的method属性通过请求的请求方式（get或post）匹配请求映射
- @RequestMapping注解的method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配 多种请求方式的请求
- 若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错 405：Request method 'POST' not supported
- 对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解
	- 处理get请求的映射-->@GetMapping 
	- 处理post请求的映射-->@PostMapping
	- 处理put请求的映射-->@PutMapping 
	- 处理delete请求的映射-->@DeleteMapping
- 常用的请求方式有get，post，put，delete 但是目前浏览器只支持get和post, 若在form表单提交时, 为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理.若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter

3. **params**
- @RequestMapping注解的params属性通过请求的请求参数匹配请求映射
- @RequestMapping注解的params属性是一个字符串类型的数组，可以通过四种表达式设置请求参数 和请求映射的匹配关系
	- "param"：要求请求映射所匹配的请求必须携带param请求参数
	- "!param"：要求请求映射所匹配的请求必须不能携带param请求参数
	- "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value
	- "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value

4. **headers**
- @RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射
- @RequestMapping注解的headers属性是一个字符串类型的数组，可以通过四种表达式设置请求头信 息和请求映射的匹配关系
	- "header"：要求请求映射所匹配的请求必须携带header请求头信息
	- "!header"：要求请求映射所匹配的请求必须不能携带header请求头信息
	- "header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value
	- "header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

## 3. SpringMVC获取请求参数

### 3.1 通过ServletAPI获取

>将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请求的请求报文的对象

```java
@RequestMapping("/servletapi")  
public String testParam(HttpServletRequest request) {  
    String username = request.getParameter("username");  
    String password = request.getParameter("password");  
    System.out.println("username = " + username);  
    System.out.println("password = " + password);  
    return "success";  
}
```

### 3.2 通过控制器方法的形参获取请求参数

- 在控制器方法的形参位置，设置和请求参数同名的形参，当浏览器发送请求，匹配到请求映射时，在 DispatcherServlet中就会将请求参数赋值给相应的形参
- 若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置字符串 数组或者字符串类型的形参接收此请求参数
	- 若使用字符串数组类型的形参，此参数的数组中包含了每一个数据
	- 若使用字符串类型的形参，此参数的值为每个数据中间使用逗号拼接的结果

```java
//th:href="@{/testParam(username='admin',password=123456)}"
@RequestMapping("/testParam") 
public String testParam(String username, Stringpassword){
	System.out.println("username:"+username+",password:"+password); 
	return "success";
 }
```

### 3.3 @RequestParam

- @RequestParam是将请求参数和控制器方法的形参创建映射关系
- @RequestParam注解一共有三个属性
	- value：指定为形参赋值的请求参数的参数名
	- required：设置是否必须传输此请求参数，默认值为true
		- 若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置 defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为 false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为 null
	- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值 为""时，则使用默认值为形参赋值

### 3.4 @RequestHeader和@COOkieValue

- @RequestHeader是将请求头信息和控制器方法的形参创建映射关系
- @RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam
- @CookieValue是将cookie数据和控制器方法的形参创建映射关系
- @CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

### 3.5 通过POJO获取请求参数

>可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实 体类中的属性名一致，那么请求参数就会为此属性赋值

```html
<form th:action="@{/testpojo}" method="post">  
    用户名：<input type="text" name="username"><br>  
    密码：<input type="password" name="password"><br>  
    性别：<input type="radio" name="sex" value="男">男
    <input type="radio" name="sex" value="女">女<br>  
    年龄：<input type="text" name="age"><br>  
    邮箱：<input type="text" name="email"><br>  
    <input type="submit">  
</form>
```

```java
@RequestMapping("/testpojo") 
public String testPOJO(User user){ 
	System.out.println(user); 
	return "success"; 
} 
//最终结果-->User{id=null, username='张三', password='123', age=23, sex='男', email='123@qq.com'}
```

### 3.6 RESTful风格占位符获取请求参数

>SpringMVC路径中的占位符常用于[[SSM/SpringMVC#6. RESTful|RESTful]]风格中，当请求路径中将某些数据通过路径的方式传输到服 务器中，就可以在相应的@RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据，再通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参

```java
//th:href="@{/testRest/1/admin}"
@RequestMapping("/testRest/{id}/{username}") 
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){ 
	System.out.println("id:"+id+",username:"+username);
	return "success"
} 
//最终输出的内容为-->id:1,username:admin
```

### 3.7 请求参数的乱码问题

>解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器CharacterEncodingFilter，但是 必须在web.xml中进行注册
>SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效

```xml
<filter>  
    <filter-name>CharacterEncodingFilter</filter-name>  
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>  
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
```

## 4. 域对象共享数据

### 4.1 request

1. 使用ServletAPI向request域对象共享数据

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
	request.setAttribute("testScope", "hello,servletAPI"); 
    return "success"; 
}
```

2. 使用ModelAndView向request域对象共享数据

```java
@RequestMapping("/testModelAndView")  
public ModelAndView testModelAndView() {  
	/**  
	 * ModelAndView有Model和View的功能  
	 * Model主要用于向请求域共享数据  
	 * View主要用于设置视图，实现页面跳转  
	 */  
	ModelAndView mav = new ModelAndView();  
	//向请求域共享数据  
	mav.addObject("testScope", "hello,ModelAndView");  
	//设置视图，实现页面跳转  
	mav.setViewName("success");  
	return mav;  
}
```

3. 使用Model向request域对象共享数据

```java
@RequestMapping("/testModel") 
public String testModel(Model model){ 
	model.addAttribute("testScope", "hello,Model"); 
	return "success"; 
}
```

4. 使用map向request域对象共享数据

```java
@RequestMapping("/testMap") 
public String testMap(Map map){ 
	map.put("testScope", "hello,Map"); 
	return "success"; 
}
```

5. 使用ModelMap向request域对象共享数据

```java
@RequestMapping("/testModelMap") 
public String testModelMap(ModelMap modelMap){ 
	modelMap.addAttribute("testScope", "hello,ModelMap"); 
	return "success"; 
}
```

>Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap 类型的

### 4.2 session

```java
@RequestMapping("/testSession") 
public String testSession(HttpSession session){ 
	session.setAttribute("testSessionScope", "hello,session"); 
	return "success"; 
}
```

### 4.3 application

```java
@RequestMapping("/testApplication") 
public String testApplication(HttpSession session){ 
	ServletContext application = session.getServletContext(); application.setAttribute("testApplicationScope", "hello,application"); 
	return "success"; 
}
```

## 5. SpringMVC的视图

### 5.1 ThymeleafView

>当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转

### 5.2 转发视图

- SpringMVC中默认的转发视图是InternalResourceView
- SpringMVC中创建转发视图的情况：
	- 当控制器方法中所设置的视图名称以"forward:"为前缀时，创建InternalResourceView视图，此时的视 图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"forward:"去掉，剩余部 分作为最终路径通过转发的方式实现跳转

### 5.3 重定向视图

- SpringMVC中默认的重定向视图是RedirectView
- 当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建RedirectView视图，此时的视图名称不 会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最 终路径通过重定向的方式实现跳转

### 5.4 视图控制器view-controller

- 当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示
- 当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效,包括注解，此时需 要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：<mvc:annotation-driven>

```xml
<!--配置视图控制器-->
<mvc:view-controller path="/" view-name="index"/>

<!-- 开启mvc注解驱动 -->  
<mvc:annotation-driven>  
    <mvc:message-converters>  
        <!-- 处理响应中文内容乱码 -->  
        <bean  
                class="org.springframework.http.converter.StringHttpMessageConverter">  
            <property name="defaultCharset" value="UTF-8"/>  
            <property name="supportedMediaTypes">  
                <list>  
                    <value>text/html</value>  
                    <value>application/json</value>  
                </list>  
            </property>  
        </bean>  
    </mvc:message-converters>  
</mvc:annotation-driven>
```

## 6. RESTful

### 6.1 RESTful简介

- REST: Representational State Transfer ,表现层资源状态转移
- RESTful的实现
	- 具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE
	- 它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源
	- REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方 式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性
|   操作   |     传统方式     |        rest风格         |
|:--------:|:----------------:|:-----------------------:|
| 查询操作 | getUserById?id=1 |  user/1-->get请求方式   |
| 保存操作 |     saveUser     |   user-->post请求方式   |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 |    updateUser    |   user-->put请求方式    |

### 6.2 HiddenHttpMethodFilter

- SpringMVC 提供了 HiddenHttpMethodFilter 帮助我们将 POST 请求转换为 DELETE 或 PUT 请求
- HiddenHttpMethodFilter 处理put和delete请求的条件：
	- 当前请求的请求方式必须为post
	- 当前请求必须传输请求参数method
- 使用方法: 在web.xml中注册HiddenHttpMethodFilter

```xml
<filter>  
    <filter-name>HiddenHttpMethodFilter</filter-name>  
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>  
</filter>  
<filter-mapping>  
    <filter-name>HiddenHttpMethodFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
</filter-mapping>
```

## 7. SpringMVC处理ajax请求

### 7.1 @RequestBody

>@RequestBody可以获取请求体信息，使用@RequestBody注解标识控制器方法的形参，当前请求的请求体就会为当前注解所标识的形参赋值

```html
<!--此时必须使用post请求方式，因为get请求没有请求体-->  
<form th:action="@{/test/RequestBody}" method="post">  
    用户名：<input type="text" name="username"><br>  
    密码：<input type="password" name="password"><br>  
    <input type="submit">  
</form>
```

```java
@RequestMapping("/test/RequestBody")  
public String testRequestBody(@RequestBody String requestBody){  
    System.out.println("requestBody:"+requestBody);  
    return "success";  
}
```

输出结果:requestBody:username=admin&password=123456

### 7.2 @RequestBody获取json格式的请求参数

- 在使用了axios发送ajax请求之后，浏览器发送到服务器的请求参数有两种格式
	- name=value&name=value...，此时的请求参数可以通过request.getParameter()获取，对应 SpringMVC中，可以直接通过控制器方法的形参获取此类请求参数
	- {key:value,key:value,...}，此时无法通过request.getParameter()获取，在SpringMVC中，直接使用@RequestBody注解标识控制器方法的形参即可将此类请求参数转换为java对象

使用@RequestBody获取json格式的请求参数的条件:
1. 导入jackson的依赖
```xml
<dependency>  
    <groupId>com.fasterxml.jackson.core</groupId>  
    <artifactId>jackson-databind</artifactId>  
    <version>2.12.1</version>  
</dependency>
```
2. SpringMVC的配置文件中设置开启mvc的注解驱动
3. 在控制器方法的形参位置，设置json格式的请求参数要转换成的java类型（实体类或map）的参数，并使用@RequestBody注解标识

```java
//将json格式的数据转换为map集合  
@RequestMapping("/test/RequestBody/json")  
public void testRequestBody(@RequestBody Map<String, Object> map,  
HttpServletResponse response) throws IOException {  
	System.out.println(map);  
	//{username=admin, password=123456}  
	response.getWriter().print("hello,axios");  
}  

//将json格式的数据转换为实体类对象  
@RequestMapping("/test/RequestBody/json")  
public void testRequestBody(@RequestBody User user, HttpServletResponse  
response) throws IOException {  
	System.out.println(user);  
	//User{id=null, username='admin', password='123456', age=null,  gender = 'null' 
	response.getWriter().print("hello,axios");   
}  
}
```

### 7.3 RestController

>@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了 @Controller注解，并且为其中的每个方法添加了@ResponseBody注解

## 8. 文件上传和下载

### 8.1 文件下载

>ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文

```java
@RequestMapping("/testDown")  
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {  
	//获取ServletContext对象  
	ServletContext servletContext = session.getServletContext();  
	//获取服务器中文件的真实路径  
	String realPath = servletContext.getRealPath("/static/img/1.jpg");  
	//创建输入流  
	InputStream is = new FileInputStream(realPath);  
	//创建字节数组  
	byte[] bytes = new byte[is.available()];  
	//将流读到字节数组中  
	is.read(bytes);  
	//创建HttpHeaders对象设置响应头信息  
	MultiValueMap<String, String> headers = new HttpHeaders();  
	//设置要下载方式以及下载文件的名字  
	headers.add("Content-Disposition", "attachment;filename=1.jpg");  
	//设置响应状态码  
	HttpStatus statusCode = HttpStatus.OK;  
	//创建ResponseEntity对象  
	ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers,statusCode);  
	//关闭输入流  
	is.close();  
	return responseEntity;  
}
```

### 8.2 文件上传

>文件上传要求form表单的请求方式必须为post，并且添加属性enctype="multipart/form-data" SpringMVC中将上传的文件封装到MultipartFile对象中，通过此对象可以获取文件相关信息

1. 添加依赖
```xml
<dependency>  
    <groupId>commons-fileupload</groupId>  
    <artifactId>commons-fileupload</artifactId>  
    <version>1.3.1</version>  
</dependency>
```

2. 在SpringMVC的配置文件中添加配置：
```xml
<!--必须通过文件解析器的解析才能将文件转换为MultipartFile对象-->
<bean id="multipartResolver"
class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
</bean>
```

3. 控制器方法
```java
@RequestMapping("/testUp")  
public String testUp(MultipartFile photo, HttpSession session) throws  IOException {  
	//获取上传的文件的文件名  
	String fileName = photo.getOriginalFilename();  
	//处理文件重名问题  
	String hzName = fileName.substring(fileName.lastIndexOf("."));  
	fileName = UUID.randomUUID().toString() + hzName;  
	//获取服务器中photo目录的路径  
	ServletContext servletContext = session.getServletContext();  
	String photoPath = servletContext.getRealPath("photo");  
	File file = new File(photoPath);  
	if (!file.exists()) {  
		file.mkdir();  
	}  
	String finalPath = photoPath + File.separator + fileName;  
	//实现上传功能  
	photo.transferTo(new File(finalPath));  
	return "success";  
}
```

## 9. 拦截器

### 9.1 拦截器的配置

- SpringMVC的拦截器用于拦截控制器方法的执行
- SpringMVC中的拦截器需要实现HandlerInterceptor
- SpringMVC的拦截器必须在SpringMVC的配置文件中进行配置

```xml
<mvc:interceptor>
	<bean class="com.atguigu.interceptor.FirstInterceptor"></bean>  
	<ref bean="firstInterceptor"></ref> 
</mvc:interceptor>
<!-- 以上两种配置方式都是对DispatcherServlet所处理的所有的请求进行拦截 -->  
<mvc:interceptor>  
    <mvc:mapping path="/**"/>  
    <mvc:exclude-mapping path="/testRequestEntity"/>  
    <ref bean="firstInterceptor"></ref>  
</mvc:interceptor>  
<!--  
以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过  
mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求  
-->
```

### 9.2 拦截器的三个抽象方法

- preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返 回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法
- postHandle：控制器方法执行之后执行postHandle()
- afterCompletion：处理完视图和模型数据，渲染视图完毕之后执行afterCompletion

## 10. 异常处理器

```java
//@ControllerAdvice将当前类标识为异常处理的组件 
@ControllerAdvice 
public class ExceptionController { 
	//@ExceptionHandler用于设置所标识方法处理的异常 
	@ExceptionHandler(ArithmeticException.class) //ex表示当前请求处理中出现的异常对象 
	public String handleArithmeticException(Exception ex, Model model){ 
		model.addAttribute("ex", ex); 
		return "error"; 
	} 
}
```

## 11. 注解配置SpringMVC

1. 配置web.xml

>在Servlet3.0环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类， 如果找到的话就用它来配置Servlet容器。 
>Spring提供了这个接口的实现，名为 SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配置的任务交给它们来完成。
>Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为 AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了 AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文

```java
public class WebInit extends  
        AbstractAnnotationConfigDispatcherServletInitializer {  
    //指定Spring的配置类   
    @Override  
    protected Class<?>[] getRootConfigClasses() {  
        return new Class[]{SpringConfig.class};  
    }  
  
    //指定SpringMVC的配置类
    @Override  
    protected Class<?>[] getServletConfigClasses() {  
        return new Class[]{WebConfig.class};  
    }  
  
	//指定DispatcherServlet的映射规则,即url-pattern
	@Override  
    protected String[] getServletMappings() {  
        return new String[]{"/"};  
    }  
  
    //添加过滤器
    @Override
    protected Filter[] getServletFilters() {  
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();  
        encodingFilter.setEncoding("UTF-8");  
        encodingFilter.setForceRequestEncoding(true);  
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new  
                HiddenHttpMethodFilter();  
        return new Filter[]{encodingFilter, hiddenHttpMethodFilter};  
    }  
}
```

2. 创建SpringConfig配置类,代替spring的配置文件

3. 创建WebConfig配置类，代替SpringMVC的配置文件

```java
@Configuration  
//扫描组件  
@ComponentScan("com.atguigu.mvc.controller")  
//开启MVC注解驱动  
@EnableWebMvc  
public class WebConfig implements WebMvcConfigurer {  

	//使用默认的servlet处理静态资源  
	@Override  
	public void configureDefaultServletHandling(
	DefaultServletHandlerConfigurer configurer) {  
		configurer.enable();  
	}  

	//配置文件上传解析器  
	@Bean  
	public CommonsMultipartResolver multipartResolver() {  
		return new CommonsMultipartResolver();  
	}  

	//配置拦截器  
	@Override  
	public void addInterceptors(InterceptorRegistry registry) {  
		FirstInterceptor firstInterceptor = new FirstInterceptor();  
		registry.addInterceptor(firstInterceptor).addPathPatterns("/**");  
	}  

	//配置视图控制  
	@Override 
	public void addViewControllers(ViewControllerRegistry registry) { 
		registry.addViewController("/").setViewName("index");
	}
	
	//配置生成模板解析器  
	@Bean  
	public ITemplateResolver templateResolver() {  
		WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();  
		// ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext的方法获得  
		ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());  
		templateResolver.setPrefix("/WEB-INF/templates/");  
		templateResolver.setSuffix(".html");  
		templateResolver.setCharacterEncoding("UTF-8");  
		templateResolver.setTemplateMode(TemplateMode.HTML);  
		return templateResolver;  
	}  

	//生成模板引擎并为模板引擎注入模板解析器  
	@Bean  
	public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {  
		SpringTemplateEngine templateEngine = new SpringTemplateEngine();  
		templateEngine.setTemplateResolver(templateResolver);  
		return templateEngine;  
	}  

	//生成视图解析器并未解析器注入模板引擎  
	@Bean  
	public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {  
		ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();  
		viewResolver.setCharacterEncoding("UTF-8");  
		viewResolver.setTemplateEngine(templateEngine);  
		return viewResolver;  
	}  
}
```



## 1. 搭建MyBatis

### 1.1 导入依赖 

> 在pom.xml中导入MyBatis依赖和MySQL驱动依赖,并且加上junit单元测试

```xml
<dependencies>  
    <dependency>  
        <groupId>org.mybatis</groupId>  
        <artifactId>mybatis</artifactId>  
        <version>3.5.11</version>  
    </dependency>  
    <dependency>  
        <groupId>junit</groupId>  
        <artifactId>junit</artifactId>  
        <version>4.13.2</version>  
    </dependency>  
    <dependency>  
        <groupId>mysql</groupId>  
        <artifactId>mysql-connector-java</artifactId>  
        <version>8.0.30</version>  
    </dependency>  
</dependencies>
```

### 1.2 创建Mapper接口

> MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类

![[Attachments/Images/Pasted image 20230113160937.png]]

### 1.3 创建对应的映射文件

>1、映射文件的命名规则： 表所对应的实体类的类名+Mapper.xml 
>例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml, 因此一个映射文件对应一个实体类，对应一张表的操作.  MyBatis映射文件用于编写SQL，访问以及操作表中的数据. MyBatis映射文件存放的位置是src/main/resources/mappers目录下 
>2、 MyBatis中可以面向接口操作数据，要保证两个一致：
> 	a>mapper接口的全类名和映射文件的命名空间（namespace）保持一致 
> 	b>mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致

![[Attachments/Images/Pasted image 20230113160749.png]]

### 1.4 配置mybatis-config.xml

![[Attachments/Images/Pasted image 20230113162318.png]]

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration  
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-config.dtd">  
<configuration>

    <!--引入properties文件-->  
    <properties resource="jdbc.properties"/>
    
	<!--配置环境-->
    <environments default="development">   
        <environment id="development">  
            <transactionManager type="JDBC"/>  
            <dataSource type="POOLED">  
                <property name="driver" value="${jdbc.driver}"/>  
                <property name="url" value="${jdbc.url}"/>  
                <property name="username" value="${jdbc.username}"/>  
                <property name="password" value="${jdbc.password}"/>  
            </dataSource>
        </environment> 
    </environments>
    
    <!--引入映射文件-->  
    <mappers>  
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>

```

### 1.5 测试

>SqlSession：代表Java程序和数据库之间的会话。（HttpSession是Java程序和浏览器之间的 会话） SqlSessionFactory：是“生产”SqlSession的“工厂”
> 工厂模式：如果创建某一个对象，使用的过程基本固定，那么我们就可以把创建这个对象的相关代码封装到一个“工厂类”中，以后都使用这个工厂类来“生产”我们需要的对象。

```java
package pers.quan.mybatis.test;  
  
import org.apache.ibatis.io.Resources;  
import org.apache.ibatis.session.SqlSession;  
import org.apache.ibatis.session.SqlSessionFactory;  
import org.apache.ibatis.session.SqlSessionFactoryBuilder;  
import org.junit.Test;  
import pers.quan.mybatis.mapper.UserMapper;  
  
  
import java.io.InputStream;  
  
public class MyBatisTest {  
  
    @Test  
    public void testInsert() throws Exception {  
        //读取核心配置文件  
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");  
        //创建SqlSessionFactoryBuilder对象  
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();  
        //创建SqlSessionFactory对象  
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);  
        //创建SqlSession对象,true参数设置自动提交commit
        SqlSession sqlSession = sqlSessionFactory.openSession(true);  
        //通过代理模式获取实现了UserMapper接口的实现类对象  
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);  
        //执行方法  
        int result = userMapper.insertUser();  
        System.out.printf("结果:" + result);  
        sqlSession.close();  
    }  
}
```

## 2. mybatis-config.xml详解

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration  
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-config.dtd">  
<configuration>  

    <!--
	MyBatis核心配置文件中，标签的顺序properties?,settings?,typeAliases?,
	typeHandlers?,objectFactory?,objectWrapperFactory?,
	reflectorFactory?,plugins?,environments?,databaseIdProvider?,mappers?   
     -->   
    
    <!--引入properties文件-->  
    <properties resource="jdbc.properties"/>
    
    <!--设置类型别名,将来在处理映射时可以使用-->
    <typeAliases>  
        <!--  
        typeAlias：设置某个类型的别名  
        属性：  
        type：设置需要设置别名的类型  
        alias：设置某个类型的别名，若不设置该属性，那么该类型拥有默认的别名，即类名且不区分大小写   
        -->  
        
        <!--<typeAlias type="pers.quan.mybatis.pojo.User"></typeAlias>--> 
          
        <!--以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写-->  
        <package name="pers.quan.mybatis.pojo"/>  
        
    </typeAliases>  
    
    <!--environments配置多个连接数据库的环境,default设置默认使用的环境的id-->
    <environments default="development">   
		<!--environment配置具体的环境-->
        <environment id="development">  
            <!--  
            transactionManager：设置事务管理方式  
            属性：  
            type="JDBC|MANAGED" 
	            JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事  
	            务的提交或回滚需要手动处理  
	            MANAGED：被管理，例如Spring 
            -->    
            <transactionManager type="JDBC"/>  
            <!--
            dataSource：配置数据源  
	            属性:
	            type：设置数据源的类型  
	            type="POOLED|UNPOOLED|JNDI"         
		            POOLED：表示使用数据库连接池缓存数据库连接  
		            UNPOOLED：表示不使用数据库连接池  
		            JNDI：表示使用上下文中的数据源  
            -->  
            <dataSource type="POOLED">  
                <!--设置连接数据库的驱动-->  
                <property name="driver" value="${jdbc.driver}"/>  
                <!--设置连接数据库的连接地址-->  
                <property name="url" value="${jdbc.url}"/>  
                <!--设置连接数据库的用户名-->  
                <property name="username" value="${jdbc.username}"/>  
                <!--设置连接数据库的密码-->  
                <property name="password" value="${jdbc.password}"/>  
            </dataSource>
        </environment> 
        
		<environment id="test">  
		    <transactionManager type="JDBC"/>  
		    <dataSource type="POOLED">  
		        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>  
		        <property name="url"  
		                  value="jdbc:mysql://localhost:3306/ssmserverTimezone=UTC"/>  
		        <property name="username" value="root"/>  
		        <property name="password" value="123456"/>  
		    </dataSource>  
		</environment>
		
    </environments>
    
    <!--引入映射文件-->  
    <mappers>  
	    
        <!--<mapper resource="mappers/UserMapper.xml"/>-->  
        
        <!--以包为单位引入映射文件  
        要求：  
        1、mapper接口所在的包要和映射文件所在的包一致  
        2、mapper接口要和映射文件的名字一致  
        -->
        <package name="pers.quan.mybatis.mapper"/>  
    </mappers>
```

## 3. MyBatis获取参数值

> MyBatis获取参数值的两种方式：\${ }和#{ }
>\${ }的本质就是字符串拼接，#{ }的本质就是占位符赋值
>${ }使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号；但是#{ }使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时， 可以自动添加单引号

### 3.1 单个字面量类型的参数

>若mapper接口中的方法参数为单个的字面量类型 此时可以使用\${ }和#{ }以任意的名称获取参数的值,注意\${ }要加单引号

### 3.2 多个字面量类型的参数

>若mapper接口中的方法参数为多个时 此时MyBatis会自动将这些参数放在一个map集合中，以arg0,arg1...为键，以参数为值同时也会以 param1,param2...为键，以参数为值；
>因此只需要通过\${ }和#{ }访问map集合的键就可以获取相对应的值，注意${ }需要手动加单引号

### 3.3 map集合类型的参数

>若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中 只需要通过\${ }和#{ }访问map集合的键就可以获取相对应的值，注意${ }需要手动加单引号

### 3.4 实体类类型的参数

>若mapper接口中的方法参数为实体类对象时 此时可以使用\${ }和#{ }，通过访问实体类对象中的属性名获取属性值，注意${ }需要手动加单引号

### 3.5 使用@Param标识参数

> 可以通过@Param注解标识mapper接口中的方法参数 , 此时会将这些参数放在map集合中，以@Param注解的value属性值为键，以参数为值同时也可以以 param1,param2...为键，以参数为值；只需要通过\${ }和#{ }访问map集合的键就可以获取相对应的值， 注意${ }需要手动加单引号

## 4. 查询的返回值类型

### 4.1 resultType与类型别名

>在select标签中添加resultType属性可以指定返回结果的全类名或者是[[SSM/MyBatis#2. mybatis-config.xml详解|mybatis-config.xml]]中设置的别名
>如果是返回值是自定义类,属性名与列的别名相匹配会被赋值,否则为java默认值
>Mybatis中设置了一些常用类的别名

|           _byte           |    byte    |
|:-------------------------:|:----------:|
|   _char     |    char    |
| _character |    char    |
|           _long           |    long    |
|          _short           |   short    |
|           _int            |    int     |
|         _integer          |    int     |
|          _double          |   double   |
|          _float           |   float    |
|         _boolean          |  boolean   |
|          string           |   String   |
|           byte            |    Byte    |
|    char    | Character  |
| character  | Character  |
|           long            |    Long    |
|           short           |   Short    |
|            int            |  Integer   |
|          integer          |  Integer   |
|          double           |   Double   |
|           float           |   Float    |
|          boolean          |  Boolean   |
|            map            |    Map     |
|          hashmap          |  HashMap   |
|           list            |    List    |
|         arraylist         | ArrayList  |

> 注意: 返回类型为map并且返回数据为多条时
> 可以设置接口的返回值为List<Map<String,Object>将每一条结果返回成Map类型,用List容器接收
> 还可以将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置map集合的键，值是每条数据所对应的 map集合,即大Map包含着小Map值

### 4.2 resultMap

#### 4.2.1 非引用类型属性的查询

> 若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

```xml
<!--  
    resultMap：设置自定义映射  
    属性：  
        id：表示自定义映射的唯一标识  
        type：查询的数据要映射的实体类的类型  
    子标签：  
        id：设置主键的映射关系  
        result：设置普通字段的映射关系  
        association：设置多对一的映射关系  
        collection：设置一对多的映射关系  
            属性：  
            property：设置映射关系中实体类中的属性名  
            column：设置映射关系中表中的字段名  
-->  
<resultMap id="testMap" type="User">  
	<id property="id" column="id"/>  
	<result property="name" column="name"/>  
	<result property="age" column="age"/>  
	<result property="email" column="email"/>  
</resultMap>  
  
<!--User getUserById(@Param("id") Integer id);-->  
<select id ="getUserById" resultMap = "testMap" >  
	select age,email,name,id from user where id = #{id};    
</select>
```

>若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性名符合Java的规则（使用驼峰） 此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系 
>1.  可以通过为字段起别名的方式，保证和实体类中的属性名保持一致 
>2. 可以在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰 例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为userName

#### 4.2.2 包含实体类属性的类的查询

>例: 查询员工信息及其所在的部门信息

##### 4.2.1 级联

> 级联就是将查询出来的结果用**引用类.属性**的方式对应

```xml
<!-- Emp getEmpAndDepByEmpId(@Param("id") Integer id);-->  
 <resultMap id="testMap" type="Emp">  
     <id property="empId" column="emp_id"/>  
     <result property="empName" column="emp_name"/>  
     <result property="dep.depId" column="dep_id"/>  
     <result property="dep.depName" column="dep_name"/>  
 </resultMap>  
   
<select id="getEmpAndDepByEmpId" resultMap="testMap">  
    select emp.*, dep.* from emp  
	    left join dep on emp.dep_id = dep.dep_id
	where emp_id = #{id};
</select>
```

##### 4.2.2 association

> association相当于在resultMap中进行一个嵌套

```xml
<resultMap id="testMap" type="Emp">  
    <id property="empId" column="emp_id"/>  
    <result property="empName" column="emp_name"/>  
    <association property="dep" javaType="Dep">  
        <id property="depId" column="dep_id"/>  
        <result property="depName" column="dep_name"/>  
    </association>  
</resultMap>  
<select id="getEmpAndDepByEmpId" resultMap="testMap">  
    select emp.*, dep.* from emp 
		left join dep on emp.dep_id = dep.dep_id   
	where emp_id = #{id};
</select>
```

##### 4.2.3 分步查询

```xml
<resultMap id="testMap" type="Emp">  
    <id property="empId" column="emp_id"/>  
    <result property="empName" column="emp_name"/> 
			    <!--
			    select：设置分步查询，查询某个属性的值的sql的标识（namespace.sqlId）
				column：将sql以及查询结果中的某个字段设置为分步查询的条件 
			    -->
    <association property="dep"  
                 select="pers.quan.mybatis.mapper.DepMapper.getDepByDepId"  
                 column="emp_id"/>  
</resultMap>  
<!--Emp getEmpByEmpId(@Param("id") Integer id);-->  
<select id="getEmpByEmpId" resultMap="testMap">  
    select * from emp 
    where emp_id = #{id};
</select>
```

> pers.quan.mybatis.mapper.DepMapper.getDepByDepId中的配置
```xml
<!--     Dep getDepByDepId(@Param("id") Integer id);-->  
<select id="getDepByDepId" resultType="Dep">  
	select * from dep
	where dep_id = #{id};    
</select>
```

>分步查询的优点：
>
>可以实现延迟加载 但是必须在核心配置文件中设置全局配置信息
>lazyLoadingEnabled：延迟加载的全局开关, 当开启时，所有关联对象都会延迟加载 aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载
> 此时就可以实现按需加载，获取的数据是什么，就只会执行相应的sql。此时可通过association和 collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy(延迟加载)|eager(立即加载)"


#### 4.2.3 集合类型

> 根据Id查询部门中的所有员工

```xml
<resultMap id="testMap" type="Dep">  
    <id property="depId" column="dep_id"/>  
    <result property="depName" column="dep_name"/> 
    <!-- ofType中写的是集合中元素的类型-->
    <collection property="empList" ofType="Emp">  
        <id property="empId" column="emp_id"/>  
        <result property="empName" column="emp_name"/>  
    </collection>  
</resultMap>  
  
<select id="getDepByDepId" resultMap="testMap">  
    select emp.*, dep.* from dep 
	    left join emp  on dep.dep_id = emp.dep_id 
	where dep.dep_id = #{id};
</select>
```

## 5. 动态SQL

### 5.1 if

> if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行

```xml
<select id="getUserConditional" resultType ="User">  
	<!--where后面加上1=1保证and有效-->
    select * from user where 1=1   
    <!--语句前面加上and-->
    <if test="id!='' and id!=null"> 
        and id =#{id}
	</if>  
    <if test="name!='' and name!=null">  
        and name = #{name}    
	</if>  
</select>
```

### 5.2 where

> where和if一般结合使用：
> 1. 若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字 
> 2. 若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的and去掉
> 注意：where标签不能去掉条件最后多余的and

```xml
<select id="getUserConditional">  
	select * from user   
	<where>  
        <if test="id!='' and id!=null">  
            and id =#{id}       
		</if>  
        <if test="name!='' and name!=null">  
            and name = #{name}       
		</if>  
    </where>  
</select>
```

### 5.3 trim

> trim用于去掉或添加标签中的内容 
> 常用属性： 
> prefix：在trim标签中的内容的前面添加某些内容 
> prefixOverrides：在trim标签中的内容的前面去掉某些内容
> suffix：在trim标签中的内容的后面添加某些内容 
> suffixOverrides：在trim标签中的内容的后面去掉某些内容

```xml
<select id="getUserConditional">  
    select * from user
    <!--加上where,去掉后面的and-->    
    <trim prefix="where" suffixOverrides="and">  
        <if test="id!='' and id!=null">  
            id =#{id} and      
		</if>  
        <if test="name!='' and name!=null">  
            name = #{name} and    
	    </if>  
    </trim>  
</select>
```

### 5.4 choose、when、otherwise

> choose、when、 otherwise相当于if...else if..else

```xml
<select id="getUserConditional" resultType="User">  
	select * from user  
	<choose>  
		<when test="id!='' and id!=null">  
            id = #{id}      
	    </when>  
        <when test="name='' and name!=null">  
            name = #{name}      
	    </when>  
        <otherwise>  
            email = #{email}     
	    </otherwise>  
    </choose>  
</select>
```

### 5.5 foreach

> foreach可以用于处理集合类型的参数

```xml
<!--int insertMoreEmp(List<Emp> emps);-->  
<insert id="insertMoreEmp">  
    insert into emp values
	<!--collection指定集合,item指定集合中的每一项名字,separator指定每次循环之间的分隔符-->
	<foreach collection="emps" item="emp" separator=",">  
		(#{emp.empId},#{emp.empName},#{emp.dep.depId})   
	</foreach>  
</insert>  

<!--int deleteMoreByArray(int[] eids);-->  
<delete id="deleteMoreByArray">  
    delete from emp where   
    <foreach collection="eids" item="eid" separator="or">  
        emp_id = #{eid}   
	</foreach>  
</delete>  

<!--int deleteMoreByArray(int[] eids);-->  
<delete id="deleteMoreByArray">  
    delete from emp where emp_id in
    <!--open设置循环开始时拼接的符号,close设置结束时拼接的符号-->
    <foreach collection="eids" item="eid" separator="," open="(" close=")">  
        #{eid}   
	</foreach>  
</delete>
```

### 5.6 SQL片段

> sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入

```xml
<sql id="empColumns">  
    emp_id,emp_name  
</sql>  
<select id="selectTest" resultType="Emp">  
	select   
    <include refid="empColumns"/>  
    from emp   
    where emp_id = #{id}
</select>
```

## 6. 缓存

### 6.1 一级缓存

>一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问
>
>使一级缓存失效的四种情况： 
>1. 不同的SqlSession对应不同的一级缓存 
>2. 同一个SqlSession但是查询条件不同 
>3. 同一个SqlSession两次查询期间执行了任何一次增删改操作 
>4. 同一个SqlSession两次查询期间手动清空了缓存

### 6.2 二级缓存

> 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存,  此后若再次执行相同的查询语句，结果就会从缓存中获取
> 
> 二级缓存开启的条件：
>  1. 在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置
>  2. 在映射文件中设置标签
>  3. 二级缓存必须在SqlSession关闭或提交之后有效
>  4. 查询的数据所转换的实体类类型必须实现序列化的接口
>
> 使二级缓存失效的情况： 
> 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效
> 
> 二级缓存相关配置:
> 
> 在mapper配置文件中添加的cache标签可以设置一些属性: 
>  1. eviction属性：缓存回收策略，默认的是 LRU
> 	 1. LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。
> 	 2. FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。 
> 	 3. SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。 
> 	 4. WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象
	 2. flushInterval属性：刷新间隔，单位毫秒 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新
	 3. size属性：引用数目，正整数 代表缓存最多可以存储多少个对象，太大容易导致内存溢出 
	 4. readOnly属性：只读， true/false true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了 很重要的性能优势。 false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是 false

>先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。 
>如果二级缓存没有命中，再查询一级缓存 如果一级缓存也没有命中，则查询数据库 SqlSession关闭之后
>一级缓存中的数据会写入二级缓存

## 7. 分页插件

### 7.1 分页插件的配置

1. 添加依赖
```xml
<dependency>  
    <groupId>com.github.pagehelper</groupId>  
    <artifactId>pagehelper</artifactId>  
    <version>5.3.2</version>  
</dependency>
```

2. 配置分页插件
```xml
<plugins>  
    <!--设置分页插件-->  
    <plugin interceptor="com.github.pagehelper.PageInterceptor"/>  
</plugins>
```

### 7.2 分页插件的使用

1. 在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能
> pageNum：当前页的页码
> pageSize：每页显示的条数
2. 在查询获取list集合之后,使用PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)获取分页相关数据
>list：分页之后的数据 
>navigatePages：导航分页的页码数

### 7.3 Page

```java
private int pageNum;	//当前页码
private int pageSize; 	//每页数据的数量
private int startRow;	//始页首行行号
private int endRow;		//尾页尾行行号
private long total;		//总记录数
private int pages;		//总页数
private Boolean reasonable; //分页合理化
private Boolean pageSizeZero; //当设置为true的时候，如果pagesize设置为0（或RowBounds的limit=0），就不执行分页，返回全部结果
```

### 7.4 PageInfo

```java
    private int pageNum;   			//当前页
    private int pageSize;			//每页显示数据条数
    private int size;				//当前页的数量
    private int startRow; 			//始页首行行号
    private int endRow;				//尾页尾行行号
    private long total;				//总记录数
    private int pages;				//总页数
    private List<T> list;			//查询结果的数据
    private int firstPage;			//首页
    private int prePage;			//上一页
    private int nextPage;			// 下一页
    private int lastPage;			//最后一页
    private boolean isFirstPage;	//是不是第一页
    private boolean isLastPage;		//是不是最后一页
    private boolean hasPreviousPage;//有没有上一页
    private boolean hasNextPage;	//有没有下一页
    private int navigatePages;		//所有导航页号
    private int[] navigatepageNums;	//导航页码数

```
