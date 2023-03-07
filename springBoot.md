## JavaConfig

### JavaConfig

使用java类作为xml配置文件的替代，是配置spring容器的纯java方式。在这个java类可以创建java对象，把对象注入到spring容器中

使用两个注解：@Configuration和@Bean

1. @Configuration
	- ==放在类上==，声明这个类为==配置类==，表示这个类作为配置文件使用
	- 里面添加了@Component注解
	- 在被@Configuration注解的类中所有带有@Bean注解的方法都会被CGLib动态代理，而后每次调用这些方法时返回的都是第一次返回的实例
2. @Bean
  - 在@Configuration修饰的类中使用，==放在方法上==。
  - 告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。即声明对象，把对象注入到容器中
  - 未指定bean 的名称，默认bean的名称是==方法名 + 首字母小写==

``` java
@Configuration
publie class MyConfig{
	
    @Bean
	public MyBean myBean() {
        // instantiate and configure MyBean obj
         return obj;
    }
}
```

> @Component（和@Service和@Repository）用于自动检测和使用类路径扫描自动配置bean。注释类和bean之间存在隐式的一对一映射（即每个类一个bean）。该注解作用于类
>
> @Bean用于显式声明单个bean，而不是让Spring像上面那样自动执行它。它将bean的声明与类定义分离，并允许您精确地创建和配置bean。常和@Configuration注解搭配使用。该注解的方法返回值是对象，可以在方法中为对象设置属性。
>
> 区别：
>
> 1. 作用对象不同：`@Component` 注解作用于类，而 `@Bean` 注解作用于方法
> 2. `@Component` 通常是通过路径扫描来自动侦测以及自动装配到 Spring 容器中(我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中)。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean，`@Bean` 告诉了 Spring 这是某个类的实例，当我们需要用它的时候还给我。
> 3. `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring 容器时，只能通过 `@Bean` 来实现。
> 4. 两者都可以使用@Autowired或者@Resource注解注入



### @ImporResource

作用是导入其他的xml配置文件， 等同于在xml中 \<import resources="其他配置文件"/>

``` java
@Configuration
@ImportResource(value ={ "classpath:applicationContext.xml","classpath:beans.xml"})
public class SpringConfig {
}
```



### @PropertyResource

读取properties属性配置文件。使用属性配置文件可以实现外部化配置，在程序代码之外提供数据

SpringBoot 中默认的配置文件是resources 目录下的 application.properties，所以我们不需要额外的配置，直接在其中写上我们需要的信息即可。但是我们要是想自己在其他目录下建配置文件就需要用@PropertyResource

步骤

1. 在resources目录下创，建properties文件，使用k=v的格式提供数据

2. 在PropertyResource指定properties文件的位置

3. 使用@Value(value="${key}")

	``` java
	@Configuration
	@ImportResource(value = "classpath:applicationContext.xml")
	@PropertySource(value = "classpath:config.properties")
	@ComponentScan(basePackages = "com.ming.vo")
	public class SpringConfig {
	}
	```

	

## 入门

### 介绍

1. 为什么要使用 Spring Boot：

	- 因为Spring， SpringMVC 需要使用的大量的配置文件 （xml文件）

	- 还需要配置各种对象，把使用的对象放入到spring容器中才能使用对象

	- 还需要了解其他框架配置规则。

2. SpringBoot 就相当于不需要配置文件的Spring+SpringMVC。 常用的框架和第三方库都已经配置好了，开箱即用。

3. SpringBoot是Spring中的一个成员， 可以简化Spring，SpringMVC的使用。 他的核心还是IOC容器。

4. 特点：

	- 内嵌tomcat， jetty ， undertow

	- 提供了starter起步依赖，简化应用的配置。
		比如使用MyBatis框架，需要在Spring项目中，配置MyBatis的对象SqlSessionFactory， Dao的代理对象。而在SpringBoot项目中，在pom.xml里面，加入一个mybatis-spring-boot-starter依赖即可

	- 尽可能去配置spring和第三方库。即自动配置（就是把spring中的第三方库中的对象都创建好，放到容器中， 开发人员可以直接使用）

	- 提供了健康检查， 统计，外部化配置。不用生成代码，不用使用xml做配置



### 创建SpringBoot项目

第一种方式：使用Spring提供的初始化器，就是向导创建SpringBoot应用

​	使用的地址： https://start.spring.io

​	使用国内的地址：https://start.springboot.io

> - [x] Spring Web

第二种方式：使用 maven 向导创建项目，即自己加入SpringBoot依赖，修改目录结构，创建启动类



### @SpringBootApplication

复合注解：由三个注解组成

​	@SpringBootConfiguration

​	@EnableAutoConfiguration

​	@ComponentScan

#### @SpringBootConfiguration

``` java
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

