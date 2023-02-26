## 概述

### MVC三层架构

Model-View-Controller（模型-视图-控制器）

用户使用界面层--> 业务逻辑层--->数据访问层（持久层）-->数据库（mysql）

1. 介绍
	- 界面层V： ==和用户打交道的， 接收用户的请求参数， 显示处理结果==。（jsp ，html ，servlet）
	- 业务逻辑层C： ==接收了界面层传递的数据，计算逻辑，调用数据库，获取数据==
	- 数据访问层M： ==访问数据库==，对数据库进行操作，执行对数据的查询，修改，删除等
2. 对应的包以及框架
	- 界面层： controller包 （servlet） ---springMVC
	- 业务逻辑层： service 包（XXXService类）---spring
	- 数据访问层： dao包（XXXDao类）---mybatis

### mybatis框架

mybatis是一个sql映射框架，提供的数据库的操作能力。增强的JDBC。

使用mybatis让开发人员集中精神写sql就可以了，不必再关心Connection,Statement,ResultSet的创建，销毁，sql的执行。

1. MyBatis SQL Mapper Framework for Java （sql映射框架）

	- sql mapper：sql映射，可以把数据库表中的一行数据 映射为 一个java对象；操作这个对象，就相当于操作表中的数据
	- Data Access Objects（DAOs）：数据访问 ，对数据库执行增删改查

2. 使用JDBC的缺陷

	- 代码比较多，开发效率低
	- 需要关注 Connection ,Statement,     ResultSet 对象创建和销毁
	- 对 ResultSet 查询的结果，需要自己封装为 List
	-  重复的代码比较多些
	- 业务代码和数据库的操作混在一起

3. mybatis提供了哪些功能

	- ==提供了创建Connection     ,Statement, ResultSet的能力== ，不用开发人员创建这些对象了

	- ==提供了执行sql语句的能力==， 不用你执行sql

	- ==提供了循环sql， 把sql的结果转为java对象， List集合的能力==

		``` java
		while (rs.next()) {
		        Student stu = new Student();
		        stu.setId(rs.getInt("id"));
		        stu.setName(rs.getString("name"));
		        stu.setAge(rs.getInt("age"));
		        //从数据库取出数据转为 Student 对象，封装到 List 集合
		        stuList.add(stu);
		}
		```

	- ==提供了关闭资源的能力==，不需要开发人员自己关闭Connection,     Statement, ResultSet

	- 开发人员只需要提供sql语句：开发人员提供sql语句--mybatis处理sql---开发人员得到List集合或java对象（表中的数据）



## 入门

### 基本步骤

1. maven依赖

	``` xaml
	<!--mybatis依赖-->
	<dependency>
	  <groupId>org.mybatis</groupId>
	  <artifactId>mybatis</artifactId>
	  <version>3.5.1</version>
	</dependency>
	<!--mysql驱动-->
	<dependency>
	  <groupId>mysql</groupId>
	  <artifactId>mysql-connector-java</artifactId>
	  <version>8.0.16</version>
	</dependency>
	
	<!--指定文件插件-->
	<resources>
	  <resource>
	    <directory>src/main/java</directory><!--所在的目录-->
	    <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
	      <include>**/*.properties</include>
	      <include>**/*.xml</include>
	    </includes>
	    <filtering>false</filtering>
	  </resource>
	</resources>
	```

2. 创建Dao接口：定义操作数据的方法

