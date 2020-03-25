

# 					MyBatis(半自动 轻量级 持久层框架)

概述

​						

![1582618901690](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582618901690.png)

sql与java编码分离 ;sql是开发人员控制

只需要掌握sql即可



mybatis以简化JDBC操作  实现数据的持久层

​													ORM:Object  Relational  Maping

​														person对象-->ponson数据库表

​													ORM:概念，Mybatis是ORM的一个实现/Hibernate



### 										课时1    Mybatis配置以及入门示例

1.）导入所需的jar包 mvn

```maven
 <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
```

.2)Mapper SQL映射的头部

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.hbz.dao.IPersonDao">
```



.3)mapper SQL映射配置----编写实体对应数据库

```xml
<mapper namespace="org.hbz.dao.IPersonDao">
        <select id="selectPersonById" parameterType="Integer" resultType="org.hbz.entity.Person">
            select * from person where id=#{id}
        </select>
</mapper>
```



```java
package org.hbz.entity;

public class Person {
    private Integer id;
    private String name;
    private Integer age;
    private String sex;

    public Person() {

    }

    public Person(Integer id, String name, Integer age, String sex) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.sex = sex;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                '}';
    }
}

```



.4) mybatis的主配置文件头

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
```



.5)mybatis的主配置文件体

```xml
<configuration>
    <environments default="mysql">
        <environment id="mysql">
<!--                配置事务-->
            <transactionManager type="JDBC">
            </transactionManager>
<!--            配置数据库连接信息-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
<!--加载sql映射文件-->
    <mappers>
<!--        注意如果是xml文件 格式是../../..-->
        <mapper resource="org/hbz/dao/personMapper.xml"></mapper>
    </mappers>
</configuration>
```

.6)log4j配置文件如下

```proper
### 设置###
log4j.rootLogger = debug,stdout,D,E

### 输出信息到控制抬 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

### 输出DEBUG 级别以上的日志到=E://logs/error.log ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = E://logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG 
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

### 输出ERROR 级别以上的日志到=E://logs/error.log ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =E://logs/error.log 
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR 
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```



.7)dao层

```java
/**
     * 按照id查询一个学生
     * @param id
     * @return
     */
    Student selectStudentById(Integer id);
```



.8)测试类

```java
package org.hbz.test;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.hbz.dao.IPersonDao;
import org.hbz.entity.Person;

import java.io.IOException;
import java.io.Reader;

/**
 * 测试类
 */
public class MybatisTest {
    public static void main(String [] args) throws IOException {
        //加载mybatis配置文件(为了访问数据库)
       Reader reader= Resources.getResourceAsReader("config.xml");
       //创建工厂
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        //打开一次会话
        SqlSession session=sessionFactory.openSession();
        //获取代理对象
        IPersonDao iPersonDao = (IPersonDao)session.getMapper(IPersonDao.class);
        //执行方法
        Person person = iPersonDao.selectPersonById(1);
        System.out.println(person.getName());

        //关闭流
        reader.close();
        //关闭会话
        session.close();
    }

}

```



.8)mybatis通过namespace加id定位出唯一的sql





### 										课时2   Mybatis 动态代理方式实现增删改查(mybatis接口方式开发)以及配置分析

官方推荐 

另外一种就是statement开发



原则:约定优于配置

.1)分析主配置文件

```xml
<configuration>
    <environments default="mysql">
        <environment id="mysql">
<!--                配置事务-->
            <transactionManager type="JDBC">
            </transactionManager>
<!--            配置数据库连接信息-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
<!--加载sql映射文件-->
    <mappers>
<!--        注意如果是xml文件 格式是../../..-->
        <mapper resource="org/hbz/dao/personMapper.xml"></mapper>
    </mappers>
</configuration>
```

分析如下:

1.environments标签和environment关系

default:代表在哪个环境上运行项目

id:代表的是环境标识   每一个id都代表一个环境

上诉的总结:通过default 指定的id值产生映射关系    可以随意的更换运行的环境  列如第一个环境是本机的数据库  第二个是真正的发布项目的数据库 

也可以通过java代码来指定运行的环境

第二值就是更改默认运行的环境

```java
SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader,"mysql");
```



2.dataSource标签

数据源类型:

​				POOLED:使用数据库连接池

​				UNPOOLED:传统的JDBC模式(每次访问数据库 均需要打开 关闭等数据库操作 但是打开关闭数据库是比较消耗性能的)

​				JNDI:从tomcat中获取一个内置的数据库连接池 (数据连接池--数据源)



3.transactionManager标签

提交事务方式:

​				JDBC:利用JDBC方式处理事务 (commit rollback close)

​				MANAGED:将事务交由 其他组件去托管  (spring,jobss),默认 会关闭连接  如果不想关闭使用下面代码

```xml
<property name="closeCConnection" value="false"/>配置在事务标签下
```



.2)分析sql映射文件

```xml
<mapper namespace="org.hbz.dao.IStudentDao">
    <select id="selectStudentById" resultType="org.hbz.entity.Student" parameterType="Integer">
        select * from student where stuno=#{stuno}
    </select>
    
    <select id="selectAllStudent" resultType="org.hbz.entity.Student">
        select * from student
    </select>

    <insert id="addStudent" parameterType="org.hbz.entity.Student">
        insert into student(stuName,stuAge,graName) values(#{stuName},#{stuAge},#{graName})
    </insert>

    <delete id="deleteStudent" parameterType="Integer">
        delete from student where id=#(id)
    </delete>

    <update id="updateStudent" parameterType="org.hbz.entity.Student">
        update into student set stuName=#{stuName},stuAge=#{stuAge},graName=#{graName} where id=#{id}
    
</mapper>
```

1.mybatis约定:resultType和parameter只能有一个值

2.select :标签代表查询操作

3.namespace:映射的接口路径

4.id:代表接口中的方法名称

5.parameterType:

​			5.1 代表接口中的方法参数类型

​			5.2 如果parameterType的参数是8个基本类型(基本类型+String)  占位符里面的值可以随便填写#{xxx}

​			5.3 如果是对象类型 必须写的是对象的属性名称 #{属性名}

6.resultMap:

​			6.1 代表接口方法的返回值

​			6.2 如果返回值是一个 对象(如Student) ，则无论是返回一个还是多个，再resultType都写成org.hbz.entity.Student

.7)dao层

```java
public interface IStudentDao {
    /**
     * 按照id查询一个学生
     * @param id
     * @return
     */
    Student selectStudentById(Integer id);

    /**
     * 查询全部学生
     * @return
     */
    List<Student> selectAllStudent();

    /**
     * 增加一个学生
     * @param student
     * @return
     */
    Integer addStudent(Student student);

    /**
     * 根据id删除学生
     * @param id
     * @return
     */
    Integer deleteStudent(Integer id);

    /**
     * 按照id更新学生
     * @param student
     * @return
     */
    Integer updateStudent(Student student);
}

```

1.如果调用了dao层的方法 则会通过接口名称来查找配置的文件namespace 

 并且通过方法名值映射id得到sql语句