> 使用了@SpringBootConfiguration注解标注的类，可以作为配置文件使用的，可以使用Bean声明对象，注入到容器

#### @EnableAutoConfiguration

启用自动配置， 把java对象配置好，注入到spring容器中。例如可以把mybatis的对象创建好，放入到容器中。

#### @ComponentScan

@ComponentScan 扫描器，找到注解，根据注解的功能创建对象，给属性赋值等等。

默认扫描的包： @ComponentScan所在的类所在的包和子包。

> @ComponentScan是组件扫描注解，用来扫描@Controller @Service @Repository这类,主要就是定义扫描的路径从中找出标志了需要装配的类到Spring容器中
>
> @MapperScan 是扫描mapper类的注解，就不用在每个mapper类上加@Mapper了



### 使用容器

从容器中获取对象

通过SpringApplication.run(Application.class, args)返回值获取容器。

```java
@SpringBootApplication
public class ContainerApplication {

   public static void main(String[] args) {
      //获取容器对象
      //ConfigurableApplicationContext ctx=SpringApplication.run(ContainerApplication.class, args);
      ApplicationContext ctx=SpringApplication.run(ContainerApplication.class, args);

      //从容器中获取对象
      UserService userService= (UserService) ctx.getBean("userService");
   }

}

//public interface ConfigurableApplicationContext extends ApplicationContext
//ConfigurableApplicationContext接口，是ApplicationContext的子接口
```



### ComnandLineRunner 

ComnandLineRunner 接口，ApplcationRunner接口

这两个接口都有一个run方法。执行时间是在容器对象创建好后，自动执行run()方法。

可以完成自定义在容器对象创建好的一些操作，也就是项目启动之后就立即执行的操作。

1. ComnandLineRunner：参数是一个字符串
2. ApplcationRunner：参数是ApplicationArguments

``` java
@SpringBootApplication
public class CommandLinerRunnerApplication implements CommandLineRunner {

	@Resource
	private HelloService helloService;

	public static void main(String[] args) {
		System.out.println("准备创建容器对象");
		//创建容器对象
		SpringApplication.run(CommandLinerRunnerApplication.class, args);
		System.out.println("容器对象创建之后");
	}

	@Override
	public void run(String... args) throws Exception {
		String str = helloService.sayHello("lis");
		System.out.println("调用容器中的对象"+str);

		//可以做自定义的操作，比如读取文件，数据库等等
		System.out.println("在容器对象创建好，执行的方法");
	}

}

/**
 * 准备创建容器对象
 * 调用容器中的对象你好：lis
 * 在容器对象创建好，执行的方法
 * 容器对象创建之后
 */
```



## 配置文件

### 介绍

1. 配置文件名称：application

2. 扩展名有：properties(k=v)； yml ( k: v)

3. 使用application.properties,  application.yml

	例1：application.properties设置端口和上下文

	``` properties
	#设置端口号
	server.port=8082
	#设置访问应用上下文路径， contextpath
	server.servlet.context-path=/myboot
	```

	例2：application.yml

	``` yaml
	server:
	  port: 8083
	  servlet:
	    context-path: /myboot
	```

	> yml语法
	>
	> - 大小写敏感
	>
	> - 使用缩进表示层级关系
	>
	> - 缩进==不允许使用tab，只允许空格==
	>
	> - 缩进的空格数不重要，只要相同层级的元素左对齐即可
	>
	> - #表示注释
	>
	> - 对象：表示以键值对（key: value）形式出现的数据。使用==冒号+空格==来分开键与值
	>
	> - 以 `-` 开头的行表示构成一个数组。==破折号+空格==开头的数据
	>
	> 	``` yaml
	> 	values:
	> 	  - A
	> 	  - B
	> 	  - C
	> 	```
	>
	> 



### 多环境配置

1. 有开发环境，测试环境， 上线的环境。每个环境有不同的配置信息，例如端口，上下文件， 数据库url，用户名，密码等等

2. 使用多环境配置文件，可以方便的切换不同的配置。

3. 使用方式：创建多个配置文件

4. 名称规则：application-环境名称.properties(yml)

	- 创建开发环境的配置文件：application-dev.properties( application-dev.yml )
	- 创建测试者使用的配置：application-test.properties

5. 只需要在`application.yml`里加上如下配置，就可以切换环境配置

	``` yaml
	spring:
	  profiles:
	    active: dev
	```

	

### @ConfigurationProperties

把配置文件的数据映射为java对象

==该类需要有对应的getter和setter方法==，这样就相当于具备了获取yml文件中参数属性的能力

注意在类上面加入@Component注解，==需要把配置类交给spring容器进行管理才能完成配置的自动注入==