3. 创建sql映射文件

	- 一般一个表一个sql映射文件
	- 定义在接口所在的目录中
	- ==文件名称和接口名保存一致==

	``` xaml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<mapper namespace="com.ming.dao.StudentDao">
	    <!--
	        select:表示查询操作
	            id：你要执行的sql语句的唯一表示，mybatis会使用这个id的值来找到要执行的sql语句
	            可以自定义，但是建议使用接口中的方法名称
	        resultType：表示结果类型的，是sql语句执行后得到ResultSet，遍历这个ResultSet得到java对象的类型
	            值写的是类型的全限定名称。
	    -->
	    <select id="selectStudents" resultType="com.ming.domain.Student">
	        select id,name,email,age from student order by id
	    </select>
	</mapper>
	<!--
	sql映射文件(sql mapper)：写sql语句的，mybatis会执行这些sql
	1.指定约束文件
	    <!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	    mybatis-3-mapper.dtd是约束文件的名称，扩展名是.dtd
	2.约束文件作用：限制，检查在当前文件中出现的标签，属性，必须符合mybatis的要求
	3.mapper:是当前文件的根标签，是必须的。
	  namespace：命名空间，唯一值的，可以是自定义的字符串,要求你使用dao接口的全限定名称
	4.在当前文件中，可以使用特点的标签，来表示数据库的特定操作
	    <select>表示执行查询
	    <update>表示更新数据库的操作，就是在<update>标签中，写的是update sql语句的
	    <insert>表示插头，放的是insert语句
	    <delete>表示删除，执行的是delete语句
	-->
	```

4. 创建mybatis主配置文件

	- 一个项目就一个主配置文件
	- 主配置文件==提供了数据库的连接信息和sql映射文件的位置信息==

	``` xaml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <!--指定properties文件的位置，从类路径跟开始找文件，要写在configuration第一行-->
	    <properties resource="jdbc.properties" />
	    <!--settings:控制mybatis全局行为的-->
	    <settings>
	        <!--设置Mybatis输出日志-->
	        <setting name="logImpl" value="STDOUT_LOGGING"/>
	    </settings>
	    <!--定义别名-->
	    <typeAliases>
	        <!--
	            第一种方式：
	            可以指定一个类型的一个自定义别名
	            type:自定义类型的全限定名称
	            alias：别名（短小，容易记忆的）
	        -->
	        <!--typeAlias type="com.ming.domain.Student" alias="stu"/-->
	        <!--
	            第二种方式：
	            <package> name是包名，这个包中的所有类，类名就是别名（类名不区分大小写）
	        -->
	        <package name="com.ming.domain"/>
	    </typeAliases>
	    <!--环境配置：数据库的连接信息
	            default：必须和某个environment的id值一致，告诉mybatis使用哪个数据库的连接信息，也就是访问哪个数据库
	    -->
	    <environments default="mydev">
	        <!--environment：一个数据库信息的配置，环境
	                id:一个唯一值，自定义的，表示环境的名称
	        -->
	        <environment id="mydev">
	            <!--transactionManager：mybatis提交事务，回滚事务的方式
	                type: 事务的处理的类型
	                    1.JDBC：表示mybatis底层是调用JDBC中的Connection对象的commit、rollback做事务处理
	                    2.MANAGED：把mybatis的事务处理委托给其他的容器（一个服务器软件，一个框架(Spring)）
	            -->
	            <transactionManager type="JDBC"/>
	            <!--dataSource：表示数据源，java体系中，规定实现了javax.sql.DataSource接口都是数据源
	                            数据源表示Connection对象的
	                type：指定数据源的类型，
	                    1.POOLED：表示使用连接池，mybatis会创建PooledDataSource类
	                    2.UPOOLED：不使用连接池，在每次执行sql语句时，先创建连接，执行sql，再关闭连接
	                               mybatis会创建一个UnPooledDataSource，管理Connection对象的使用
	                    3.JNDI：java命名和目录服务（类似win注册表）
	            -->
	            <dataSource type="POOLED">
	                <!--driver,user,username,password是固定的，不能自定义 -->
	                <!--数据库的驱动类名-->
	                <property name="driver" value="${jdbc.driver}"/>
	                <!--连接数据库的url字符串-->
	                <property name="url" value="${jdbc.url}"/>
	                <!--访问数据库的用户名-->
	                <property name="username" value="${jdbc.user}"/>
	                <!--密码-->
	                <property name="password" value="${jdbc.password}"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!--指定sql mapper(sql映射文件)的位置-->
	    <mappers>
	        <!--一个mapper标签指定一个文件的位置
	            从类路径开始的路径信息。 target/clasess(类路径)
	        -->
	        <!--第一种方式：指定多个mapper文件，使用多行mapper标签语句-->
	        <!--mapper resource="com/ming/dao/StudentDao.xml"/-->
	        <!--第二种方式：使用包名指定多个mapper文件
	            name：xml文件(mapper文件)所在的包名，这个包中所有xml文件一次都能加载给mybatis
	            使用package的要求：
	                1.mapper文件名称需要和接口名称一样，区分大小写
	                2.mapper文件和dao接口需要在同一目录
	        -->
	        <package name="com.ming.dao"/>
	
	    </mappers>
	</configuration>
	
	<!--
	    mybatis的主配置文件：主要定义了数据库的配置信息；sql映射文件的位置
	    1.约束文件
	        <!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	         mybatis.org/dtd/mybatis-3-config.dtd：约束文件的名称
	    2.configuration：根标签
	-->
	
	属性配置文件jdbc.properties：
	jdbc.driver=com.mysql.cj.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/springdb?useSSL=false&serverTimezone=UTC
	jdbc.user=root
	jdbc.password=169736
	```