2.习惯:既然dao层和mapper.xml方法一起  而在idea中resources的包和java源码的包结构一样即可 会自动放在一起

.8)测试类

```java
  //查询一个学生
    public static void selectOneStudent() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Student student = iStudentDao.selectStudentById(1);
        System.out.println(student);
        session.close();
        reader.close();
    }
    //查询全部学生
    public static void selectAll() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        List<Student> stus = iStudentDao.selectAllStudent();
        System.out.println(stus);
        session.close();
        reader.close();
    }

    //增加一个学生
    public static void addStu() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Student stu=new Student();

        stu.setStuName("王五");
        stu.setStuAge(20);
        stu.setGraName("S1");

        Integer count = iStudentDao.addStudent(stu);
        session.commit();
        System.out.println(count);
        session.close();
        reader.close();
    }

    //删除一个学生
    public static void deleteStu() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);

        Integer count = iStudentDao.deleteStudent(2);
        session.commit();
        System.out.println(count);
        session.close();
        reader.close();
    }

    //更新学生
    public static void updateStudent() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Student stu=new Student();
        stu.setStuNo(3);
        stu.setStuName("王子");
        stu.setStuAge(20);
        stu.setGraName("S2");

        Integer count = iStudentDao.updateStudent(stu);
        session.commit();
        System.out.println(count);
        session.close();
        reader.close();
    }
```

1.通过getMapper(接口.class)方法反射出类来得到这个接口

​	之后调用接口中的方法即可



### 		课时3:属性文件丶全局参数丶别名丶类型转换器丶resultMap

.1)如何配置连接数据库的参数(主配置文件中)

​	1.首先创建一个properties的文件 把连接的参数写入

```proper
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/ssm
username=root
password=root
```

​	2.在主配置文件中引入该配置文件

```xml
<!--    引入-->
    <properties resource="mysql.properties"></properties>
```

​	3.通过跟el表达式的形式填入value值

```xml

                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
```



.2)如何设置全局参数

1.下面有一张全局参数的键和值

![1582686418004](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582686418004.png)

![1582686445041](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582686445041.png)

![1582686465225](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582686465225.png)

![1582686502443](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582686502443.png)

![1582686521440](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582686521440.png)

2.在主配置文件中配置

```xml
 <settings>
        <setting name="cacheEnabled" value="false"/>
    </settings>
```

​			name代表要执行查找键   value 代表键的值  



.3)如何配置别名

​	1.配置单个别名 （在主配置文件中）

```xml
<typeAliases>
        <typeAlias type="org.hbz.entity.Student" alias="student"></typeAlias>
    </typeAliases>
```

​	如果设置了别名  只要要用到这个全限定类名了  都可以使用别名  并且不区分大小写

​			type:代表全限定类名							alias:代表别名

2.配置多个别名(在主配置中)

```xml
    <typeAliases> 
<package name="org.hbz.entity"/>
    </typeAliases>
```

​	如果设置了别名  只要要用到这个全限定类名了  都可以使用别名  并且不区分大小写

​				name:代表全限定包名   而别名就是类本身

3.除了自定义别名mybatis内置的一些别名如下

![1582688016568](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582688016568.png)



.4)类型处理器(类型转换器)

1.mybatis自带的一些类型处理器

​		![1582688230321](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582688230321.png)

![1582688252807](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582688252807.png)

2.自定义mybatis类型处理器

​					java-数据库(jdbc类型)

实例:

实体类Student     boolean  stuSex    true:男    false :女

数据库 student    int    stuSex			1:男		0::女

自定义类型转换器(boolean-int) 步骤:

​	(1) 创建转换器

​				需要实现TypeHandler接口

​				通过阅读源码发现BaseTypeHandler是实现类  因此实现类型转换器有两种选择

​				i:实现TypeHandler

​				ii:继承BaseTypeHandler

```java
package org.hbz.converter;


import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class BooleanAndInt extends BaseTypeHandler<Boolean> {

    //set:java-->数据库

    /**
     *
     * @param preparedStatement PreparedStatement对象
     * @param i PreparedStatement对象操作参数的位置
     * @param o java值
     * @param jdbcType jdbc操作数据库的值
     * @throws SQLException
     */
    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Boolean o, JdbcType jdbcType) throws SQLException {
                if (o){//true编程1
                    preparedStatement.setInt(i,1);
                }else{//false就是0
                    preparedStatement.setInt(i,0);
                }
    }
    //get:数据库-->java

    /**
     *
     * @param resultSet 数据集
     * @param s 数据库列名
     * @return 返回转换后的值
     * @throws SQLException
     */
    @Override
    public Boolean getNullableResult(ResultSet resultSet, String s) throws SQLException {
        int bool=resultSet.getInt(s);
        return (bool==1?true:false);
    }
    //get:数据库-->java

    /**
     *
     * @param resultSet 数据集
     * @param i 下标
     * @return 转换后的数据
     * @throws SQLException
     */
    @Override
    public Boolean getNullableResult(ResultSet resultSet, int i) throws SQLException {
        int bool=resultSet.getInt(i);
        return (bool==1?true:false);
    }
    //get:数据库-->java

    /**
     *
     * @param callableStatement 存储过程对象
     * @param i 下标
     * @return 转换后的值
     * @throws SQLException
     */
    @Override
    public Boolean getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        int bool=callableStatement.getInt(i);
        return (bool==1?true:false);
    }
}

```

​	(2)配置转换器(主配置文件中)

```xml
 <typeHandlers>
 <typeHandler handler="org.hbz.converter.BooleanAndInt" javaType="boolean" jdbcType="Integer"></typeHandler>
    </typeHandlers>
```

​	handler:代表映射转换类型的类			javaType:java的类型    jdbcType:数据库的类型

 (3)配置sql映射文件 之查询

```xml
 <select id="selectStudentConverter" parameterType="Integer" resultMap="selectConverter">
        select * from student where stuno=#{stuno}
    </select>
```

​	1.resultMap和resultType的区别

​			情况1:如果类中的属性类型和表中的字段类型能够合理的被识别(String-varchar) ,则使用resultType,否则使用resultMap

​			情况2:如果类中的属名称和表中的字段名称能够合理的被识别(stuno-stuno) ,则使用resultType,否则使用resultMap

​	2.配置resultMap(在sql映射文件中配置)

```xml
 <resultMap id="selectConverter" type="student">
<!--        分为主键和非主键-->
        <id property="stuNo" column="stuno"></id>
        <result property="stuName" column="stuname"></result>
        <result property="stuAge" column="stuage"></result>
        <result property="graName" column="graname"></result>
        <result property="stuSex" column="stusex" javaType="boolean" jdbcType="INTEGER"></result>
    </resultMap>
```

​			  第一个id:代表通过这个id值可以找到这个map

​			  第二个id:代表主键

​			 result:代表非主键

​			property:代表java中的属性名

​			column:代表数据库的字段名

​			javaType:java属性的类型

​			jdbcType:数据库字段的类型 并且只能是大写

3.测试查询操作

![1582694028244](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582694028244.png)



