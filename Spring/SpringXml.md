### 课时1:Spring环境搭建、STS工具、第一个Spring程序

.1)概要以及介绍

​	1.2002 作者 Rod Jonnon    发布了一篇文章<Expoer One-toOne j2eedvelopment and Design>

​	2.2003年根据这篇文章产生了一些感悟  产生了Spring 

​	3.spring最基础的核心AOP  IOC

​	4.经过了18年的的洗礼衍生出很多框架   Spring Data,Spring Boot ,Spring Cloud,Spring Framework,Spring social

.2)IOC:控制反转 (DI:依赖注入) 

​	1.搭建Spring的环境

​		1.1 导入spring的依赖jar包

```xml
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.9.RELEASE</version>
    </dependency>
	 <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.10</version>
      <scope>provided</scope>
    </dependency>
```

​		1.2 编写配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

2. 开发Spring程序(IOC)

   ​	 2.1 编写配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   <bean id="student" class="net.bdqn.hbz.pojo.Student">
       <property name="stuno" value="1"></property>
       <property name="stuName" value="zs"></property>
       <property name="stuAge" value="24"></property>
   </bean>
   </beans>
   ```

   ​		2.1.1 id:唯一标识符             class:指定类的路径

   ​		2.1.2 property标签 :代表该class的属性    

   ​				2.1.2.1 name:属性名称

   ​				2.1.2.1 value :属性值

​			2.2 通过bean的id来获取一个对象

```java
//创建上下文对象
        ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        //通过bean的id来获取对象
        Student student =(Student) context.getBean("student");
        System.out.println(student);
```

​	3.这样写的一些分析以及好处

​		3.1 不需要new

​		3.2 对象属性的赋值

​		3.3 在applicationContext.xml中产生的对象,被spring放入了一个 称为Spring IOC容器里面

### 课时2:解耦合发展史、控制反转、依赖注入

.1)SpringIOC的发展史

 	1.刚开始的的new对象 太零散了   后期维护比较脑火 

```java
public void doJava(){
        Course course=new CourseJava();
        course.javaCourse();
    }

    public void doHtml(){
        Course course=new CourseHtml();
        course.htmlCourse();
    }
```

​	2.太零散了  改进  通过简单工厂创建对象

```java
   public static Course getCourse(String name){
            if ("html".equals(name)){
                return new CourseHtml();
            }else{
                return new CourseJava();
            }
        }
```

​		2.1 通过简单工厂，可以将创建课程集中操作,方便后期维护

​	3.但是简单工厂还是有弊端   之后衍生出了springIOC

​		3.1 springIOC是一个超级工厂  

​			3.1.1 可以容纳任何对象

.2)控制反转

​	1.反转的是:获取对象的方式

```xml
context.getBean("student");
```

​		1.1 直接从springIOC容器中拿(ApplicationContext.xml)

​	2.为了更加清晰的理解ioc,ioc在一次大会上更名为DI(依赖注入)



.3)DI(依赖注入)  和控制反转是一样的  只是改了名

​	1.将属性值 注入给了属性 ,将属性 注入给了bean ,将bean注入给了ioc容器

​	2.总结:ioc/di ，无论要什么对象,都可以直接去springioc容器中获取,而不需要自己操作(new /setXXX)

​	3.因此之后的ioc分为2步:1 先给springioc中存放对象并赋值(相当于共产主义)  2 去容器中取

​	4.改造自己编写的简单工厂

```java
public void courseShow(String name){
        ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        Course course =(Course) context.getBean(name);
        course.show();
    }
```

​		4.1 name就是容器中的id  通过id获取容器中的对象

### 课时3:三种方式的依赖注入、给各种集合类型的属性注入

.1)三种方式的依赖注入 

​	1.set方式的依赖注入  使用的标签是property

​		 1.1 IOC容器的赋值:如果是简单类型(8个基本+String)   使用value赋值;

​		如果是对象类型 使用ref=“其他需要依赖的bean的id” 来赋值

```xml
<!--    课程类-->
    <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse">
        <property name="CName" value="java"></property>
        <property name="CHour" value="200"></property>
        <property name="teacher" ref="Teacher"></property>
    </bean>
<!--    老师类-->
    <bean id="Teacher" class="net.bdqn.hbz.pojo.Teacher">
        <property name="teaName" value="何邦柱"></property>
        <property name="teaAge" value="20"></property>
    </bean>