5. 使用mybatis的对象`SqlSession`，通过它的方法执行sql语句

	- Resources：mybatis中的一个类， 负责读取主配置文件

		``` java
		InputStream in = Resources.getResourceAsStream("mybatis.xml");
		```

	- `SqlSessionFactoryBuilder` : 创建`SqlSessionFactory`对象

		``` java
		SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
		SqlSessionFactory factory = builder.build(in);
		```

	- `SqlSessionFactory`：重量级对象，程序创建一个对象耗时比较长，使用资源比较多，在整个项目中，有一个就够用了。

		- SqlSessionFactory接口：接口实现类DefaultSqlSessionFactory

		- ==SqlSessionFactory作用： 获取SqlSession对象==

			``` java
			SqlSession sqlSession = factory.openSession();
			```

		- openSession()方法

			1. openSession()：无参数的，获取是非自动提交事务的SqlSession对象
			2. openSession(boolean)：openSession(true) 获取自动提交事务的SqlSession；
			    openSession(false) 非自动提交事务的SqlSession对象

	- `SqlSession`

		- SqlSession接口 ：==定义了操作数据的方法== 例如 selectOne() ,selectList() ,insert(),update(), delete(),     commit(), rollback()
		- SqlSession接口的实现类DefaultSqlSession
		- ==当与数据库建立一次连接，就会创建一个SqlSession对象==，SqlSession对应一次数据库会话，它不是永久存货的，每次访问数据库都需要创建它。
		- ==SqlSession对象不是线程安全的==，需要在方法内部使用， 在执行sql语句之前，使用openSession()获取SqlSession对象。
			在执行完sql语句后，需要关闭它，执行SqlSession.close(). 这样能保证他的使用是线程安全的。

### 动态代理

使用`SqlSession.getMapper(dao接口.class)` 获取这个dao接口的对象
由mybatis帮你创建dao接口的实现类，在实现类中调用SqlSession的方法执行sql语句

1. 步骤
	- 获取SqlSession对象，SqlSessionFactory.opeanSession()
	- 使用getMapper方法获取某个接口的对象，sqlSession.getMapper(接口.class)
	- 使用dao接口的方法，调用方法就执行了mapper文件中的sql语句
2. 使用动态代理的要求
	- dao接口和mapper文件放在一起，同一个目录
	- ==dao接口和mapper文件名要一致==
	- ==mapper文件中的namespace的值是dao接口的全限定名称==
	- mapper文件中的\<select> \<insert> \<update> \<delete>等的id是接口中方法的名称
	- dao接口中不要使用重载方法

### Demo