(4)配置sql映射文件 之增改

```xml
<!--    含有类型转换的操作-->
    <insert id="addStudenttConverte" parameterType="org.hbz.entity.Student">
        insert into student(stuName,stuAge,graName,stuSex) values(#{stuName},#{stuAge},#{graName},#{stuSex ,javaType=boolean,jdbcType=INTEGER })
    </insert>
```

1.在需要转换的地方 #{实体的属性名称,javaType=实体的属性类型,jdbcType数据库的字段的类型}

2.测试结果:![1582696699394](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582696699394.png)

3.#{xx}中存放的属性值  严格区分大小写



### 课时4:l两种取值符号以及ParameterType为简单，对象,嵌套对象类型

.1)输入参数:ParameterType

1.类型为简单类型(8个基本类型+String)

​		1.1${} 和 #{}的区别

​		(1)#{任意值}

​			${value},其中的标识符只能是value 

​		(2)#{}自动给String类加上‘ ’ (自动类型转换)

​		    ${}原样输出,但是适用于 动态排序(动态字段)

​		    动态排序的实例:

```xml
<!--    动态排序-->
    <select id="selectStudentByOrderBy" parameterType="string" resultType="student">
        select * from student order by ${value} desc
    </select>
```

​		如果使用#{}会自动的添加‘ ‘号 就不符合数据库的语句

​			(3) #{}可以防止sql注入

​				${}不防止		

2.类型是对象或者是嵌套对象类型

1.对象类型

```xml
<!--    通过家庭地址或者学校地址插叙学生-->
    <select id="selectStudentByhomeOrSchool" parameterType="Address" resultType="student">
select stuno,stuName from student where homeaddress=#{homeAddress} or schooladdress='${schoolAddress}'
    </select>
```

2.嵌套对象类型

```xml
 <!--    通过家庭地址或者学校地址插叙学生-->
    <select id="selectStudentByhomeOrSchool" parameterType="student" resultType="student">
select stuno,stuName from student where homeaddress=#{address.homeAddress} or schooladdress='${address.schoolAddress}'
    </select>
```

​	(1)#{}${}两个都支持级联  有点像表达式 



### 课时5 入参为HashMap,以及mybatis调用存储过程CRUD

.1) 入参为HashMap

1.创建sql语句

```xml
<!--    通过hashMap入参来通过家庭地址或者学校地址插叙学生-->
    <select id="selectStudentByhomeOrSchoolMap" resultType="Student" parameterType="Map">
        select stuno,stuName from student where homeaddress=#{homeAddress} or schooladdress=#{schoolAddress}
    </select>
```

2.编写接口

```java
    /**
     *
     * @param map
     * @return
     */
    List<Student> selectStudentByhomeOrSchoolMap(Map<String,Object> map);
```

3.测试类

```java
                                                                                    
  public static void selectAllOrderByhomeOrSchoolMap() throws IOException {         
      Reader reader = Resources.getResourceAsReader("config.xml");                  
      SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
      SqlSession session=sessionFactory.openSession();                              
      IStudentDao iStudentDao=session.getMapper(IStudentDao.class);                 
      Map<String,Object> map=new HashMap<>();                                       
      map.put("homeAddress","sy");                                                  
      map.put("schoolAddress","sy");                                                
      List<Student> stus = iStudentDao.selectStudentByhomeOrSchoolMap(map);         
      System.out.println(stus);                                                     
      session.close();                                                              
      reader.close();                                                               
  }                                                                                 
```

​		(1)用map中的key的值  匹配 占位符#{stuAge} ,如果匹配成功,就用map的value替换占位符



.2)mybatis如何调用存储过程

1.示例一:查询某个的学生总数    (可能出现错误)

​	输入:年级

​	输出:该年级的总数

​	(1)在数据库创建存储过程

```sql
DELIMITER //
 CREATE PROCEDURE queryByGradeWithProcedure
 (IN gName VARCHAR(30),OUT scount INT)
 BEGIN 
 SELECT COUNT(1) INTO scount  FROM student WHERE graName=gName;
 END //
```

   (2) 如何调用数据库里面的存储过程呢？

```xml
<!--    通过调用存储过程 实现查询 ,statementType="CALLABLE"
           存储过程的输入参数一般使用HashMap来传递
-->
     <select id="queryCOuntByGraNameWithProcedure" statementType="CALLABLE" parameterType="HashMap">
        {call queryByGradeWithProcedure( #{gName,mode=IN}, #{scount,mode=OUT,jdbcType=INTEGER})}
    </select>
```

其中 通过statementType="CALLABLE" 设置SQL的执行方式是存储过程

存储过程的输入参数是gName 需要通过HashMap来指定

通过HashMap方法的put方法传入输出参数的值,通过HashMap的get方法来获取输出参数的值

mode:来指定在数据库是入参还是输出参数

要注意jar的问题；![1582952901182](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1582952901182.png)

存储过程  无论输入参数是什么值,语法上都需要 用map来传值

 (3) 测试类

```java
   public static void selectOneStudentByGraName() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Map <String,Object> map=new HashMap<>();
        map.put("gName","S2");
        iStudentDao.queryCOuntByGraNameWithProcedure(map);
        //获取存储过程的输出参数
        Object count=map.get("scount");
        System.out.println(count);
        session.close();
        reader.close();
    }

```

2.示例二:根据学号删除学生

输入:学号

(1)创建存储过程

```sql
DELIMITER //
CREATE PROCEDURE deleteStudentByStuno
(IN NO INT )
BEGIN
    DELETE FROM student WHERE stuno=NO;
END //
```

(2) 调用存储过程

```xml
<!--    通过调用存储过程  按照id值删除学号-->
    <delete id="deleteStudentByno" statementType="CALLABLE" parameterType="HashMap">
         {call deleteStudentByStuno( #{no,mode=IN})}
    </delete>
```

只要是<transactionManager type="JDBC"></transactionManager>这个事务方式 只要是删除添加更新  都需要手工的提交事务 除非在打开会话时打开自动提交

(3)测试类

```java
  public static void deleteStudentByno() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Map <String,Object> map=new HashMap<>();
        map.put("no",3);
        iStudentDao.deleteStudentByno(map);
        session.commit();
        session.close();
        reader.close();
    }

```

### 

### 课时6 输出参数为简单类型、对象类型、HashMap及resultMap使用

.1)resultType

(1)简单类型(8个基本类型加String)

(2)输出类型为实体对象类型

(3) 输出参数为实体对象类型的集合 :虽然输出类型为集合,但是resultType依然写  集合的元素类型(resultType="student")

(4)输出参数为HashMap

```xml
<!--    别名作为Map的key-->
    <select id="queryStudentOutHashMap" resultType="HashMap" parameterType="int">
        select stuName "name",stuAge "age" from student where stuno=#{xx}
    </select>
```



​		--HashMap本身就是一个集合,可以存放多个元素,

​		但是根据提示发现   返回值为HashMap的时候,查询的结果只能是一个学生(no,name);

​	结论---》:一个HashMap对应一个学生的多个元素(多个属性) （一个map一个学生）

