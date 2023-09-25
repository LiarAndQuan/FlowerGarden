
# 1. 配置文件

>Spring Boot 同时支持 properties 和 yml 格式的配置文件。 配置文件名称默认是 application。我们可以使用 application.properties , application.yml
>如果 properties 和 yml 都存在。properties 文件优先

## yaml

>yaml（YAML Ain't Markup Language)也看做是 yml，是一种做配置文件的数据格式，基本的语法 key:\[空格]值
>yml 文件文件扩展名是 yaml 或 yml（常用)

yaml支持三种数据结构 : 
- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list)
- 标量（scalars）：单个的、不可再分的值，例如数字、字符串、true|false 等

```yml
#Integer age
age: 18

#List<String> names
names:
  - lisi
  - zhangsan

#List<MyServer> servers
servers:
  - title: 华北服务器
    ip: 202.12.39.1
  - title: 西南服务器
    ip: 106.90.23.229
  - title: 南方服务器
    ip: 100.21.56.23
    
#Map<String,User> users
users:
  user1:
    name: 张三
    sex: 男
    age: 22
  user2:
    name: 李四
    sex: 男
    age: 26
```

## 读取配置文件的值

### @Value

>读取配置文件的 key 值，注入到 Bean 的类的属性可用@Value读取单个值，语法${key:默认值}

```java
@Component
public class Person {
    @Value("${name}")
    public String name;
    @Value("${age:18}")
    public Integer age;
}
```

### Environment

>Environment 是外部化的抽象，是多种数据来源的集合。从中可以读取 application 配置文件，环境变量，系统属 性。使用方式在 Bean 中注入 Environment。调用它的 getProperty(key)方法

```java
@Autowired
Environment environment;

@Test
void test2() {
	//获取属性值,返回值默认为String
	String name = environment.getProperty("name");
	//key是否存在
	if (environment.containsProperty("owner")) {
		System.out.println("owner存在");
	} else {
		System.out.println("owner不存在");
	}
	//获取返回值为Integer的属性,默认值为30
	Integer age = environment.getProperty("age", Integer.class, 30);
	System.out.println("name = " + name);
	System.out.println("age = " + age);
}
```

## 多文件配置

>如果都将配置集中到一个 application 文件，导致文件内容多，不易于阅读。我们将每个框架独立一个配置文件，最后将多个文件集中到 application。 我们使用导入文件的功能。

```yml
spring:
	config:
		import: xxx.yml,xxx.yml
```

## 多环境配置

在不同的环境下,可能需要不同的配置 , Springboot提供了多环境配置

>Spring Boot 规定环境文件的名称 application-{profile}.properties(yml)
>其中 profile 为自定义的环境名称，推 荐使用 dev 表示开发 ，test 表示测试 , prod 表示生产，feature 表示特性 ,  profile 名称是自定义的
>Spring Boot 会加载 application 以及 application-{profile}两类文件，不是只单独加载 application-{profile}

```yml
#application-test.yml是测试时的环境配置
spring:
	config:
		activate:
			#当前文件起作用时的环境名称
			on-profile: test

#在application.yml中导入配置文件并且激活
spring:
	config:
		import: application-test.yml
	profiles:
		active: test
```

## @ConfigurationProperties

>@Value 绑定单个属性，当属性较多时不方便，Spring Boot 提供了另一种属性的方法。将多个配置项绑定到 Bean 的属性，提供强类型的 Bean。Bean 能够访问到配置数据

基本准则 : 标准的 Java Bean 有无参数构造方法，包含属性的访问器。配合@ConfigurationProperties 注解一 起使用。Bean 的 static 属性不支持

```yml
app.name= lisi
app.age= 18
```

```java
@Component
@ConfigurationProperties(prefix = "app")//与前缀为app的配置文件中的属性绑定
public class Person {  
	//与配置文件中的app.name绑定
	public String name;  
	public Integer age;
}
```

>@ConfigurationProperties 声明在类上，表示绑定属性到此类。prefix 表示前缀，是配置文件中多个 key 的公共前 缀。这些 key 以“.”作为分隔符。例如 app.name, app: name 等。 prefix=”app”, 将文件中 app 开始的 key 都找到， 调用与 key 相同名称的 setXXX 方法。如果有给属性赋值成功。没有的忽略

@ConfigurationProperties注解起作用，还需要@EnableConfigurationProperties 或@ConfigurationPropertiesScan。这个注解是专门寻找@ConfigurationProperties 注解的，将他的对象注入到 Spring 容器。在启动类上使用扫描注解.
直接使用@Component也可以

@ConfigurationPropertiesScan
```java
@ConfigurationPropertiesScan({"com.example.springboot01"})
@SpringBootApplication
public class Springboot01Application {  
	public static void main(String[] args) {  
		SpringApplication.run(Springboot01Application.class, args);  
	}  
}
```

@EnableConfigurationProperties
```java
@EnableConfigurationProperties({Person.class})
@SpringBootApplication
public class Springboot01Application {
    public static void main(String[] args) {
        SpringApplication.run(Springboot01Application.class, args);
    }
}
```

## 处理第三方库对象

>如果某个类需要在配置文件中提供数据，但是没有源代码。此时@ConfigurationProperties 结合@Bean 一起在方法上面使用

直接创建一个新的Bean放入容器

```java
@Configuration
public class ApplicationConfig {
	@ConfigurationProperties(prefix = "security")
	@Bean
	public Security createSecurity(){
		return new Security();
	}
}
```

## 指定数据源文件

>application 做配置是经常使用的，除此以外我们能够指定某个文件作为数据来源
>@PropertySource 是注解， 用以加载指定的 properties 文件
>@PropertySource 与@Configuration 一同使用

读取group-info.properties中的配置
```java
@Component  
@ConfigurationProperties(prefix = "group")  
@PropertySource("classpath:/group-info.properties")
```

# Bean的注册

## xml与@ImprotResource

1. applicationContext.xml中注册bean

```xml
<bean id="myPerson" class="com.bjpowernode.config.pk7.Person">
	<property name="name" value="李四" />
	<property name="age" value="20" />
</bean>
```

2. 启动类使用ImportResource注解导入xml配置

```java
@ImportResource("classpath:/applicationContext.xml")
@EnableConfigurationProperties({Person.class})
@SpringBootApplication
public class Springboot01Application {
    public static void main(String[] args) {
        SpringApplication.run(Springboot01Application.class, args);
    }
}
```

## @Configuration与@Bean

## @Controller,@Service,@Repository,@Component


# 数据