```

​			1.1.1 就是调用了类中的set方法

​			1.1.2 因此可以实现依赖注入

​			1.1.3 依赖注入的底层就是通过反射技术来做的

​				1.1.3.1 列如teaName    先通过拼接set+TeaName ---->setTeaName 在通过class里面的值来通过反射找寻类中的方法

​	2.构造方法方式的依赖注入  

​		2.1 IOC容器的赋值:如果是简单类型(8个基本+String)   使用value赋值;

​			如果是对象类型 使用ref=“其他需要依赖的bean的id” 来赋值

​		2.2 通过<constructor-arg>来进行注入  如果<constructor-arg>的顺序与构造方法的顺序不一致,则需要通过type或者index或name来指定

```xml
 <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse">
       <constructor-arg value="java" index="0" name="cName" type="java.lang.String"></constructor-arg>
        <constructor-arg value="200" index="1"></constructor-arg>
        <constructor-arg ref="Teacher" index="2"></constructor-arg>
    </bean>

 <bean id="Teacher" class="net.bdqn.hbz.pojo.Teacher">
<constructor-arg value="何邦柱" index="0"></constructor-arg>
        <constructor-arg value="20" index="1"></constructor-arg>
    </bean>
```

​	3.p命名空间方式注入

​		3.1 引入命名空间

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

​		3.2 如何使用使用p标签

```xml
<bean id="Teacher" class="net.bdqn.hbz.pojo.Teacher" p:teaName="傻逼" p:teaAge="22">
```

​			3.2.1 如果是简单类型:p:属性名="输入的值"       如果是引用类型(除了String)：p:属性名-ref=“引用的id”

​			3.2.2 注意多个p之间不要有空格



.2)给各种集合类型的属性注入 列如 Map List  Set  Properties  本次示例用的是set注入方式

​	1.List集合注入

```xml
 <property name="list">
            <list>
                <value>list篮球1</value>
                <value>list篮球2</value>
                <value>list篮球3</value>
            </list>
        </property>
```

​	2.array数组注入

```xml
<property name="array">
            <array>
                <value>array足球1</value>
                <value>array足球2</value>
                <value>array足球3</value>
            </array>
</property>
```

​	3.map集合注入

```xml
<property name="map">
            <map>
                <entry> 
                    <key>
                        <value>ko</value>
                    </key>
                    <value>乒乓球1</value>
                </entry>
                <entry>
                    <key>
                        <value>kk</value>
                    </key>
                    <value>乒乓球2</value>
                </entry>
                <entry>
                    <key>
                        <value>koo</value>
                    </key>
                    <value>乒乓球3</value>
                </entry>
            </map>
        </property>
```

​	4.set集合注入

```xml
<property name="strings">   
    <set>       
        <value>1</value>       <value>2</value>       <value>3</value>  
    </set>
</property>
```

​	5.properties集合注入

```xml
 <property name="properties">
            <props>
                <prop key="jj">网球1</prop>
                <prop key="jj1">网球2</prop>
                <prop key="jj2">网球3</prop>
            </props>
        </property>
```

### 课时4:特殊值的注入问题和各种类型的自动装配

.1）特殊值的注入问题

​	1.value和<value>的区别

```xml
<constructor-arg value="何邦柱" index="0"></constructor-arg>
```

```xml
<constructor-arg  index="1"><value>20</value></constructor-arg>
```

​		1.1    								使用子元素<value>注入     								而使用value属性注入

​				参数的位置				写在首尾标签<value></value>的				写在value的属性值中(必须加双引号)

​													中间(不加双引号)。	

​				type属性					有(可选) 可以通过type属性指定数据类型。	  无。

​				参数值包含特殊字		两种处理方法：1.使用<![CDATA[  ]]标>记。	一种处理方法:即使用xml预定义的

​				符(<,&)时的处理方法			2.使用xml预定义的实体引用。					实体引用。

​		1.2  如何处理参数值包含特殊符号

​			1.2.1 使用XML预定义的实体引用

```xml
  <bean id="Teacher" class="net.bdqn.hbz.pojo.Teacher">
<!--        <property name="teaName" value="何邦柱"></property>-->
<!--        <property name="teaAge" value="20"></property>-->
        <constructor-arg value="何邦柱&lt;&amp;" index="0"></constructor-arg>
        <constructor-arg  index="1">
            <value>20</value>
        </constructor-arg>
    </bean>
```

​				1.2.1.1   “&lt    ----->  <       											&amp代表  --->&

​			1.2.2   使用<![CDATA[  ]]标记

```xml
<!--    课程类-->
    <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse">
        <property name="CName" >
            <value>java<![CDATA[ >>>> ]]></value>
        </property>
        <property name="CHour" value="200"></property>
        <property name="teaCher" ref="Teacher"></property>
<!--        <constructor-arg value="java" index="0" name="cName" type="java.lang.String"></constructor-arg>-->
<!--        <constructor-arg value="200" index="1"></constructor-arg>-->
<!--        <constructor-arg ref="Teacher" index="2"></constructor-arg>-->
    </bean>
```

​	2.constructor注入的特殊值处理与set注入是一样的

​	3.如何处理null值以及""值

​		3.1 给元素赋值null

```xml
		 <property name="CName" >
            <null/>
        </property>