如果非要改成输出为多个学生  改进如下:

```xml
<!--    查询返回值为集合map-->
    <select id="queryAllStudentOutHashMap" resultType="HashMap">
        select stuName "name",stuAge "age" from student
    </select>
```

```java
 /**
     * 查询所有学生并且返回值为Map集合
     * @return
     */
    List<Map<String ,Object>> queryAllStudentOutHashMap();
```

```java
   public static void queryAllStudentOutHashMap() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        List<Map<String, Object>> map = iStudentDao.queryAllStudentOutHashMap();
        System.out.println(map);
        session.close();
        reader.close();
    }
```

.2)resultMap

(1)实体类的属性表中的字段:类型,名字不同时(stuno,id)

注意:当属姓名和字段名不一致时,除了resultMap以外，还可以使用resultType+HashMap

​	1.resultMap处理属性和字段不一致		

```xml

<!--    使用resultMap返回-->
    <select id="queryAllStudentByidMap" resultMap="queryAllStudentByidMaps" parameterType="int">
        select id,name from student where id=#{xx}
    </select>

    <resultMap id="queryAllStudentByidMaps" type="student">
        <id property="stuNo" column="id"></id>
        <result property="stuName" column="name"></result>
    </resultMap>
```

 2.resultType处理属性和字段名不一致

```xml
<!--使用resultType返回-->
    <select id="queryAllStudentByidWithType" resultType="student" parameterType="int">
        select id "stuNo",name "stuName" from student where id=#{xx}
    </select>
```

select 表中的字段名 “类中的属性名”  from ...来指定属性名和字段名的关系

注意:如果10个字段 ,但发现 某一个属性结果始终为默认值(0,0.0,null) ,则可能是表的字段和类的属性 名字写错了



### 课时7:动语态SQL、foreach、输入参数为集合属性、集合、数组、动态数组

.1)if和where标签的使用

1.第一种方式来来实现动态的sql

```xml
<!--    是用if标签实现动态sql语句 1-->
    <select id="queryAllStudentByNameAndAgeSql" resultType="student" parameterType="student">
        select stuno,stuname,stusex from student where 1=1
        <if test="stuSex!=null and stuSex!=''">
                and stusex=#{stuSex}
        </if>
        <if test="stuName!=null and stuName!=''">
             and stuname=#{stuName}
        </if>
```

2.第二种方式来实现动态sql方式

```xml
  <select id="queryAllStudentByNameAndAgeSql" resultType="student" parameterType="student">
        select stuno,stuname,stusex from student
        <where>
        <if test="stuSex!=null and stuSex!=''">
            and stusex=#{stuSex}
        </if>
        <if test="stuName!=null and stuName!=''">
            and stuname=#{stuName}
        </if>
        </where>
    </select>
```

   where:标签可以自动的处理第一个and    但是不会处理其他的and     推荐使用第二种方式 

.2)foreach标签的使用

(1)  可以迭代的类型 :数组,对象数组,集合,属性(Grade)类:List<Integer> ids

如果是类中的集合以下代码就是实例

```xml
<!--    通过年纪实体里里面的所有学号来查询学生-->
    <select id="querystuNosInGrade" parameterType="grade" resultType="student">
        select * from student
        <where>
            <if test="nos!=null and nos.size>0">
                <foreach collection="nos" open="and stuno in(" close=")" item="no" separator=",">
                    #{no}
                </foreach>
            </if>
        </where>
    </select>
```

  (2)collection:代表的是要浏览哪个集合

  (3)open 在循环值的前半部分的条件sql语句

  (4)close 在循环值的后半部分的条件sql语句

 (5) item 临时变量  列如一堆学生   这个就代表其中一个

 (6) separator 按照什么样的字符分割数据   列如:-,:, ,

 (7)取值方式:#{临时变量}



 (8如果是简单数组类型:无论编写代码时,传递的是什么参数名,在mapper.xml中  必须使用array来代替该数组

```xml
 <!--    通过年纪实体里里面的所有学号来查询学生-->
    <select id="querystuNosInArray" parameterType="int[]" resultType="student">
        select * from student
        <where>
            <if test="array!=null and array.length>0">
                <foreach collection="array" open="and stuno in(" close=")" item="no" separator=",">
                    #{no}
                </foreach>
            </if>
        </where>
    </select>
```

(9)如果是集合类型:无论编写代码时,传递的是什么参数名,在mapper.xml中  必须使用list来代替该集合

```xml
 <select id="querystuNosInList" parameterType="list" resultType="student">
        select * from student
        <where>
            <if test="list!=null and list.size>0">
                <foreach collection="list" open="and stuno in(" close=")" item="no" separator=",">
                    #{no}
                </foreach>
            </if>
        </where>
    </select>
```

(10)如果是对象数组类型的:第一点-----传递值的时候必须是Object[]数组 ,第二点----在取值的时候需要用

临时变量.具体的属性名称

```xml
<select id="querystuNosInsObject" parameterType="Object[]" resultType="student">
        select * from student
        <where>
            <if test="array!=null and array.length>0">
                <foreach collection="array" open="and stuno in(" close=")" item="student" separator=",">
                    #{student.stuNo}
                </foreach>
            </if>
        </where>
    </select>
```

.3)SQL片段

java中的方法

数据库中的存储过程和存储函数

mybatis中的sql片段  mybatis怎么提取公共的sql语句呢

```xml
  <sql id="foreachNo">
        <where>
            <if test="array!=null and array.length>0">
                <foreach collection="array" open="and stuno in(" close=")" item="student" separator=",">
                    #{student.stuNo}
                </foreach>
            </if>
        </where>
    </sql>
    <select id="querystuNosInsObject" parameterType="Object" resultType="student">
        select * from student
       <include refid="foreachNo"></include>
    </select>
```

使用sql标签来创建片段的部分  再用include的refid的映射导入 

如果这个片段 不在一个文件中(不在一个空间中) 需要 namespace的值.片段的id来映射导入



### 课时8:一对一关联查询和一对多查询

.1)常见的关联关系

​	(1)一对一,多对一,一对多,多对多

​	(2)但是在mybatis框架只有一对多,和一对一的形式,          (多对一,多对多的本质就是一对多的变化)

.2)一对一

​	(1)业务扩展类

​		1.创建类,继承一张表 在创建类中写另外一个表的字段  形成合并

```java

package org.hbz.entity;

public class StudentBusiness extends Student {
    private Integer cardId;
    private String cardInfo;

    public Integer getCardId() {
        return cardId;
    }

    public void setCardId(Integer cardId) {
        this.cardId = cardId;
    }

    public String getCardInfo() {
        return cardInfo;
    }

    public void setCardInfo(String cardInfo) {
        this.cardInfo = cardInfo;
    }

    @Override
    public String toString() {
        return super.toString()+"StudentBusiness{" +
                "cardId=" + cardId +
                ", cardInfo='" + cardInfo + '\'' +
                '}';
    }
}

```



​	2.创建mappersql