1. 原始写法

	StudentDao

	``` java
	//接口操作Studetn表
	public interface StudentDao {
	    //查询student表所有数据
	    public List<Student> selectStudents();
	    //插入方法,返回值int，表示执行insert操作后，影响数据库的行数
	    public  int insertStudent(Student student);
	}
	```

	StudentDao.xml

	``` xaml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<mapper namespace="com.ming.dao.StudentDao">
	    <select id="selectStudents" resultType="com.ming.domain.Student">
	        select id,name,email,age from student order by id
	    </select>
	    <!--插入操作-->
	    <insert id="insertStudent">
	        insert into student value (#{id},#{name},#{email},#{age})
	    </insert>
	</mapper>
	```

	Test

	``` java
	public static void main(String[] args) {
	    //访问mybatis读取student数据
	    //1.定义mybatis主配置文件的名称,从类路径根开始（target/clasess），即编译前放在resources包里
	    String config="mybatis.xml";
	    try {
	        //2.读取这个config表示的文件
	        InputStream in=Resources.getResourceAsStream(config);
	        //3.创建SqlSessionFactoryBuilder对象
	        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
	        //4.创建SqlSessionFactory对象
	        SqlSessionFactory factory = builder.build(in);
	        //5.【重要】获取SqlSession对象，从SqlSessionFactory中获取SqlSession
	        SqlSession sqlSession = factory.openSession();
	        //6.【重要】指定要执行的sql语句的标识。sql映射文件中的namespace+"."+标签的id值
	        //String sqlId = "com.ming.dao.StudentDao"+"."+"selectStudents";
	        String sqlId = "com.ming.dao.StudentDao.selectStudents";
	        //7.执行sql语句，通过sqlId找到语句
	        List<Student> list=sqlSession.selectList(sqlId);
	        //8.输出结果
	        list.forEach(stu -> System.out.println(stu));
	        /* 相当于
	        for(Student stu:list){
	            System.out.println(stu.getId()+stu.getName()+stu.getEmail()+stu.getAge());
	        }
	        */
	        //9.关闭SqlSession对象
	        sqlSession.close();
	    } catch (IOException e) {
	        e.printStackTrace();
	    }
	}
	```

2. 把获取SqlSession对象的代码封装成一个工具类

	MyBatisUtils

	``` java
	public class MyBatisUtils {
	    private  static SqlSessionFactory factory = null;
	    static {
	        String config="mybatis.xml";
	        try {
	            InputStream in = Resources.getResourceAsStream(config);
	            //创建SqlSessionFactory对象，使用SqlSessionFactoryBuild
	            factory = new SqlSessionFactoryBuilder().build(in);
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	    //获取SqlSession的方法
	    public static SqlSession getSqlSession(){
	        SqlSession sqlSession = null;
	        if(factory!=null){
	            sqlSession=factory.openSession(true);
	
	        }
	        return sqlSession;
	    }
	}
	```

	Test

	``` java
	public static void main(String[] args) {
	    try {
	        //5.【重要】获取SqlSession对象，从SqlSessionFactory中获取SqlSession
	        SqlSession sqlSession = MyBatisUtils.getSqlSession();
	        //6.【重要】指定要执行的sql语句的标识。sql映射文件中的namespace+"."+标签的id值
	        //String sqlId = "com.ming.dao.StudentDao"+"."+"selectStudents";
	        String sqlId = "com.ming.dao.StudentDao.selectStudents";
	        //7.执行sql语句，通过sqlId找到语句
	        List<Student> list=sqlSession.selectList(sqlId);
	        //8.输出结果
	        list.forEach(stu -> System.out.println(stu));
	        //9.关闭SqlSession对象
	        sqlSession.close();
	    } catch (Exception e) {
	        e.printStackTrace();
	    }
	
	}
	```

3. 动态代理

	``` java
	@Test
	public void testSelectStudents(){
	    /**
	     * 使用mybatis动态代理机制，使用SqlSession.getMapper(dao接口)
	     * getMapper能获取dao接口对应的实现类对象
	     */
	    SqlSession sqlSession = MyBatisUtils.getSqlSession();
	    StudentDao dao=sqlSession.getMapper(StudentDao.class);
	    //com.sun.proxy.$Proxy2: jdk的动态代理
	    //调用dao方法，执行数据库操作
	    List<Student> studentList = dao.selectStudents();
	    for(Student stu:studentList){
	        System.out.println(stu);
	    }
	}
	```

	

## 传入参数

从java代码中把数据传入到mapper文件的sql语句中