```

​		3.2 给元素赋值“”

```xml
		<property name="CName" >
            <value></value>
        </property>
或者
		<property name="CName" value="">
        </property>
```

.2)在定义ioc的前提:该bean的类必须要有无参构造



.3)自动装配(只适用于  ref类型):

​	1.约定大于配置

​	2.如何使用byName自动装配      byName的本质就是byId

```xml
   <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse" autowire="byName">
  </bean>
```

​		2.1 自动寻找:其他bean的值id值=该类的属性名称

​	3.如何使用byType自动装配 

```XML
    <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse" autowire="byType">
</bean>
```

​			3.1 自动寻找bean的class类型=该类的属性类型(注意:此种方式  必须满足:当ioc容器中    只能有一个Bean满足条件 )

​	4.如何使用constructor自动装配

```xml
    <bean id="Scourse" class="net.bdqn.hbz.pojo.Scourse" autowire="constructor">
</bean>
```

​			4.1 其他的bean的类型(class) 是否与  该Course类的构造方法参数的类型一致 ;本质上就是byType 

​	4.可以在头文件中一次性将该ioc 容器的所有bean统一设置成自动装配

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-autowire="byName"
>
```

​		4.1 这虽然可以减少代码量 ,但是会降低可读性,使用时需要谨慎 

.4)使用注解自定义bean:通过注解方式  将bean以及相应的属性值   放入ioc容器

​	1.首先声明头部

```xml
	   xmlns:context="http://www.springframework.org/schema/context"
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd"
```

​	2.开始配置扫描器  （如果有多个包  使用逗号隔开）

```xml
<!--    配置扫描器 扫描注解形式的bean对象-->
    <context:component-scan base-package="net.bdqn.hbz.dao"/>
```

​	3.声明注解类放入bean

```java
@Component("IStudentDao")
public class IStudentDao {
    public Integer addStudent(Student student){
        System.out.println("你好......我是何邦柱");
        return 1;
    }
```

​	4.@Component("IStudentDao)范围太大了  可以细化:

​		4.1 dao层使用:@Repository("IStudentDao")

​		4.2 service层使用:@Service("IStudentDao")

​		4.3 控制层使用:@Controller("IStudentDao")

### 课时:5 使用注解实现声明式事务

.1)使用注解实现声明式事务

​	1.目标:通过事务  使以下方法  要么全部成功     要么全部失败

```java
public void addStudent(){
    deleteStudent();
    //增加班级
    //增加学生
    //crud
}
public void deleteStudent(){
    
}
```

​	2.导入相关的jar包

```xml
  <!-- https://mvnrepository.com/artifact/aopalliance/aopalliance -->
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
<!--mysql驱动包-->
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.6</version>
    </dependency>
<!--连接池使用数据源-->
<dependency>
      <groupId>commons-dbcp</groupId>
      <artifactId>commons-dbcp</artifactId>
      <version>1.4</version>
    </dependency>
<!--连接池-->
<!-- https://mvnrepository.com/artifact/commons-pool/commons-pool -->
    <dependency>
      <groupId>commons-pool</groupId>
      <artifactId>commons-pool</artifactId>
      <version>1.6</version>
    </dependency>

还有几个spring自带的
spring-jdbc-4.3.9.RELEASE.jar
spring-tx-4.3.9.RELEASE.jar
```

​	3.编写配置文件

​		3.1 增加事务tx的命名空间

```xml
xmlns:tx="http://www.springframework.org/schema/tx"
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
```

​		3.2 增加对事务的支持

```xml
 <tx:annotation-driven transaction-manager="txManager"/>
```

​			3.2.1 配置事务管理器

```xml
 <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

​				3.2.1.1 配置数据源相关

```xml
<!--    配置数据库相关-事务-->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
<!--        配置驱动包-->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
<!--        配置连接地址-->
        <property name="url" value="jdbc:mysql://localhost:3306/ssm"></property>
<!--        配置用户名-->
        <property name="username" value="root"></property>
<!--        配置密码-->
        <property name="password" value="root"></property>
<!--        最大连接时间-->
        <property name="maxActive" value="10"></property>
<!--        最大空闲时间-->
        <property name="maxIdle" value="6"></property>
    </bean>
```

​	4.将需要 成为事务的方法   前增加注解:

```java
	 @Transactional(readOnly = false,propagation = Propagation.REQUIRED)
    @Override
    public void addStudent(Student student) {
        iStudentDao.addStudent(new Student());
    }
