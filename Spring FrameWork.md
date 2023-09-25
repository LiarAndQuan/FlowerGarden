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