### 参数

#### parameterType

它是写在mapper文件中的一个属性。==表示dao接口中方法的参数的数据类型==

> 例如：parameterType="java.lang.Integer"
>             parameterType="int"
>    注意：parameterType不是强制的，因为mybatis通过反射能够发现接口参数的数据类型，==一般不用写==

#### 一个简单类型的参数

简单类型： mybatis把java的==基本数据类型和String==都叫简单类型

在mapper文件获取简单类型的一个参数的值，使用 `#{任意字符}`

> ==注：如果在动态 SQL 中使用了参数作为变量，即使你只有一个参数，那么也需要 @Param 注解==

``` java
//接口
public Student  selectStudentById(Integer id) 
```

``` xaml
<!--mapper文件-->
<select id="selectStudentById" parameterType="java.lang.Integer" resultType="com.ming.daomain.Student">
        select id,name,email,age from student where id=#{id}
</select>

<!--parameterType：用来表示的是dao接口中方法参数的数据类型
parameterType它的值是java的数据类型全限定名称或者是mybatis定义的别名
例如：parameterType="java.lang.Integer"
     parameterType="int"
注意：parameterType不是强制的，因为mybatis通过反射能够发现接口参数的数据类型，一般不用写

使用#{}之后，mybatis执行sql是使用的jdbc中的PreparedStatement对象
由Mybatis执行下面的代码：
1.Mybatis创建Connection,PreparedStatement对象
    String sql="select id,name,email,age from student where id=?";
    PreparedStatement pst = conn.PreparedStatement(sql);
    pst.SetInt(1,1001);
2.执行sql封装为resultType="com.ming.daomain.Student" 这个对象
    ResultSet rs = ps.executeQuery();
    while(re.next){
        //从数据库取表的一行数据，存到一个java对象属性中
        Student stu = new Student();
        stu.setId(rs.getInt("id"));
        stu.setName(rs.getString("name"));
        stu.setEmail(rs.getString("email"))
        stu.setAge(rs.getInt("age"));
    }
    return student;//赋给了dao方法调用的返回值
-->
```

#### 多个参数

1. 使用`@Param`命名参数

	``` java
	//接口
	public List<Student> selectMulitParam(@Param("myname") String name, @Param("myage") Integer age)
	
	```

	``` xaml
	<!--mapper文件-->
	<select>
	     select * from student where name=#{myname} or age=#{myage}
	</select>
	```

2. 使用java对象

	语法：`#{属性名}`

	``` java
	//对象类
	public class QueryParam {
	    private String paramName;
	    private Integer paramAge;
	    
	    public QueryParam(String paramName,Integer paramAge){
	        this.paramName=paramName;
	        this.paramAge=paramAge;
	    }
	}
	
	//接口
	public List<Student> selectMultiObject(QueryParam param);
	```

	``` xaml
	<!--mapper文件-->
	<select id="selectMultiObject" resultType="com.ming.domain.Student">
	        select id,name,email,age from student where name=#{paramName} or age=#{paramAge}
	</select>
	```

**4）其他（不建议使用）**

多个简单类型的参数，可以按位置传值，如\#{arg0}，#{arg1}

多个参数也可以使用Map存放多个值

### \#和$的区别

1. \#：==占位符==，告诉 mybatis ==使用实际的参数值代替==。==并使用PrepareStatement对象执行sql语句==, #{…}代替sql 语句的 `?` 更安全迅速，是首选做法
2. \$ ：==字符串替换==，告诉 mybatis ==使用\$包含的“字符串”替换所在位置==。使用==Statement==把sql语句和${}的内容连接起来。主要用在替换表名、列名、不同列排序等操作
3. 区别
	- \#使用`?`在sql语句中做占位符， 使用PreparedStatement执行sql，效率高
	- ==\#能够避免sql注入，更安全==。
	- $不使用占位符，是字符串连接方式，使用Statement对象执行sql，效率低
	- $有sql注入的风险，缺乏安全性。
	- $:可以替换表名或者列名