```

​	4.1 readOnly的详细说明

![1584019218219](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019218219.png)

​	4.2 propagation事务传播行为的详细说明

​		4.2.1 @Transactional(propagation=Propagation.REQUIRED) ：如果有事务, 那么加入事务, 没有的话新建一个(默认情况下)
　　4.2.2@Transactional(propagation=Propagation.NOT_SUPPORTED) ：容器不为这个方法开启事务
　　4.2.3@Transactional(propagation=Propagation.REQUIRES_NEW) ：不管是否存在事务,都创建一个新的事务,原来的挂起,新的执行完毕,继续执行老的事务
　　4.2.4@Transactional(propagation=Propagation.MANDATORY) ：必须在一个已有的事务中执行,否则抛出异常
　　4.2.5@Transactional(propagation=Propagation.NEVER) ：必须在一个没有的事务中执行,否则抛出异常(与Propagation.MANDATORY相反)
　　4.2.6@Transactional(propagation=Propagation.SUPPORTS) ：如果其他bean调用这个方法,在其他bean中声明事务,那就用事务.如果其他bean没有声明事务,那就不用事务.

​	5.Transactional注解的一些其他的属性

![1584019790050](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019790050.png)

![1584019833794](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019833794.png)

![1584019852025](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019852025.png)



### 课时6::AOP、execution表达式、前置通知

.1)AOP:面向方面编程

​	1.aop的一些名词

![1584020838459](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584020838459.png)



.2)AOP的通知

​	1.一些通知的示意图

![1584021326040](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584021326040.png)

​	2.以前置通知为例子   其他的通知写法都差不多

​		2.1 导入相关jar包

```xml
  <!-- https://mvnrepository.com/artifact/aopalliance/aopalliance -->
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
	<!-- https://mvnrepository.com/artifact/aspectj/aspectjweaver -->
	<dependency>
    <groupId>aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.5.3</version>
	</dependency>

```

​		2.2 配置

​			2.2.0 编写ioc容器头部

```xml
xmlns:aop="http://www.springframework.org/schema/aop"
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd"
```

​			2.2.1 注入业务类

```xml
<!--    配置切入点的方法在哪个类-->
    <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
        <property name="iStudentDao" ref="studentDao"></property>
    </bean>
```

​			2.2.2 注入通知类

```xml
		<!--    注入”前置通知“类-->
		<!--    连接线的一方 切面-->
    <bean id="before" class="net.bdqn.hbz.aop.Before"/>
```

​			2.2.3 将addStudent  和  通知进行关联

```xml
		<aop:config>
<!--        配置切入点 (在哪里执行通知)  连接线的另一方-->
        <aop:pointcut id="add" expression="execution(public void addStudent(net.bdqn.hbz.pojo.Student))"/>
<!--        advisor相当于连接切入点和切面的线-->
        <aop:advisor advice-ref="before" pointcut-ref="add"/>
</aop:config>
```

​				2.2.3.1 aop:config:将 将addStudent  和  通知进行关联

​				2.2.3.2 aop:pointcut :切入点的位置

​						id:切入点唯一标识符           expression:切入点的位置

​				2.2.3.3  aop:advisor:相当于连接切入点和切面的线

​						advice-ref:通知类(通知类注入的id)        pointcut-ref:切入点的id

​		2.3 编写代码

​			2.3.1 aop:每当之前add()之前（IStudentService.java  addStudent()）   自动执行一个方法

​			2.3.2 add():业务方法     log()l:自动执行的通知,即aop前置通知

​			2.3.3 编写前置通知的一个类实现MethodBeforeAdvice  将这个类变成前置通知类

```java
package net.bdqn.hbz.aop;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

/**
 * 前置通知类
 */
public class Before implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("执行了前置通知");
    }
}

```

​			2.3.4 编写业务类

```java
package net.bdqn.hbz.service.impl;

import net.bdqn.hbz.dao.impl.IStudentDaoImpl;
import net.bdqn.hbz.pojo.Student;
import net.bdqn.hbz.service.IStudentService;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

public class IStudentServiceImpl implements IStudentService {
    private IStudentDaoImpl iStudentDao;

    public void setiStudentDao(IStudentDaoImpl iStudentDao) {
        this.iStudentDao = iStudentDao;
    }

    /**
     *
     * @param student
     */
    @Transactional(readOnly = false,propagation = Propagation.REQUIRED)
    @Override
    public void addStudent(Student student) {
        iStudentDao.addStudent(new Student());
    }
}

```

​	3.如果想要添加多个切入点  通过execution来配置多个切入点

```xml
  <aop:pointcut id="add" expression="execution( public void 	deleteStudent(java.lang.Integer)) or execution(public void addStudent(net.bdqn.hbz.pojo.Student))"/>
```

​		A

![1584026741419](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584026741419.png)



### 课时7:后置通知、异常通知

.1)后置通知的实现步骤以及解释 

​	1.导入相应jar包  前置通知示例已经有了  这里就不做过多的讲解

​	2.编写业务类以及编写后置通知类

​		2.1 业务类代码如下

```java
package net.bdqn.hbz.service.impl;