该注解可以放在类上，也可以放在方法上

该注解有一个==prefix属性==，通过指定的前缀，绑定配置文件中的配置

``` java
@Component
@ConfigurationProperties(prefix = "school")
public class SchoolInfo {
    private String name;
    private String website;
    private String address;

    ...

    }
```

``` properties
#自定义key=value
school.name=ytu
school.website=www.mingever.com
school.address=山东烟台

```

``` yaml
school:
  name: ytu
  website: www.mingever.com
  address: 山东烟台
```

> 也可以通过`@value`注解来赋值
>
> @Value 支持三种取值方式
>
> - 字面量
>
> - ${key}从环境变量、配置文件中获取
>
> 	``` java
> 	// @value可以直接放在属性上面，也可以放在set方法上面，注意set方法不能为static,还有就是当前类要交给spring来管理
> 	@Value("${school.name}")
> 	private String name;
> 	```
>
> -  \#{SpEL}



## web组件

拦截器， Servlet ，Filter

### 拦截器Interceptor

拦截器是SpringMVC中一种对象，能拦截器对Controller的请求

拦截器框架中有系统的拦截器，还可以自定义拦截器，实现对请求预先处理。

1. 创建类实现SpringMVC框架的`HandlerInterceptor接口`

	``` java
	public class LoginInterceptor implements HandlerInterceptor {
	/**
	 *
	 * @param request
	 * @param response
	 * @param handler 被拦截的控制器对象
	 * @return boolean
	 *      true：请求能被Controller处理
	 *      false：请求被截断
	 */
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
	    System.out.println("执行了LoginInterceptor的preHandle");
	    return true;
	}
	
	```

2. 在SpringBoot中注册拦截器

	1. 创建拦截器注册器类
	2. 加入`@Configuration注解`
	3. 实现`WebMvcConfigurer接口`
	4. 重写接口的默认方法：`addInterceptors(InterceptorRegistry registry)`，其参数是InterceptorRegistry
	5. 让InterceptorRegistry实现`addInterceptor方法`，其对象是一个拦截器对象
	6. InterceptorRegistry的`addPathPatterns方法`，指定拦截的请求uri地址
	7. InterceptorRegistry的`excludePathPatterns方法`，指定不拦截的地址

	``` java
	@Configuration
	public class MyAppConfig implements WebMvcConfigurer {
	    //添加拦截器对象，注入到容器中
	    @Override
	    public void addInterceptors(InterceptorRegistry registry) {
	        //创建拦截器对象
	        HandlerInterceptor interceptor = new LoginInterceptor();
	        //指定拦截的请求uri地址
	        String[] path={"/user/**"};
	        //指定不拦截的地址
	        String[] excludePath={"/user/login"};
	
	        registry.addInterceptor(interceptor)
	                .addPathPatterns(path)
	                .excludePathPatterns(excludePath);
	    }
	}
	```

	

### servlet

在SpringBoot框架中使用Servlet对象

1. 创建Servlet类。创建类继承HttpServlet

	```  java
	public class MyServlet extends HttpServlet {
	    @Override
	    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        doPost(req, resp);
	    }
	
	    @Override
	    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        //使用HttpServletResponse输出数据，应答结果
	        resp.setContentType("text/html;charset=utf-8");
	        PrintWriter out = resp.getWriter();
	        out.println("===执行的是servlet===");
	        out.flush();
	        out.close();
	    }
	```

2. 注册Servlet ，让框架能找到Servlet

	``` java
	@Configuration
	public class WebApplicationConfig {
	
	    //定义方法，注册Servlet对象
	    @Bean
	    public ServletRegistrationBean servletRegistrationBean(){
	
	        //ServletRegistrationBean(T servlet, String... urlMappings)
	        //第一个参数是Servlet对象，第二个参数是url地址
	        //ServletRegistrationBean bean = new ServletRegistrationBean(new MyServlet(), "/myservlet");
	        ServletRegistrationBean bean = new ServletRegistrationBean(new MyServlet());
	        bean.addUrlMappings("/login","/test"); //<url-pattern>
	        return bean;
	    }
	}
	```

	

### 过滤器Filter

Filter是Servlet规范中的过滤器，可以处理请求，对请求的参数，属性进行调整。常常在过滤器中处理字符编码。

1. 创建自定义过滤器类

	``` java
	public class MyFilter implements Filter {
	    @Override
	    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
	        System.out.println("执行了MyFilter，doFilter");
	        filterChain.doFilter(servletRequest,servletResponse);
	    }
	}
	```

2. 注册Filter过滤器对象

	``` java
	@Configuration
	public class WebApplicationConfig {
	    @Bean
	    public FilterRegistrationBean filterRegistration(){
	        FilterRegistrationBean bean = new FilterRegistrationBean();
	        bean.setFilter(new MyFilter());
	        bean.addUrlPatterns("/user/*");
	        return bean;
	    }
	}
	```

	