``` sql
select id,name, email,age from student where id=#{studentId}
# 的结果： select id,name, email,age from student where id=? 

select id,name, email,age from student where id=${studentId}
$ 的结果：select id,name, email,age from student where id=1001

String sql="select id,name, email,age from student where id=" + "1001";
使用的Statement对象执行sql， 效率比PreparedStatement低。

$:可以替换表名或者列名， 你能确定数据是安全的。可以使用$
```



## 封装mybatis输出结果

输出结果：==mybatis执行了sql语句，得到java对象==

### resultType

==结果类型==，指sql语句执行完毕后， ==数据转为的java对象，这个java对象的类型是任意的==

1. resultType的值

	- 类型的==全限定名称==
	- 类型的==别名==，例如 java.lang.Integer别名是int

2. 处理方式

	- mybatis执行sql语句， 然后mybatis调用类的==无参数构造方法==，创建对象
	- mybatis把ResultSet指定列值赋给同名的属性

	```  xaml
	<select id="selectMultiPosition" resultType="com.bjpowernode.domain.Student">
	      select id,name, email,age from student
	</select>
	```

	``` java
	//对等的JDBC
	ResultSet rs = executeQuery(" select id,name, email,age from student" )
	 while(rs.next()){
	    Student  student = new Student();
	    student.setId(rs.getInt("id"));
	    student.setName(rs.getString("name"))
	}
	```

3. 定义自定义类型的别名

	1. 在mybatis主配置文件中定义，使<typeAlias>定义别名
	2. 可以在resultType中使用自定义别名

### resultMap

==结果映射， 指定列名和java对象的属性对应关系==

1. ==当列名和属性名不一样时，一定要使用resultMap==

2. ==resultType和resultMap不能一起用，二选一==

3. ==实体类属性名和列名不同的处理方式==

	- 使用==列别名==和resultType：即用`as`定义别名

		``` xaml
		<!--例如：实体表列名为id,java类属性名为stuid-->
		<select id="selectDiffColProperty" resultType="com.ming.domain.MyStudent">
			select id as stuid,name as stuname, email as stuemail,age as stuage from student
		</select>
		```

	- 使用resultMap

		``` xaml
		<!--
		使用resultMap
			1.先定义resultMap
			2.在select标签，使用resultMap来引用上面定义的
		-->
		
		<!--
		定义resultMap
			id：自定义名称，表示你定义的这个resultMap
			type：java类型的全限定名称
		-->
		
		<resultMap id="MystudentMap" type="com.ming.domain.MyStudent">
		    <!--列名和属性的关系-->
		    <!--主键列，使用id标签
		        column：实体表列名
		        property：java类型的属性名
		    -->
		    <id column="id" property="stuid"/>
		    <!--非主键列，使用result-->
		    <result column="name" property="stuname"/>
		    <result column="email" property="stuemail"/>
		    <result column="age" property="stuage"/>
		</resultMap>
		
		<select id="selectAllMyStudents" resultMap="MystudentMap">
			select id,name,email,age from student
		</select>
		```

### 模糊 like

模糊查询的实现有两种方式

1. ==java 代码中给查询数据加上`%`（推荐）==

	```java
	//mapper
	select id,name,email,age from student where name like #{name}
	//java代码
	String name = "%李%";
	List<Student> studentList = dao.selectLikeOne(name);
	```

2. 在 mapper 文件 sql 语句的条件位置加上"%"



## 动态sql

动态sql: ==sql的内容是变化的，可以根据条件获取到不同的sql语句==（主要是where部分发生变化）

动态sql的实现：使用的是mybatis提供的标签，\<if> \<where> \<foreach>

### \<if>

是用来==判断条件==的（不要单独使用if，if要结合where使用）

``` xaml
<if test="判断java对象的属性值">
	部分sql语句
</if>
```

### \<where>

==是用来包含多个\<if>的，当多个if有一个成立的， \<where>会自动增加一个where关键字， 并去掉 if中多余的 and ，or等。==

### \<foreach>

==循环java中的数组，list集合==的，主要用在sql的in语句中