```xml

 <select id="queryStudentByWith00" parameterType="int" resultType="StudentBusiness">
        select s.*,c.* from student s inner join studentcart c on s.cardid=c.cartid where s.stuno=#{xx}
    </select>
```

核心:就是让一个类的属性和数据库表的字段一一对应

(2)resultMap

 	1.通过 属性成员 将两个类建立关系

```java

package org.hbz.entity;

public class Student {
    private Integer stuNo;
    private String stuName;
    private Integer stuAge;
    private String graName;
    private boolean stuSex;
    private Integer cardid;
    private Studentcart studentcart;

    public Student(){}

    public Student(Integer stuNo, String stuName, Integer stuAge, String graName, boolean stuSex, Integer cardid, Studentcart studentcart) {
        this.stuNo = stuNo;
        this.stuName = stuName;
        this.stuAge = stuAge;
        this.graName = graName;
        this.stuSex = stuSex;
        this.cardid = cardid;
        this.studentcart = studentcart;
    }

    public Integer getStuNo() {
        return stuNo;
    }

    public void setStuNo(Integer stuNo) {
        this.stuNo = stuNo;
    }

    public String getStuName() {
        return stuName;
    }

    public void setStuName(String stuName) {
        this.stuName = stuName;
    }

    public Integer getStuAge() {
        return stuAge;
    }

    public void setStuAge(Integer stuAge) {
        this.stuAge = stuAge;
    }

    public String getGraName() {
        return graName;
    }

    public void setGraName(String graName) {
        this.graName = graName;
    }

    public boolean isStuSex() {
        return stuSex;
    }

    public void setStuSex(boolean stuSex) {
        this.stuSex = stuSex;
    }

    public Integer getCardid() {
        return cardid;
    }

    public void setCardid(Integer cardid) {
        this.cardid = cardid;
    }

    public Studentcart getStudentcart() {
        return studentcart;
    }

    public void setStudentcart(Studentcart studentcart) {
        this.studentcart = studentcart;
    }

    @Override
    public String toString() {
        return "Student{" +
                "stuNo=" + stuNo +
                ", stuName='" + stuName + '\'' +
                ", stuAge=" + stuAge +
                ", graName='" + graName + '\'' +
                ", stuSex=" + stuSex +
                ", cardid=" + cardid +
                ", studentcart=" + studentcart +
                '}';
    }
}
```

2.配置xml文件如下

```xml

 <select id="queryStudentByWith00" parameterType="int" resultMap="queryStudentByWith00">
        select s.*,c.* from student s inner join studentcart c on s.cardid=c.cartid where s.stuno=#{xx}
    </select>

    <resultMap id="queryStudentByWith00" type="student">
        <id property="stuNo" column="stuno"></id>
        <result property="stuName" column="stuName"></result>
        <result property="stuAge" column="stuAge"></result>
        <result property="graName" column="graName"></result>
        <result property="stuSex" column="stusex" javaType="boolean" jdbcType="INTEGER"></result>
        <result property="cardid" column="cardid"></result>
        <association property="studentcart" javaType="Studentcart">
            <id property="cartid" column="cartid"></id>
            <result property="cardinfo" column="cardinfo"></result>
        </association>
    </resultMap>
```

​     如果是一对一关系映射使用association标签 实现

​	property填入的是属性名

​	javaType填入的是属性的类型

.3)一对多

1.通过 属性成员 将两个类建立关系

```java

package org.hbz.entity;


import java.util.List;

public class Studentclass {

  private int classid;
  private String classname;

//  设置一对多关系  一个班级对应多个学生
  private List<Student> list;

  public List<Student> getList() {
    return list;
  }

  public void setList(List<Student> list) {
    this.list = list;
  }

  public int getClassid() {
    return classid;
  }

  public void setClassid(int classid) {
    this.classid = classid;
  }


  public String getClassname() {
    return classname;
  }

  public void setClassname(String classname) {
    this.classname = classname;
  }

  @Override
  public String toString() {
    return "Studentclass{" +
            "classid=" + classid +
            ", classname='" + classname + '\'' +
            ", list=" + list +
            '}';
  }
}

```

2.配置xml如下:

```xml

<select id="queryStudentByWith01" parameterType="int" resultMap="queryStudentByWith01">
        select s.*,c.* from studentclass c inner join  student s on s.classid=c.classid where c.classid=#{xx}
    </select>
    <resultMap id="queryStudentByWith01" type="Studentclass">
        <id property="classid" column="classid"></id>
        <result property="classname" column="classname"></result>
<!--        配置成员属性一对多 如果是属性的本身类型用javaType 如果是属性的元素的类型用ofType-->
        <collection property="list" ofType="student">
            <id property="stuNo" column="stuno"></id>
            <result property="stuName" column="stuName"></result>
            <result property="stuAge" column="stuAge"></result>
            <result property="graName" column="graName"></result>
            <result property="stuSex" column="stusex" javaType="boolean" jdbcType="INTEGER"></result>
            <result property="cardid" column="cardid"></result>
        </collection>
    </resultMap>
```

如果是一对多关系 使用collection标签 实现

​			property属性名称

​			ofType属性的泛型类类型



### 课时9::MyBatis整合Log4j、延迟加载

.1)如何整合Log4j到mybatis

​	(1)导入log4j的jar包

```xml
 <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
  </dependencies>
```

  (2)在主配置文件中开启log4j

```xml
<!--    配置log4j启动-->
    <settings>
        <setting name="logImpl" value="LOG4J"></setting>
    </settings>
```

  1.如果不写value里面的值  会按照这个顺序查找SLF4J-->Apache Commons Logging-->Log4j 2---Log4j-->JDK logging

 (3)在resources中配置log4j.properties文件



.2)延迟加载:

 （1）概念

一对一,一对多,多对一,多对多

一对多:班级--学生,

如果不采用延迟加载(立即加载),查询时会将一对多都查询,班级丶班级中的所有学生。

如果想要  暂时只查询一个1的一方,而多的一方 先不查询,而是在需要查询的时候再去查询--->延迟加载

  (2)以学生和学生证一对一案例为例

​			1.首先配置主文件开启延迟加载并关闭立即加载

```xml
<!--        开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"></setting>
<!--        关闭立即加载-->
        <setting name="aggressiveLazyLoading" value="false"/>
```

​		2.配置sql映射文件的主SQL

```xml
<!--    查询全部的学生-->
    <select id="queryStudentByWithLazyLoad"  resultMap="queryStudentByWithLazyLoad">
       select * from student
    </select>

    <resultMap id="queryStudentByWithLazyLoad" type="student">
        <id property="stuNo" column="stuno"></id>
        <result property="stuName" column="stuName"></result>
        <result property="stuAge" column="stuAge"></result>
        <result property="graName" column="graName"></result>
        <result property="stuSex" column="stusex" javaType="boolean" jdbcType="INTEGER"></result>
        <result property="cardid" column="cardid"></result>
<!--        引入次要的sql 并且以什么进行关联-->
        <association property="studentcart" javaType="Studentcart" select="org.hbz.dao.IStudentcartDao.queryCardById" column="cardid">
<!--            <id property="cartid" column="cartid"></id>-->
<!--            <result property="cardinfo" column="cardinfo"></result>-->
        </association>
    </resultMap>
```