### 字符集过滤器

CharacterEncodingFilter：解决servlet中post请求中乱码的问题

在SpringMVC框架，在web.xml注册过滤器。配置它的属性

**1）第一种方式**

1. 配置字符集过滤器

	``` java
	@Configuration
	public class WebSystemConfig {
	    //注册Filter
	    @Bean
	    public FilterRegistrationBean filterRegistrationBean(){
	        FilterRegistrationBean reg = new FilterRegistrationBean();
	
	        //使用框架中的过滤器类
	        CharacterEncodingFilter filter = new CharacterEncodingFilter();
	        //指定编码的方式
	        filter.setEncoding("utf-8");
	        //指定request，response都使用encoding的值
	        filter.setForceEncoding(true);
	
	        reg.setFilter(filter);
	        //指定过滤的url地址
	        reg.addUrlPatterns("/*");
	        return reg;
	    }
	}
	```

2. 修改application.properties文件，让自定义的过滤器起作用

	``` properties
	#springboot中默认已经配置了CharacterEncodingFilter。编码默认ISO-8859-1
	#设置enable=false 作用是关闭系统中配置好的过滤器，使用自定义的CharacterEncodingFilter
	server.servlet.encoding.enabled=false
	```

**2）第二种方式**

修改application.properties文件

``` properties
#让系统的CharacterEncdoingFilter生效，不用写也行，默认是true
server.servlet.encoding.enabled=true
#指定使用的编码方式
server.servlet.encoding.charset=utf-8
#强制request，response都使用charset属性的值
server.servlet.encoding.force=true
```



## 集成ORM

使用MyBatis框架操作数据，在SpringBoot框架集成MyBatis

### 步骤

1. mybatis起步依赖：完成mybatis对象自动配置，对象放在容器中

  ``` xaml
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
  
   <!--mybatis 起步依赖： mybatis 框架需要的依赖全部加入好-->
   <dependency>
   <groupId>org.mybatis.spring.boot</groupId>
   <artifactId>mybatis-spring-boot-starter</artifactId>
   <version>2.1.4</version>
   </dependency>
  
   <!--mysql 驱动-->
   <dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <scope>runtime</scope>
   </dependency>
  ```

2. pom.xml 指定把src/main/java目录中的xml文件包含到classpath中

  ``` xaml
  <resources>
      <resource>
          <directory>src/main/resources</directory>
          <includes>
              <include>**/*.*</include>
          </includes>
      </resource>
  </resources>
  ```

3. 创建实体类Student

4. 创建Dao接口StudentDao , 创建一个查询学生的方法

  ``` java
  //@Mapper：告诉mybatis这是dao接口，会创建此接口的代理对象
  public interface StudentDao {
      Student selectById(@Param("stuId") Integer id);
  }
  ```

5. 创建Dao接口对应的Mapper文件xml文件，写sql语句

  ``` xaml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.ming.dao.StudentDao">
      <select id="selectById" resultType="com.ming.model.Student">
          select id,name,age from student where id=#{stuId}
      </select>
  </mapper>
  ```

6. 创建Service层对象，创建StudentService接口和他的实现类。dao对象的方法。完成数据库的操作

  ``` java
  public interface StudentService {
      Student queryStudent(Integer id);
  }
  ```

  ``` java
  @Service
  public class StudentServiceImpl implements StudentService {
  
      @Resource
      private StudentDao studentDao;
  
      @Override
      public Student queryStudent(Integer id) {
          Student student = studentDao.selectById(id);
          return student;
      }
  }
  ```

7. 创建Controller对象，访问Service

  ``` java
  @Controller
  public class StudentController {
  
      @Resource
      private StudentService studentService;
  
      @RequestMapping("/student/query")
      @ResponseBody
      public Object queryStudent(Integer id){
          Student student = studentService.queryStudent(id);
          return student;
      }
  }
  ```

8. 写application.properties文件，配置数据库的连接信息

	``` properties
	#连接数据库 mysql驱动新版的驱动类
	spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
	spring.datasource.url=jdbc:mysql://localhost:3306/springbootdb?useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT%2B8
	spring.datasource.username=root
	spring.datasource.password=169736
	
	#指定mapper文件的位置
	mybatis.mapper-locations=classpath:mapper/*.xml
	
	#指定mybatis的日志
	#mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImp
	```

9. 在springboot启动类里，加上@MapperScan

	``` java
	/**
	 * @MapperScan：找到Dao接口和Mapper文件
	 * 		basePackages：Dao接口所在的包名
	 */
	
	@SpringBootApplication
	@MapperScan(basePackages = "com.ming.dao")
	public class MapperApplication {
	
		public static void main(String[] args) {
			SpringApplication.run(MapperApplication.class, args);
		}
	
	}
	```

	