import net.bdqn.hbz.dao.impl.IStudentDaoImpl;
import net.bdqn.hbz.pojo.Student;
import net.bdqn.hbz.service.IStudentService;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

public class IStudentServiceImpl implements IStudentService {
    private IStudentDaoImpl iStudentDao;

    public void setiStudentDao(IStudentDaoImpl iStudentDao) {
        this.iStudentDao = iStudentDao;
    }

    /**
     *
     * @param student
     */
    @Transactional(readOnly = false,propagation = Propagation.REQUIRED)
    @Override
    public void addStudent(Student student) {
        iStudentDao.addStudent(new Student());
    }

    @Override
    public void deleteStudent(Integer no) {
        System.out.println("正在删除学生");
    }


}

```

​		2.2 编写后置通知类  需要实现一个接口  AfterReturningAdvice 所有接口都可以参考前置通知的一张图

```java
package net.bdqn.hbz.aop;


import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

/**
 * 前置通知类
 */
public class After implements AfterReturningAdvice {
    /**
     *
     * @param o 目标的返回值
     * @param method 目标方法
     * @param objects 目标参数列表
     * @param o1 目标对象
     * @throws Throwable
     */
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("后置通知:目标对象:"+o1+",目标方法:"+method.getName()+",目标参数个数:"+
                objects.length+",目标返回值"+o);
    }
}

```

​	3.将切面和切入点进行一个连接 

​		3.1 注入这个后置通知类到SpringIOC容器当中

```xml
 <bean id="after" class="net.bdqn.hbz.aop.After"></bean>
```

​		3.2 配置切面和切入点关联关系

​			3.2.1  配置切入点信息  (目的就是为了找到这个方法)    以及把切面进行关联

```xml
    <aop:config>
<!--        配置切入点信息-->
        <aop:pointcut id="addAfter" expression="execution(public void addStudent(net.bdqn.hbz.pojo.Student))"></aop:pointcut>
<!--        配置连接-->
        <aop:advisor advice-ref="after" pointcut-ref="addAfter"></aop:advisor>
    </aop:config>
```



.2)异常通知

​	1.导入相应jar包  前置通知示例已经有了  这里就不做过多的讲解

​	2..编写业务类以及编写后置通知类

​		2.1 业务类代码如下

```java
package net.bdqn.hbz.service.impl;

import net.bdqn.hbz.dao.impl.IStudentDaoImpl;
import net.bdqn.hbz.pojo.Student;
import net.bdqn.hbz.service.IStudentService;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

public class IStudentServiceImpl implements IStudentService {
    private IStudentDaoImpl iStudentDao;

    public void setiStudentDao(IStudentDaoImpl iStudentDao) {
        this.iStudentDao = iStudentDao;
    }

    /**
     *
     * @param student
     */
    @Transactional(readOnly = false,propagation = Propagation.REQUIRED)
    @Override
    public void addStudent(Student student) {
        iStudentDao.addStudent(new Student());
    }

    @Override
    public void deleteStudent(Integer no) {
        System.out.println("正在删除学生");
    }
}
```

​	2.2编写异常通知类

​		2.2.1 在实现ThrowsAdvice发现并没有需要我们重写什么方法   但是源码文档中给出提示:

​				public void afterThrowing([Method, args, target], ThrowableSubclass);

​			2.2.1.1 这个提示的意思就是[Method, args, target]这些参数可有可无  因为只是异常通知 ,有时候并不需要目标信息.

​	3.将切面和切入点进行一个连接 

​		3.1  注入这个异常通知类到SpringIOC容器当中

```xml
<!--    注入异常通知类-->
    <bean id="throw" class="net.bdqn.hbz.aop.Throw"></bean>
```

​		3.2 配置切面和切入点关联关系

```xml
<!--   将addStudent  和  异常通知进行关联-->
    <aop:config>
        <!--        配置切入点信息-->
        <aop:pointcut id="addThrow" expression="execution(public void addStudent(net.bdqn.hbz.pojo.Student))"/>
<!--        配置连接-->
        <aop:advisor advice-ref="throw" pointcut-ref="addThrow"/>
    </aop:config>
```

### 课时8:环绕通知

.1)环绕通知:在目标方法的前后丶异常发生时丶最终等各个地方都可以  进行通知  , 最强大的一个通知;

​	可以获取目标方法的  全部控制权(目标方法是否执行丶执行之前丶执行之后丶参数丶返回值等)

​	1.导入相应jar包  前置通知示例已经有了  这里就不做过多的讲解

​	2.编写业务类以及编写环绕通知类

​		2.1 业务类代码如下

```java
package net.bdqn.hbz.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

/**
 * 环绕通知
 */
public class Surround  implements MethodInterceptor {