association:一对一查询时用       select:表示引入的要延迟加载的sql的空间+id名称           column:表示通过主SQL字段与要延迟加载的表建立关系

​		3.配置sql映射文件的次SQL   

```xml
<!--    根据学生证id查询学生证-->
    <select id="queryCardById" parameterType="int" resultType="Studentcart">
        select * from studentcart where cartid=#{xx}
    </select>
```

​	 4.编写dao层

```java
List<Student> queryStudentByWithLazyLoad();
```

 5.编写测试类

```java
 public static void queryStudentByWithLazyLoad() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        List<Student> stu= iStudentDao.queryStudentByWithLazyLoad();
        for (Student student:stu){
            System.out.println(student.getGraName());
            //查看学生证
            System.out.println(student.getStudentcart().getCardinfo());
        }
        session.close();
        reader.close();
    }
```

通过代码测试结果总结:如果输出studnet整个对象是会一起加载学生证的  如果只是调用属于本身student的属性是不会触发一起查看学生证的

(3)以班级和学生为例一对多延迟加载

1.首先配置主文件开启延迟加载并关闭立即加载

```xml
<!--        开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"></setting>
<!--        关闭立即加载-->
        <setting name="aggressiveLazyLoading" value="false"/>
```

2..配置sql映射文件的主SQL

```xml
<!--    主SQL查询班级所有信息-->
    <select id="queryStudentByWithLazy" resultMap="queryStudentByWithLazyMap">
        select c.* from  studentclass c
    </select>

    <resultMap id="queryStudentByWithLazyMap" type="studentclass">
        <id property="classid" column="classid"></id>
        <result property="classname" column="classname"></result>
        <collection  fetchType="lazy" property="list" ofType="student" select="org.hbz.dao.IStudentDao.queryStudentByCLassId" column="classid"/>
    </resultMap>
```

collection:一对多查询时用       select:表示引入的要延迟加载的sql的空间+id名称           column:表示通过主SQL字段与要延迟加载的表建立关系

3.配置sql映射文件的次SQL   

```xml
<!--    通过班级id查询学生   延迟加载-->
    <select id="queryStudentByCLassId" parameterType="Integer" resultType="student">
        select * from student where classid=#{xx}
    </select>
```

4.编写dao层

```java
List<Studentclass> queryStudentByWithLazy();
```

5.编写测试类

```java
 public static void queryStudentByWithLazy() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentClassDao iStudentClassDao=session.getMapper(IStudentClassDao.class);
        List<Studentclass> studentclasses = iStudentClassDao.queryStudentByWithLazy();
       for (Studentclass studentclass:studentclasses){
           System.out.println(studentclass.getClassname());
       }
        session.close();
        reader.close();
    }
```

通过代码测试结果总结:如果输出studentclass整个对象是会一起加载学生的  如果只是调用属于本身studentclass的属性是不会触发一起查看学生的

### 

### 课时10:MyBatis一级缓存、二级缓存

.1)查询缓存

 (1)一级缓存

​      1.同一个SqlSession对象,mybatis默认就开启了一级缓存,下方为示意图

![1583303863267](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1583303863267.png)

2.如果用同样的SqlSession对象查询相同的数据,则只会在第一次 查询时 向数据库发送SQL语句,并将查询的结果  放入SqlSession中(作为缓存存在);后续再次查询同样的对象时,则直接从缓存中查询该对象即可(则省略了数据库的访问)

3.一旦commit就会清空SqlSession中的缓存

4.测试代码如下

```java
 public static void selectOneStudent() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Student student = iStudentDao.selectStudentById(1);
        session.commit();
        Student student2 = iStudentDao.selectStudentById(1);
        System.out.println(student);
        System.out.println(student2);
        session.close();
        reader.close();
    }
```



(2)二级缓存

​	   1.mybatis默认关闭二级缓存  需要手动的打开 

​			1.1在主配置文件中配置

```xml
<!--        开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
```

​		    1.2在具体的mapper.xml声明开启

```xml
<!--    声明开启-->
<cache/>
```

​		  1.3异常提示 ：java.io.NotSerializableExceptio可知,mybatis的二级缓存是将对象 放入硬盘

​				序列化：内存---》硬盘

​				反序列化:硬盘---》内存

​				如何解决?

​					在实体类中实现Serializable接口  (序列化student类，以及Student的级联属性,和Student父类)

​		2.二级缓存的范围在同一个namespace中产生的接口生成的代理对象都可以共享缓存

![1583305735103](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1583305735103.png)

​	   3.如果是同一个SqlSession对象进行多次查询,则直接进入一级缓存查询;

​		  如果不是同一个SqlSession进行多次查询(但是都是一个namespace),则进入二级缓存

​		4.测试如下:

```java
   public static void selectOneStudent() throws IOException {
        Reader reader = Resources.getResourceAsReader("config.xml");
        SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(reader);
        SqlSession session=sessionFactory.openSession();
        IStudentDao iStudentDao=session.getMapper(IStudentDao.class);
        Student student = iStudentDao.selectStudentById(1);
        SqlSession session2=sessionFactory.openSession();
        IStudentDao iStudentDao2=session2.getMapper(IStudentDao.class);
        Student student2 = iStudentDao2.selectStudentById(1);
        System.out.println(student);
        System.out.println(student2);
        session.close();
        reader.close();
    }
```

​	5.细节补充在查询完一次之后需要关闭SqlSession,不然该对象不会存入二级缓存

​	存储时机就是当执行SqlSession.close()时 才会存入到二级缓存，

​	如果多个文件的namespace的值相同,通过这些mapper.xml产生的mapper对象  仍然共享二级缓存

​	

### 课时11:禁用、清理二级缓存，以及整合Ehcache缓存

​	.1)如何禁用二级缓存:

​		在具体要关闭的mapper.xml中的select标签里面填写

```xml
<select id="selectStudentById" resultType="student" parameterType="Integer" useCache="false">
        select * from student where stuno=#{stuno}
    </select>
```

.2).清理:与清理缓存一级缓存相同 

​		(1)SqlSession.close()才会记录成缓存;（执行增删改会将缓存清除了;设计的原因 是为了脏数据的产生）

​		在二级缓存中,SqlSession.commit()不能查询自身的commit()。

​		SqlSession.commit()会清理一级缓存和二级缓存;但是清理二级缓存时不能查询自身的SqlSession.commit()

​		(2)在select标签中添加flushCache="true"也可以清除缓存

.3)命中率:

​			1.zs:0%

​			2.     50%

​			3.	 66.6%

​			4.      75%



.4)整合Ehcache二级缓存 (第三方缓存有很多种,以Ehcache为例)

 	(1)需要导入如下三个jar包

```xml
<!-- https://mvnrepository.com/artifact/net.sf.ehcache/ehcache-core -->
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache-core</artifactId>
    <version>2.6.11</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.0.3</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>

```

   (2)编写ehcache.xml配置文件 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
