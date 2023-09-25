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

![[Attachments/Images/Pasted image 20230228131019.png]]

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