    /**
     *
     * @param invocation
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        Object reult=null;
        try{
            System.out.println("执行了前置通知");
            //执行目标方法
            reult=invocation.proceed();
            //通过后置通知来获取一些信息
            System.out.println("后置通知:目标对象:"+invocation.getThis()+",目标方法:"+invocation.getMethod().getName()+",目标参数个数:"+
                    invocation.getArguments().length+"，返回值:"+reult);
        }catch (Exception e){
            System.out.println("执行了环绕通知");
        }
        return reult;
    }
}
	
```

​			2.1.1 目标方法的一些信息都可以通过MethodInvocation来获取

​			2.1.2 如何控制方法是否执行目标方法   就是invocation.proceed();  写了就执行  不写就不执行

​	3.将切面和切入点进行一个连接 

​		3.1  注入这个异常通知类到SpringIOC容器当中

```xml
<!--    注入环绕通知类-->
    <bean id="surround" class="net.bdqn.hbz.aop.Surround"></bean>
```

​	   3.2 配置切面和切入点关联关系

```xml
    <!--   将addStudent  和  环绕通知进行关联-->
    <aop:config>
<!--        配置切入点信息-->
        <aop:pointcut id="addSurround" expression="execution(public void addStudent(net.bdqn.hbz.pojo.Student))"/>
<!--        配置连接-->
        <aop:advisor advice-ref="surround" pointcut-ref="addSurround"></aop:advisor>
    </aop:config>
```

​	4.环绕通知的底层通过拦截器实现

### 课时9::基于Schema形式的AOP实现

.1)基于Schema形式的AOP实现

​	1.导入相应jar包  前置通知示例已经有了  这里就不做过多的讲解

​	2.编写通知类该类不需要继承或者写任何注解

```java
package net.bdqn.hbz.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;

/**
 * 通知类
 */
public class LogSchema {

    /**
     * 前置通知
     */
    public void before(){
        System.out.println("基于Schema进行了前置通知");
    }

    /**
     * 后置通知
     * @param jp 目标信息
     * @param returnValue 返回值
     */
    public void after(JoinPoint jp,Object returnValue){
        System.out.println("基于Schema进行了前置通知");
    }

    public void execution(Throwable throwable){
        System.out.println("基于Schema异常通知:"+throwable.getMessage());
    }