### 扫描Dao接口

**1）第一种方式 ：@Mapper**

@Mapper：放在dao接口的上面， 每个接口都需要使用这个注解

``` java
/**
 * @Mapper：告诉MyBatis这是dao接口，创建此接口的代理对象。
 *     位置：在类的上面
 */
@Mapper
public interface StudentDao {

    Student selectById(Integer id);
}

```

**2）第二种方式 ：@MapperScan**

``` java
	/**
	 * @MapperScan: 找到Dao接口和Mapper文件
	 *     basePackages：Dao接口所在的包名
	 */
	@SpringBootApplication
	@MapperScan(basePackages = {"com.ming.dao","com.ming.mapper"})
	public class Application {
}
```

**3）第三种方式： Mapper文件和Dao接口分开管理**

现在把Mapper文件放在resources目录下

1. 在resources目录中创建子目录（自定义的），例如mapper

2. 把mapper文件放到mapper目录中

3. 在application.properties文件中，指定mapper文件的目录

	``` properties
	#指定mapper文件的位置
	mybatis.mapper-locations=classpath:mapper/*.xml
	```

4. 在pom.xml中指定把resources目录中的文件，编译到目标目录中

	``` xaml
	<resources>
	    <resource>
	        <directory>src/main/resources</directory>
	        <includes>
	            <include>**/*.*</include>
	        </includes>
	    </resource>
	</resources>
	```

	

### 事务

**1）Spring框架中的事务**

1. 管理事务的对象：事务管理器（接口，接口有很多的实现类）

	​	例如：使用Jdbc或mybatis访问数据库，使用的事务管理器：DataSourceTransactionManager

2. 声明式事务：在xml配置文件或者使用注解说明事务控制的内容

​			控制事务： 隔离级别，传播行为， 超时时间

3. 事务处理方式
	1. Spring框架中的@Transactional
	2. aspectj框架可以在xml配置文件中，声明事务控制的内容

**2）SpringBoot中使用事务：上面的两种方式都可以**

1. 在业务方法的上面加入@Transactional , 注解后，方法就有事务功能了
2. 在启动类的上面，加入@EnableTransactionManager

``` java
/**
 * @Transactional: 表示方法的有事务支持
 *       默认：使用库的隔离级别， REQUIRED 传播行为； 超时时间  -1
 *       抛出运行时异常，回滚事务
 */
@Transactional
@Override
public int addStudent(Student student) {
    System.out.println("业务方法addStudent");
    int rows  =  studentDao.insert(student);
    System.out.println("执行sql语句");

    //抛出一个运行时异常， 目的是回滚事务
    //int m   = 10 / 0 ;

    return rows;
}
```



## RESTful

一种接口架构风格

接口(API)：可以指访问servlet， controller的url，调用其他程序的函数

架构风格：api组织方式。如 http://localhost:9002/mytrans/addStudent?name=lisi&age=26（在地址上提供了访问的资源名称addStudent, 在其后使用了get方式传递参数。）

### REST

Representational State Transfer , 表现层状态转移

1. REST：是一种接口的架构风格和设计的理念，不是标准

2. 优点：更简洁，更有层次

3. 表现层状态转移

  - 表现层就是视图层，显示资源的，通过视图页面，jsp等等显示操作资源的结果。
  -  状态：资源变化
  - 转移：资源可以变化的。资源能创建，new状态，资源创建后可以查询资源，能看到资源的内容，这个资源内容 ，可以被修改，修改后资源和之前的不一样。 

4. 一句话说明REST：==使用url表示资源 ，使用http动作操作资源==

	​	URL定位资源，用HTTP动词(GET,POST,DELETE,PUT)描述操作

	​	看Url就知道要什么

	​	看http method就知道干什么

	​	看http status code就知道结果如何

5. 冲突：要保证==请求方式+请求路径==一定是唯一的。RESRful很容易发送冲突，发生冲突后，要：

	1. 更改请求方式
	2. 改变请求路径


### REST中的要素

1. 用REST表示资源和对资源的操作。 在互联网中，表示一个资源或者一个操作资源是使用url表示的，图片，视频，文本，网页等等都是资源。资源是用名词来表示。