<!--    当二级缓存的对象  超过内存限制时 (缓存对象个数>maxElementsInMemory)当存入的硬盘文件-->
    <diskStore path="E:\u\Ehcache"/>

<!--
    maxElementsInMemory:设置在内存中缓存 对象的个数
    maxElementsOnDisk:设置在硬盘中缓存 对象的个数
    overflowToDisk:设置缓存是否 永久不过期
    diskPersistent:当内存中缓存的对象个数 超过maxElementsInMemory的时候 是否转移到硬盘
    timeToIdleSeconds:当两次访问 超过该值的时候,将缓存对象失效
    timeToLiveSeconds:一个缓存对象,最多存放的时间(生命周期 )
    diskExpiryThreadIntervalSeconds:每隔多长时间  通过一个线程 来清理硬盘中的缓存
    memoryStoreEvictionPolicy:当缓存对象的最大值时  处理的策略:LRU 这个值是 删除最少使用
-->
    <defaultCache
            eternal="false"
            maxElementsOnDisk="1000000"
            maxElementsInMemory="1000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="100"
            timeToLiveSeconds="100"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```

(3)开启二级缓存 在主配置中

```xml
<!--        开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
```

(4)在mapper编写cache标签

```xml
<!--    声明开启-->
<cache type="org.mybatis.caches.ehcache.EhcacheCache">
<!--    如果编写了下面的值  会覆盖全局配置的那个ehcache.xml-->
<!--    <property name="maxElementsInMemory" value="2000"/>-->
<!--    <property name="overflowToDisk" value="true"/>-->
</cache>
```

（5）测试与mybatis自带的二级缓存一样的



### 课时12:MyBatis逆向工程

.1)表丶类丶接口丶mapper.xml四者密切相关,因此,当知道一个的时候  其他三个应该可以自动生成

.2)具体实现步骤

​	(1)导入jar包

```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
    <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-core</artifactId>
      <version>1.3.7</version>
    </dependency>

 <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.5</version>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.6</version>
    </dependency>
```

  (2)编写配置配置文件generator.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="MybatisGenerator" targetRuntime="mybatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!-- 数据库连接信息：驱动类 ，连接地址，用户名，密码-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                        userId="root" password="root">
        </jdbcConnection>

        <!-- 默认值 -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成的实体类的存放位置 -->
        <javaModelGenerator targetPackage="net.bdqn.hbz.pojo" targetProject="./src">
            <!-- enableSubPackages：是否让schema作为包的后缀 -->
<!--            <property name="enableSubPackages" value="true"/>-->
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成的Mapper映射文件（XML）的存放位置 -->
        <sqlMapGenerator targetPackage="net.bdqn.hbz.mapper" targetProject="./src">
<!--            <property name="enableSubPackages" value="false"/>-->
        </sqlMapGenerator>

        <!-- 生成的mapper接口(JAVA文件)的存放位置 -->
        <javaClientGenerator targetPackage="net.bdqn.hbz.mapper" type="XMLMAPPER" targetProject="./src">
<!--            <property name="enableSubPackages" value="false"/>-->
        </javaClientGenerator>

        <!-- 指定数据库表 -->
        <table tableName="student"/>
        <table tableName="studentcart"/>
        <table tableName="studentclass"/>

    </context>
</generatorConfiguration>

```

(3)执行类

```java
package net.bdqn.hbz.test;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.exception.InvalidConfigurationException;
import org.mybatis.generator.exception.XMLParserException;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.io.IOException;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class Test {
    public static void main(String [] args) throws IOException, XMLParserException, InvalidConfigurationException, SQLException, InterruptedException {
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        // 指定配置文件
        File configFile = new File("E:\\workJavaEE\\Mybatis\\work\\MyGenerator\\target\\classes\\net\\bdqn\\hbz\\test\\generator.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        myBatisGeneratorgenerate(null);
    }
}

```



### 课时13:多环境切换、注解方式与增删改返回值问题

.1)多环境切换

​	(1)配置properties

```properties
#mysql的配置环境
mysql.driver=com.mysql.jdbc.Driver
mysql.url=jdbc:mysql://localhost:3306/ssm
mysql.username=root
mysql.password=root

#oracle的配置环境
 <property name="driver" value="oracle.jdbc.driver.OracleDriver"/>
                <property name="url" value="jdbc:oracle:thin:@localhost:1521:orcl"/>
                <property name="username" value="scott"/>
                <property name="password" value="tiger"/>
 
oracle依赖jar    可能引入不了需要添加手动添加jar包
 <dependency>
      <groupId>com.oracle</groupId>
      <artifactId>ojdbc6</artifactId>
      <version>11.1.0.7.0</version>
    </dependency>
```

  (2)配置主配置文件环境

```xml
    <environments default="envMysql">
<!--        mysql配置环境-->
        <environment id="envMysql">
<!--                配置事务-->
            <transactionManager type="JDBC">
            </transactionManager>
<!--            配置数据库连接信息-->
            <dataSource type="POOLED">
                <property name="driver" value="${mysql.driver}"/>
                <property name="url" value="${mysql.url}"/>
                <property name="username" value="${mysql.username}"/>
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>

<!--        oracle环境配置-->
        <environment id="envOracle">
            <!--                配置事务-->
            <transactionManager type="JDBC">
            </transactionManager>
            <!--            配置数据库连接信息-->
            <dataSource type="POOLED">
                <property name="driver" value="${oracle.driver}"/>
                <property name="url" value="${oracle.url}"/>
                <property name="username" value="${oracle.username}"/>
                <property name="password" value="${oracle.password}"/>
            </dataSource>
        </environment>
    </environments>

<!--    配置数据库支持类-->
    <databaseIdProvider type="DB_VENDOR">
            <property name="MySQL" value="mysql"/>
            <property name="Oracle" value="oracle"/>
    </databaseIdProvider>
```

1.通过default来切换环境

2.通过支持类来配置支持哪些数据库

(3)编写不同数据库sql

列如oracle:rownum      mysql:limit

```xml
  <select id="selectStudentById" resultType="student" parameterType="Integer" databaseId="oracle">
        select * from student where stuno=#{stuno}
    </select>   
    <select id="selectStudentById" resultType="student" parameterType="Integer" databaseId="mysql">
        select * from student where stuno=#{stuno}
    </select>
```

1.通过databaseId来找寻主配置里面配置的支持类别名

2.select两个id可以一样,当你的环境时oracle的时候就是执行oracle的id里面的sql,当你的环境时mysql的时候就是执行mysql的id里面的sql

2.)注解方式开发

​	(1)推荐使用xml开发(而注解开发适用于一些小项目)

​	(2)如何使用注解开发

​		1.配置主文件的mapper标签  通过class来映射接口  为了是让程序知道你的sql语句是存在接口中 

```xml
<mapper class="org.hbz.dao.IStudentDao"></mapper>
```

​		无论是注解还是xml都可以可以一劳永逸的方式直接填写包名存在哪里  (批量引入)   以下的语句两种方式都兼容

