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