```
学生id是 1001,1002,1003的三个学生

select * from student where id in (1001,1002,1003)
		
public List<Student> selectFor(List<Integer> idlist)
List<Integer> list = new ...
list.add(1001);
list.add(1002);
list.add(1003);
dao.selectFor(list)
```

- `collection`:==表示接口中的方法参数的类型==， 如果是==数组使用array , 如果是list集合使用list==
- `item`:自定义的，==表示数组和集合成员的变量==
- open:循环开始时的字符
- close:循环结束时的字符
- separator:集合成员之间的分隔符



### sql代码片段

就是复用一些语法

1. 先定义`<sql id="自定义名称唯一"> sql语句， 表名，字段等 </sql>`
2. 再使用`<include refid="id的值" />`

### Demo

``` xaml
<!--定义sql片段-->
<sql id="studentSql">
    select id,name,email,age from student
</sql>

<select id="selectStudentWhere" resultType="com.ming.domain.Student">
    select <include refid="studentSql2"/> from student
    <where>
        <if test="name != null and name !='' ">
            name=#{name}
        </if>
        <if test="age > 0">
            or age > #{age}
        </if>
    </where>
</select>
<!--foreach使用1，List<Integer>-->
<select id="selectForeachOne" resultType="com.ming.domain.Student">
    select * from student where id in
    <foreach collection="list" item="myid" open="(" close=")" separator=",">
        #{myid}
    </foreach>
</select>
<!--foreach使用2，List<Student>-->
<select id="selectForeachTwo" resultType="com.ming.domain.Student">
    select * from student where id in
    <foreach collection="list" item="stu" open="(" close=")" separator=",">
        #{stu.id}
    </foreach>
</select>
```



## 数据库的属性配置文件

1. 数据库的属性配置文件

	把数据库连接信息放到一个单独的文件中。 和mybatis主配置文件分开。目的是便于修改，保存，处理多个数据库的信息。

	- 在resources目录中定义一个属性配置文件， xxxx.properties ,例如 jdbc.properties

		在属性配置文件中， 定义数据，格式是 key=value 
		key： 一般使用 . 做多级目录

		``` properties
		#例如：jdbc.mysql.driver，jdbc.driver，mydriver
		jdbc.driver=com.mysql.jdbc.Driver
		jdbc.url=jdbc:mysql//.....
		jdbc.username=root
		jdbc.password=123456
		```

	- 在mybatis的主配置文件，使用\<property>指定文件的位置，在需要使用值的地方格式为${key}

2. mapper文件，使用package指定路径

	- 第一种方式：==指定多个mapper文件，使用多行mapper标签语句==

		``` xaml
		<mapper resource="com/ming/dao/StudentDao1.xml"/>
		<mapper resource="com/ming/dao/StudentDao2.xml"/>
		```

	- 第二种方式：==使用包名指定多个mapper文件==

		``` xaml
		<!--
		name：xml文件(mapper文件)所在的包名，这个包中所有xml文件一次都能加载给mybatis
		使用package的要求：
			1.mapper文件名称需要和接口名称一样，区分大小写
			2.mapper文件和dao接口需要在同一目录
		-->
		<package name="com.ming.dao"/>
		```



## 缓存机制

一级缓存是默认使用的，二级缓存需要手动开启

==一级缓存的作用域是一个sqlsession内==；每当与数据库建立一次连接时，就会创建一个SqlSession对象
==二级缓存作用域是针对namesapce级别的缓存==（Mapper级别或者叫做表级别的缓存），设计的主要目的是实现sqlSession之间的缓存共享

1. 一级缓存
	- 指的是mybatis中sqlSession对象的缓存，当我们执行查询以后，查询的结果会同时存入sqlSession中，再次查询的时候，先去sqlSession中查询，有的话直接拿出，当sqlSession消失时，mybatis的一级缓存也就消失了，当调用sqlSession的修改、添加、删除、commit()、close()等方法时，会清空一级缓存。
	- 在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SelSession第一次查询后，MyBatis会将其放在缓存中，以后再查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，SqlSession都会取出当前缓存的数据，而不会再次发送SQL到数据库。