    /**
     * 环绕通知
     */
    public void huanrao(ProceedingJoinPoint point){
        System.out.println("基于Schema执行了环绕前置通知");
        try{
            point.proceed();
            System.out.println("基于Schema执行环绕后置通知");
            System.out.println("环绕基于Schema目标方法名称:"+point.getSignature().getName());
        }catch (Throwable throwable){
            System.out.println("基于Schema执行了环绕异常通知");
        }finally {
            System.out.println("基于Schema执行了环绕最终通知");
        }
    }
}

```

​	3.编写配置 

```xml
<!--    注入通知类-->
    <bean id="logSchema" class="net.bdqn.hbz.aop.LogSchema"/>
<!--    建立关联-->
    <aop:config>
<!--        目标信息-->
        <aop:pointcut id="tra" expression="execution(public void addStudent(net.bdqn.hbz.pojo.Student))"/>
<!--        建立连接-->
        <aop:aspect ref="logSchema">
<!--            将目标对象添加前置通知-->
            <aop:before method="before" pointcut-ref="tra"></aop:before>
 <!--            将目标对象添加后置通知-->
            <aop:after-returning returning="returnValue" method="after" pointcut-ref="tra"></aop:after-returning>
<!--          将目标对象添加异常通知  -->
            <aop:after-throwing method="execution" throwing="throwable" pointcut-ref="tra"></aop:after-throwing>
<!--            将目标对象添加环绕-->
            <aop:around method="huanrao" pointcut-ref="tra"></aop:around>
        </aop:aspect>
    </aop:config>

```

​		3.1 ref="logSchema"代表的是通知bean的id值

​		3.2  method=“值”  代表的是通知类的方法       

​		3.3 pointcut-ref=“值” 代表目标信息

​		3.4  returning=“值”  返回值     throwing=“值”  捕获的异常范围

### 课时10:Spring开发Web项目  

.1)Spring开发Web项目 

​	1.在java程序的入口是统一的main(),因此只需要在main中 实例化一次applicationContext.xml，就可以让springioc初始化

```java
ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
```

​	2.在java web程序如何初始化springioc呢?

​		2.1 思路:当服务(tomcat)启动时，通过监听器将springioc初始化一次

​		2.2 监听器:监听tomcat服务启动,一旦启动  立刻实例化一个ioc容器对象(这个监听器不需要自己写,spring-web-4.3.9.RELEASEjar提供了)   因此spring开发web至少需要7个jar  :spring-java的以及spring-web

注意:spring-web-4.3.9.RELEASE.jar要放在lib下面才能生效

​		2.2.1  导入spring-web-4.3.9.RELEASE.jar目的实现监听器  

​		2.2.2 web项目启动,会自动加载web.xml,因此需要在web.xml中加载监听器(ioc容器初始化)

```xml
  <!--  指定Ioc容器的位置-->
  <context-param>
    <!--    监听器的父类ContextLoader中有一个属性contextConfigLocation  来指定配置的ioc容器-->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  <!--  指定鉴定器的位置-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
```

### 课时11:拆分Sping配置文件，Spring整合Web项目

.1)拆分Sping配置文件

.1)拆分Sping配置文件

​	1.java项目 :

 applicationContext1.xml， applicationContext2.xml， applicationContext3.xml

需要哪个就直接加载哪个就可以

​	2.web项目:根据什么拆分

​		2.1 三层结构:

​			applicationDao.xml

​			applicationService.xml

​			applicationController.xml

​			applicationDB.xml

​		2.2 功能拆分

​			学生相关的功能:applicationStudent.xml

​			班级相关的功能:applicationClass.xml

​		2.3 合并:如何将多个配置文件  加载

​			2.3.1 第一种方式在web.xml中配置

```xml
 <!--  指定Ioc容器的位置-->
  <context-param>
    <!--    监听器的父类ContextLoader中有一个属性contextConfigLocation  来指定配置的ioc容器-->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml,
                  classpath:application-Dao.xml,
                  classpath:application-Service.xml,
                  classpath:application-Controller.xml
    </param-value>
  </context-param>
  <!--  指定鉴定器的位置-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
```

​			2.3.2 也可以这样写   推荐使用

```xml
  <!--  指定Ioc容器的位置-->
  <context-param>
    <!--    监听器的父类ContextLoader中有一个属性contextConfigLocation  来指定配置的ioc容器-->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml,
      classpath:application-*.xml
    </param-value>
  </context-param>
  <!--  指定鉴定器的位置-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
```

2.)Spring整合WEB

2.)Spring整合WEB

​	1.将dao  service   servlet创建号

​	2.通过依赖注入一层一层的注入

![1584163945256](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584163945256.png)

​		2.1 application-Controller.xml代码如下

```xml
<bean id="studentServlet" class="net.bdqn.hbz.web.StudentServlet">
        <property name="iStudentService" ref="studentService"></property>
    </bean>
```

​		2.2 application-Service.xml代码如下

```xml
 <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
            <property name="iStudentDao" ref="studentDao"></property>
        </bean>
```

​		2.3 application-Dao.xml代码额如下

```xml
 <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
            <property name="iStudentDao" ref="studentDao"></property>
        </bean>
```

​		3.测试  通过a标签测试    但是页面报null指针的错误 下节课解答  (因为不在同一个容器中)

​			3.1  一个在servlet容器中    一个在springioc中

```java
package net.bdqn.hbz.web;

import net.bdqn.hbz.service.IStudentService;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/StudentServlet")
public class StudentServlet extends HttpServlet {
    private IStudentService iStudentService;

    public void setiStudentService(IStudentService iStudentService) {
        this.iStudentService = iStudentService;
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    }
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String name=iStudentService.queryStudent();
        //传送到页面
        req.setAttribute("name",name);
        req.getRequestDispatcher("result.jsp").forward(req,resp);
    }
}

```

### 课时12:Servlet容器与SpringIoC容器，及二者之间的桥梁

.1)Servlet容器与SpringIoC容器，及二者之间的桥梁

​	1.如何打通两个直接的桥梁  解决null指针的问题

​		1.1 通过servlet中的init方法来建立连接   原有的在application-Controller.xml--》bean中注入可以删除了

```java
 	@Override
    public void init() throws ServletException {
        ApplicationContext context= WebApplicationContextUtils.getWebApplicationContext(this.getServletContext());
        iStudentService =(IStudentService) context.getBean("studentService");
    }

```

​		1.2 在实际项目中不会这么写   Struts1.0  Struts2    springMvC会自动整合

### 课时13:作用域

.1)bean的作用域

![1584282246718](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584282246718.png)

​	1.单例对象   mybatis默认就单例      singleton    结果返回true 说明引用地址是一样的

```xml
<!--   注入dao层-->
    <bean id="iStudentDaoImpl" class="org.bd.dao.impl.IStudentDaoImpl"></bean>
```

```java
    public static void test1(){
        ApplicationContext context=new FileSystemXmlApplicationContext("E:\\workJavaEE\\Spring\\work\\SpringAnoo\\src\\resulrces\\applicationContextt.xml");
        IStudentDao iStudentDao1=(IStudentDao)context.getBean("iStudentDaoImpl");
        IStudentDao iStudentDao2=(IStudentDao)context.getBean("iStudentDaoImpl");
        System.out.println(iStudentDao1==iStudentDao2);
    }