2. 通过名词表示资源：在url中，使用名词表示资源，以及访问资源的信息，使用`/`分隔对资源的信息；使用http中的动作(请求方式)，表示对资源的操作(CURD)

	- GET: 查询资源 -- sql select

		​	处理单个资源： 用他的单数方式

		 	http://localhost:8080/myboot/student/1001
	 	
		 	http://localhost:8080/myboot/student/1001/1

		​	处理多个资源：使用复数形式

		​	http://localhost:8080/myboot/students/1001/1002

	- POST: 创建资源 -- sql insert

		​	http://localhost:8080/myboot/student

		​	在post请求中传递数据

		``` html
		<form action="http://localhost:8080/myboot/student" method="post">
		    姓名：<input type="text" name="name" />
		    年龄：<input type="text" name="age" />
		</form>
		```

	- PUT： 更新资源 -- sql update

		``` html
		<form action="http://localhost:8080/myboot/student/1" method="post">
		    姓名：<input type="text" name="name" />
		    年龄：<input type="text" name="age" />
		         <input type="hidden" name="_method" value="PUT" />
		</form>
		```

	- DELETE: 删除资源 -- sql delete

		``` html
		<a href="http://localhost:8080/myboot/student/1">删除1的数据</a>
		```

	- 需要的分页， 排序等参数，依然放在url的后面

		​	http://localhost:8080/myboot/students?page=1&pageSize=20



> 老版本浏览器或html老版本中，form表单中的method可能不支持put、delete，需要在SpringMVC中开启一个过滤器，将post请求转为put ,delete
>
> ``` properties
> #启用支持put，delete
> spring.mvc.hiddenmethod.filter.enabled=tr
> ```
>
> 

### 注解

- @PathVariable：映射URL绑定的占位符，可将URL中占位符参数绑定到控制器处理方法的入参中

	- 若方法参数名称和需要绑定的url中变量名称一致时,可以简写

		``` java
		@RequestMapping("/getUser/{name}")
		public User getUser(@PathVariable String name){
		    return userService.selectUser(name);
		}
		```

	- 若方法参数名称和需要绑定的url中变量名称不一致时，写成

		``` java
		@RequestMapping("/getUserById/{name}")
		public User getUser(@PathVariable("name") String userName){
		    return userService.selectUser(userName);
		}
		```

- @GetMapping：支持的get请求方式，等同于@RequestMapping(method=RequestMethod.GET)

- @PostMapping：支持post请求方式 ，等同于@RequestMapping(method=RequestMethod.POST)

- @PutMapping：支持put请求方式，等同于@RequestMapping(method=RequestMethod.PUT)

- @DeleteMapping：支持delete请求方式，等同于 @RequestMapping(method=RequestMethod.DELETE)

- @RestController: 复合注解，是@Controller 和@ResponseBody组合。在类的上面使用@RestController ， 表示当前类者的所有方法都加入了@ResponseBody

### DEMO

``` java
@RestController
public class MyRestController {
    //学习注解的使用

    //查询id=1001的学生
    /**
     * @PathVariable(路径变量)：获取url中的数据
     *      属性：value：定义路径变量名的，可省略
     *      位置：放在控制器方法的形参前面
     * {stuId}：定义路径变量，stuId自定义名称
     */
    @GetMapping("/student/{stuId}")
    public String queryStudent(@PathVariable(value="stuId") Integer studentId){
        return "查询学生studentId="+studentId;
    }

    /**
     * 创建资源 Post请求
     * http://localhost:8080/myboot/student/zhangsan/20
     */
    @PostMapping("/student/{name}/{age}")
    public String createStudent(@PathVariable("name") String name,@PathVariable("age") Integer age){
        return "创建资源 Student：name="+name+" age="+age;
    }

    /**
     * 更新资源
     *
     * 当路径变量名称和形参名一样，@PathVariable中的value可以省略
     */
    @PutMapping("/student/{id}/{age}")
    public String modifyStudent(@PathVariable Integer id,@PathVariable Integer age){
        return "更新资源，执行put请求方式：id="+id+" age="+age;
    }

    /**
     * 删除资源
     */
    @DeleteMapping("/student/{id}")
    public String removeStudentById(@PathVariable Integer id){
        return "删除资源，执行delete id="+id;
    }

    @PutMapping("/student/test")
    public String test(){
        return "执行student/test，使用的请求方式 post";
    }

    @DeleteMapping("/student/testdelete")
    public String testDelete(){
        return "执行student/testdelete，使用的请求方式 delete";
    }
}
```



## Maven

### 安装

1. 下载

	解压后放到目录即可

	 http://maven.apache.org/download.html 

2. 配置环境变量

	将maven的bin 目录放到path中，如`D:\install\maven\apache-maven-3.9.0\bin`

	通过`mvn -v`来测试

3. 打开conf\settings.xml文件，配置本地仓库地址和阿里云远程仓库

	``` xaml
	<!--注意/不要写反-->
	<localRepository>D:/install/maven/repositary</localRepository>
	
	<mirror>
	  <id>alimaven</id>
	  <name>aliyun maven</name>
	  <url>
	      http://maven.aliyun.com/nexus/content/groups/public/
	  </url>
	  <mirrorOf>central</mirrorOf>        
	</mirror>
	```