2. 二级缓存
	- 指的是mybatis中的sqlSessionFactory对象的缓存，由同一个sqlSessionFactory对象创建的sqlSession共享其缓存，但是其中缓存的是数据而不是对象。当命中二级缓存时，通过存储的数据构造成对象返回。查询数据的时候，查询的流程是二级缓存 > 一级缓存 > 数据库
	- 如果开启了二级缓存，sqlSession进行close()后，才会把sqlSession一级缓存中的数据添加到二级缓存中，为了将缓存数据取出执行反序列化，还需要将要缓存的pojo实现Serializable接口，因为二级缓存数据存储介质多种多样，不一定只存在内存中，也可能存在硬盘中
3. 当与数据库建立一次连接，就会创建一个SqlSession对象，默认是DefaultSqlSession这个实现，这个对象给用户提供了操作数据库的各种方法，与此同时，也会创建一个Executor执行器，缓存信息就是维护在Executor中，Executor有一个抽象子类BaseExecutor，这个类中有个属性PerpetualCache类，这个类就是真正用于维护一级缓存的地方

4. Demo

	- 同一个Mapper代理对象执行条件相同的同一个查询sql

		``` java
		SqlSession sqlSession = getSqlSessionFactory().openSession();
		GoodsDao goodsMapper = sqlSession.getMapper(GoodsDao.class);
		goodsMapper.selectGoodsById("1");
		goodsMapper.selectGoodsById("1");
		//结果：只向数据库进行了一次查询，第二次用了缓存
		```

	- 同一个Mapper代理对象执行条件不同的同一个查询sql

		``` java
		public void selectGoodsTest(){
		    SqlSession sqlSession = getSqlSessionFactory().openSession();
		    GoodsDao goodsMapper = sqlSession.getMapper(GoodsDao.class);
		    goodsMapper.selectGoodsById("1");
		    goodsMapper.selectGoodsById("2");
		}
		//结果：因为查询条件不同，所以是两个不同的statement，生成了两个不同key，缓存中是没有的
		```

	- 针对==同一个Mapper接口生成两个代理对象==，然后执行查询条件完全相同的同一条sql （这算是用的同一个sqlSession）

		``` java
		public void selectGoodsTest(){
		        SqlSession sqlSession = getSqlSessionFactory().openSession(); //同一个sqlSession
		        GoodsDao goodsMapper = sqlSession.getMapper(GoodsDao.class);
		        GoodsDao goodsMapper2 = sqlSession.getMapper(GoodsDao.class);
		        goodsMapper.selectGoodsById("1");
		        goodsMapper2.selectGoodsById("1");
		    }
		//结果：同一个SqlSession会话，查询条件完全相同的同一条sql。所以，第二次查询是从缓存中查找的
		```

	- 开启两个SqlSession，在SqlSession1中查询操作，在SqlSession2中执行修改操作，那么SqlSession1中的一级缓存是否仍然有效？

		``` java
		public void selectGoodsTest(){
		    SqlSession sqlSession = getSqlSessionFactory().openSession();
		    GoodsDao goodsMapper = sqlSession.getMapper(GoodsDao.class);
		    SqlSession sqlSession2 = getSqlSessionFactory().openSession();
		    GoodsDao goodsMapper2 = sqlSession2.getMapper(GoodsDao.class);
		    Goods goods = new Goods();
		    goods.setId("1");
		    goods.setName("篮球");
		    Goods goods1 = goodsMapper.selectGoodsById("1");
		    System.out.println("name="+goods1.getName());
		    System.out.println("******************************************************");
		    goodsMapper2.updateGoodsById(goods);
		    Goods goodsResult = goodsMapper.selectGoodsById("1");
		    System.out.println("******************************************************");
		    System.out.println("name="+goodsResult.getName());
		}
		//结果：在SqlSession2中对id=1的数据做了修改，但是在SqlSession1中的最后一次查询中，仍然是从一级缓存中取得数据，说明了一级缓存只在SqlSession内部共享，SqlSession对数据库的修改操作不影响其它SqlSession中的一级缓存。
		```

		