```

​	2.多例对象  prototype    结果返回了false   因为不是一个引用地址不一样了   多实例了

```xml
<!--   注入dao层-->    <bean id="iStudentDaoImpl" class="org.bd.dao.impl.IStudentDaoImpl" scope="prototype"></bean>
```

 3. 两种执行的时机

    3.1 singleton :容器在初始化的时候,就会创建对象;以后在getBean时,不在创建对象
    	3.1.1 单例也支持延迟加载bean 也就是使用的时候才创建    lazy-init="true"

    ```xml
    <!--   注入dao层-->   
    <bean id="iStudentDaoImpl" class="org.bd.dao.impl.IStudentDaoImpl" lazy-init="true">
        </bean>
    ```

    

    3.2 prototype   :容器在初始化的时候,不创建对象;只是在每次使用的时候(每次从容器取出对象的时候,context.getBean(xxx)),在创建对象;并且   每次getBean()都会创建新的对象

    

### 课时14:Bean的生命周期,通过import引入分散的spring文件

.1)Bean的生命周期   出生   创建使用     销毁

​	1.通过xml来指定出生以及销毁

​		1.1 出生   ：容器加载bean对象的时候

​		1.2 销毁 ：context.close();在执行这个的时候bean就销毁了

```xml
  <!--   注入dao层-->
    <bean id="iStudentDaoImpl" class="org.bd.dao.impl.IStudentDaoImpl" init-method="init" destroy-method="dis">
    </bean>
```

​		init-method:bean出生自定义方法        destroy-method:bean自定义销毁方法

​		通过这个上面的注定一个方法为创建以及销毁  可以做初始化工作和销毁工作

.2)通过import引入分散的spring文件  在applicationContext中编写

```xml
<!--   导入分散文件-->
    <import resource="classpath:IStudentDaoContext.xml"/>
```

### 课时15:使用aop来管理Spring事务

.1)使用aop来管理Spring事务

​	1.目标:通过事务  使以下方法  要么全部成功     要么全部失败

```java
public void addStudent(){
    deleteStudent();
    //增加班级
    //增加学生
    //crud
}
public void deleteStudent(){
    
}
```

​	2.导入相关的jar包

```xml
  <!-- https://mvnrepository.com/artifact/aopalliance/aopalliance -->
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
<!--mysql驱动包-->
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.6</version>
    </dependency>
<!--连接池使用数据源-->
<dependency>
      <groupId>commons-dbcp</groupId>
      <artifactId>commons-dbcp</artifactId>
      <version>1.4</version>
    </dependency>
<!--连接池-->
<!-- https://mvnrepository.com/artifact/commons-pool/commons-pool -->
    <dependency>
      <groupId>commons-pool</groupId>
      <artifactId>commons-pool</artifactId>
      <version>1.6</version>
    </dependency>

还有几个spring自带的
spring-jdbc-4.3.9.RELEASE.jar
spring-tx-4.3.9.RELEASE.jar
```

​	3.编写配置文件

​		3.1 增加事务tx的命名空间

```xml
xmlns:tx="http://www.springframework.org/schema/tx"
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
```

​		3.2 编写事务交给Spring

```xml
<!--    配置Spring管理事务-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--    配置哪些方法  和 事务行为-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="query*" propagation="SUPPORTS"/>
        </tx:attributes>
    </tx:advice>
    <aop:config>
        <aop:pointcut id="serviceMethod" expression="execution(public * org.bt.service.impl.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="serviceMethod"/>
    </aop:config>
```

​		3.3 之后编写服务层即可

​	4.事务的一些参数

​		4.2 propagation事务传播行为的详细说明

​			4.2.1 @Transactional(propagation=Propagation.REQUIRED) ：如果有事务, 那么加入事务, 没有的话新建一个(默认情况下)
　　	4.2.2@Transactional(propagation=Propagation.NOT_SUPPORTED) ：容器不为这个方法开启事务
　　	4.2.3@Transactional(propagation=Propagation.REQUIRES_NEW) ：不管是否存在事务,都创建一个新的事务,原来的挂起,新的执行完毕,继续执行老的事务
　　	4.2.4@Transactional(propagation=Propagation.MANDATORY) ：必须在一个已有的事务中执行,否则抛出异常
　　	4.2.5@Transactional(propagation=Propagation.NEVER) ：必须在一个没有的事务中执行,否则抛出异常(与Propagation.MANDATORY相反)
　　	4.2.6@Transactional(propagation=Propagation.SUPPORTS) ：如果其他bean调用这个方法,在其他bean中声明事务,那就用事务.如果其他bean没有声明事务,那就不用事务.

​		4.3 readOnly的详细说明

![1585038039368](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585038039368.png)

​		4.4  <tx:method name="add*" propagation="REQUIRED" .../>的一些属性 以及说明



![1584019833794](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019833794.png)

![1584019852025](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1584019852025.png)