```xml
 <package name="org.hbz.dao"/>
```

​	(3)将sql语句写在接口的方法的上面

```java
 @Select("select * from student where stuNo=#{stuNo}")
    Student queryStudentByNo(int stuNo);
```

.3)增删改的返回值问题

​		返回值可以是void ,Integer,Long,Boolean

​		如何操作?只需要在接口的方法修改返回值



### 课时14:事务提交与不同数据库的自增方式

.1)事务的提交

​	(1)手动提交的方式

```java
SqlSession session=sessionFactory.openSession();//默认值为false  需要手动提交
//使用commit()方法来提交
session.commit();
```

​	(2)自动提交的方式

```java
SqlSession session=sessionFactory.openSession(true);
```



.2)不同数据库的自增方式

​	(1)mysql支持自增长的方式

​		1.所以在添加的时候不需要传入id值  数据库会自动处理

```xml
<insert id="addStudent" parameterType="Student">
        insert into student(stuName,stuAge,graName,stuSex,homeaddress,schooladdress,cardid,classid)
        values(#{stuName},#{stuAge},#{graName},#{stuSex},#{homeaddress},#{schooladdress},
        #{cardid},#{classid}
        )
    </insert>
```

​      2.如何回写呢?(就是增加后返回增加后的id值)     增加完成以后  调用传入的对象.getId()即可拿到回写值

```xml
<!--    mysql实现学生头增加-->
    <insert id="addStudent" parameterType="Student" databaseId="mysql" useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuName,stuAge,graName,stuSex,homeaddress,schooladdress,cardid,classid)
        values(#{stuName},#{stuAge},#{graName},#{stuSex},#{homeaddress},#{schooladdress},
        #{cardid},#{classid}
        )
    </insert>
```

​						useGeneratedKeys:是否开启返回主键id          keyProperty:使用什么来接收回写数据

​	(2)oracle不支持自增长的方式

​			1.使用序列来模拟自增长

```sql
create sequence myseq increment by 1 strat with 2;
```

​				increment:每次增长数             strat:从什么数开始

​			2.如何调用序列来完成添加数据自增长

​				2.1:序列自带的两个属性:

​					nextval:序列中下一个值         currval:当前序列值

​		        2.2:编写sql语句  方式一  并且回写id

```xml
<!--    oracle实现学生增加-->
    <insert id="addStudent" parameterType="Student" databaseId="oracle" >
        <selectKey keyProperty="stuno" resultType="integer" order="BEFORE">
            select myseq.nextval from dual
        </selectKey>
        insert into student(stono,stuName,stuAge)
            values(#{stuno},#{stuName},#{stuAge})
    </insert>
```

​				 2.3:编写sql语句  方式二   并且回写id

```xml
  <insert id="addStudent" parameterType="Student" databaseId="oracle" >
        <selectKey keyProperty="stuno" resultType="integer" order="AFTER">
            select myseq.currval from dual
        </selectKey>
        insert into student(stono,stuName,stuAge)
            values(myseq.nextval,#{stuName},#{stuAge})
    </insert>
```



### 课时15::MyBatis处理多个参数问题

.1)目前  将多个参数封装到有个pojo对象中,使用对象传递 --> 如果不用呢 传入的是多个参数

​	(1)传入多个参数时,不用在mapper.xml中编写parameterType,并且参数类型不是混合类型(又有对象又有简单的类型)

​			1.方式一

​			异常提示:

​			stuNo不能使用,就可以使用的是:arg3, arg2, arg1, arg0, param1, param2， param3, param4这些参数  

```xml
 <insert id="addStudent"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuName,stuAge,graName,stuSex)
        values(#{param1},#{param2},#{param3},#{param4})
    </insert>
或者
 <insert id="addStudent"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuName,stuAge,graName,stuSex)
        values(#{arg0},#{arg1},#{arg2},#{arg3})
    </insert>

```

​			这样就可以实现多参数传递了  接口如下

```java
 Integer addStudent(String stuName,Integer stuAge,String graName,Integer sex);
```

​		2.方式二   命名参数  推荐使用

​			2.1可以再接口中这么写

```java
   Integer addStudent(@Param("sName") String stuName, @Param("sAge")Integer stuAge,
                       @Param("gName")String graName, @Param("sSex")Integer sex);
```

​			2.2在sql映射文件直接写自定义的别名

```xml
 <insert id="addStudent"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuName,stuAge,graName,stuSex)
        values(#{sName},#{sAge},#{gName},#{sSex})
    </insert>
```

​	(2)如果一个入参有简单类型又有对象类型怎么办? 混合类型

​		1.在接口编写

```java
 Integer addStudent(@Param("sName") String stuName, @Param("stu")Student student);
```

​		2.使用命名的名称.类中的属性

```xml
  <insert id="addStudent"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuName,stuAge,graName,stuSex)
        values(#{sName},#{stu.stuAge},#{stu.graName},#{stu.stuSex})
    </insert>

```



### 课时16:使用HashMap存储查询结果集

.1)使用返回值为Map 查询单个数据的时候

​	(1)配置如下

```xml
<!--    返回值为map-->
    <select id="queryStudentById" resultType="java.util.Map">
        select stuNo "no",stuName "name",stuAge "age",stuSex "sex" from student where stuno=#{id};
    </select>

```

​	其中stuNo是数据库的字段名,"no"是stuNo的别名,用于在map中get值时使用(作为map的key)。map.get("name......");

​	(2)如果不起别名,则map的key就是字段名

```xml
<!--    返回值为map-->
    <select id="queryStudentById" resultType="java.util.Map">
        select stuNo ,stuName ,stuAge ,stuSex  from student where stuno=#{id};
    </select>
```



.2)使用返回值为Map 查询多个数据的时候

​	(1)在SQL映射文件中编写

```xml
<!--    多个学生返回的map对象-->
    <select id="queryStudent" resultType="java.util.Map">
        select * from student
    </select>
```

​		1.mybatis会自动的吧这个返回值封装成一个对象存储  

​	(2)值已经封装了,通过@MapKey("")来指定数据库的哪一个字段来作为这个map的key

```java
	@MapKey("stuno")
    Map<Integer,Student> queryStudent();
```

​	注意:@MapKey("stuno")里面填写的值必须与数据库字段一模一样   不一样除非起别名  不然会key会是null

​	(3)结果:

```java
{1={stuSex=1, graName=S1, stuName=ol, stuAge=12, stuno=1}, 2={stuSex=1, graName=S1, stuName=plp, stuAge=12, stuno=2}, 3={stuSex=1, graName=aji, stuName=何哥哥, stuAge=24, stuno=3}, 4={stuSex=1, graName=aji, stuName=何哥哥001, stuAge=24, stuno=4}, 5={stuSex=1, graName=S1, stuName=ji, stuAge=23, stuno=5}, 6={stuSex=1, graName=S1, stuName=lo, stuAge=23, stuno=6}, 7={stuSex=1, graName=S3, stuName=kkk, stuAge=55, stuno=7}}
```



​			