4. 集成idea（一定要为其他项目设置，不然新建的项目还是idea默认maven）

	![](https://oss.mingever.com/note/springBoot/maven_idea.png)



## Lombok

### 安装

1. 依赖

	``` xaml
	<dependency>
	    <groupId>org.projectlombok</groupId>
	    <artifactId>lombok</artifactId>
	    <optional>true</optional>
	</dependency>
	```

2. 安装Lombok插件。

3. 在File-settings-build-Annotation Processors里开启Enable annotation processing来让Lombok注解在编译阶段起到作用

### 常用注解

- **@Getter/@Setter**

	作用类上，生成所有成员变量的getter/setter方法；作用于成员变量上，生成该成员变量的getter/setter方法。可以设定访问权限及是否懒加载等。

- ToString：作用于类，覆盖默认的toString()方法，可以通过of属性限定显示某些字段，通过exclude属性排除某些字段。

- @EqualsAndHashCode：作用于类，覆盖默认的equals和hashCode

- **@NonNull**：主要作用于成员变量和参数中，标识不能为空，否则抛出空指针异常

- @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor

	作用于类上，用于生成构造函数。有staticName、access等属性。
	staticName属性一旦设定，将采用静态方法的方式生成实例，access属性可以限定访问权限。

	- **@NoArgsConstructor**：生成无参构造器
	- **@RequiredArgsConstructor**：生成包含final和@NonNull注解的成员变量的构造器
	- **@AllArgsConstructor**：生成全参构造器

- **@Data**：作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter @Setter @RequiredArgsConstructor

- @Builder：作用于类上，将类转变为建造者模式

- @Log：作用于类上，生成日志变量。针对不同的日志实现产品，有不同的注解



## MyBatis-Plus

### 快速开始

1. 依赖

	``` xaml
	<dependency>
	    <groupId>com.baomidou</groupId>
	    <artifactId>mybatis-plus-boot-starter</artifactId>
	    <version>最新版本</version>
	</dependency>
	```

2. 配置

	- 配置 MapperScan 注解

		``` java
		@SpringBootApplication
		@MapperScan("com.ming.dao")
		```

	- 或在yml中指定

		``` yaml
		#mybatis-plus设置
		mybatis-plus:
		  #mapper位置
		  mapper-locations: classpath:mapper/*.xml
		  #configuration:
		    #sql日志打印
		    #log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
		```

### 注解

``` java
@TableName("sys_user")
public class User {
    @TableId(value = "user_id",type = IdType.AUTO)
    private Long id;
    @TableField("user_name")
    private String name;
    private Integer age;
    private String email;
}

/*
对于驼峰式命名的实体名和字段名，mybatis-plus默认处理是通过下划线分隔,比如
SysUser实体名默认映射sys_user表；
userName成员变量名默认映射是user_name字段；
*/
```

- **@TableName**

	表名注解，使用在实体类中，标识实体类对应的表。

	默认情况下，如果数据库表是使用标准的下划线命名，并且能对应上实体类的类名，我们就不需要加上此注解去手动匹配，比如表名为==sys_user==，那么会自动匹配==SysUser==实体类

	当数据库名与实体类名不一致或不符合驼峰命名时，需要在此注解用value指定表名

- **@TableId**

	主键注解，使用在实体类主键字段中

	@TableId和@TableFieId同@TableName一样，如果数据库表里的字段名使用标准的下划线命名，并且能对应上实体类的成员名称(驼峰命名)我们就不需要特别去手动匹配，如字段名为==user_name==，那么就会自动匹配==userName==成员变量

	当数据库字段名和实体类属性名不一致或不符合驼峰命名时，就需要用在此注解用==@TableId==或==@TableFieId==的==value==来指定表字段名

	属性

	- value：主键字段名
	- type：指定主键类型，默认值IdType.NONE
		-  AUTO：数据库 ID 自增
		- NONE：无状态，该类型为未设置主键类型
		- INPUT：insert 前自行 set 主键值
		- ASSIGN_ID：分配 ID，主键类型为 Number(Long 和 Integer)或 String，雪花算法
		- ASSIGN_UUID：分配 UUID,主键类型为 String

- **TableFieId**：字段注解（非主键）。主要用来解决实体类的字段名与数据库中的字段名不匹配的问题

### 接口

mybatis-plus 提供两种包含预定义增删改查操作的接口：`BaseMapper`和`IService`

Mybatis-plus提供了2个接口1个类：

- **BaseMapper\<T>**：针对dao层的方法封装CRUD，需要指定对应的实体类
- **IService\<T>** 针对业务逻辑层的封装，需要指定对应的实体类，是在BaseMapper基础上的加强
- **ServiceImpl<M extends BaseMapper\<T>, T> implements IService**：针对业务逻辑层的实现，需要指定Dao层类和对应的实体类

### 用法

1. User实体类

	``` java
	@TableName("user")
	@Data
	@AllArgsConstructor
	@NoArgsConstructor
	public class User {
	    @TableId(value = "id",type = IdType.AUTO)
	    private Integer id;
	    private String loginName;
	    private String userName;
	    private String password;
	    private String sex;
	    private String email;
	    private String address;
	}
	```

2. UserDao接口

	``` java
	public interface UserDao extends BaseMapper<User> {}
	
	/*
	Dao继承该接口后，无需编写mapper.xml文件，即可获得CRUD功能。
	BaseMapper的泛型需要传入实体类，来与其绑定，生成代理对象，对其进行功能增强等（我猜的）
	*/
	```

3. UserService接口

	``` java
	public interface UserService extends IService<User>{}
	
	/*
	IService是对BaserMapper的增强，泛型需要传入实体类，来与其绑定
	里面很多方法就是调用的BaserMapper的，只是改了个名字为了与BaserMapper的方法区分；有些方法是对BaserMapper的增强，另外还扩展了一些方法，如批量处理等。尽量用IService，BaserMapper有的它都有。
	
	service接口其实没必要继承IService，因为这里Userservice的实现类UserServiceImpl需要继承ServiceImpl。ServiceImpl里实现了IService，通过ServiceImpl就可以使用IService的默认方法，和其实现的IService的方法。
	如果实现类不继承ServiceImpl，直接实现UserService接口的话，那么需要实现IService里的方法，显然这样不可以
	
	其实如果不考虑扩展的话，连service接口都不必要写
	*/
	```

4. UserService实现类

	``` java
	@Service
	public class UserServiceImpl extends ServiceImpl<UserDao, User> implements UserService{}
	```

	





## 注解

### 创建对象的

@Controller: 放在类的上面，创建控制器对象，注入到容器中

@RestController: 放在类的上面，创建控制器对象，注入到容器中。

作用：复合注解：@Controller , @ResponseBody。使用这个注解类的，里面的控制器方法的返回值都是数据

@Service：放在业务层的实现类上面，创建service对象，注入到容器

@Repository : 放在dao层的实现类上面，创建dao对象，放入到容器。 

没有使用这个注解，是因为现在使用MyBatis框架，dao对象是MyBatis通过代理生成的不需要使用@Repository

@Component: 放在类的上面，创建此类的对象，放入到容器中。 

### 赋值的

@Value ： 简单类型的赋值，例如在属性的上面使用@Value("李四")  private String name
还可以使用@Value,获取配置文件者的数据（properties或yml）  @Value("${server.port}")  private Integer port

@Autowired: 引用类型赋值自动注入的，支持byName, byType，默认是byType。放在属性的上面，也可以放在构造方法的上面。推荐是放在构造方法的上面。

@Qualifer: 给引用类型赋值，使用byName方式。 

@Autowird和@Qualifer都是Spring框架提供的

@Resource：来自jdk中的定义，javax.annotation。实现引用类型的自动注入， 支持byName, byType。默认是byName，如果byName失败，再使用byType注入。 在属性上面使用。

### 其他

@Configuration ： 放在类的上面，表示这是个配置类，相当于xml配置文件

@Bean：放在方法的上面，把方法的返回值对象，注入到spring容器中。

@ImportResource：加载其他的xml配置文件， 把文件中的对象注入到spring容器中

@PropertySource：读取其他的properties属性配置文件

@ComponentScan：扫描器 ，指定包名，扫描注解的

@ResponseBody: 放在方法的上面，表示方法的返回值是数据，不是视图

@RequestBody : 把请求体中的数据，读取出来，转为java对象使用。

@ControllerAdvice: 控制器增强，放在类的上面， 表示此类提供了方法，可以对controller增强功能。

@ExceptionHandler : 处理异常的，放在方法的上面

@Transcational : 处理事务的， 放在service实现类的public方法上面， 表示此方法有事务

@PathVariable：映射URL绑定的占位符，可将URL中占位符参数绑定到控制器处理方法的入参中

### SpringBoot中使用的注解

@SpringBootApplication ： 放在启动类上面， 包含了@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan

### MyBatis相关的注解

@Mapper：放在类的上面 ， 让MyBatis找到接口：创建他的代理对象  

@MapperScan：放在主类的上面：指定扫描的包：把这个包中的所有接口都创建代理对象，对象注入到容器中

@Param：放在dao接口的方法的形参前面，作为命名参数使用
