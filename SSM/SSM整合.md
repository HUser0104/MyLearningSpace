### 课时15:SSM整合

​	整个整合需要的jar包

```xml
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>javax.validation</groupId>
      <artifactId>validation-api</artifactId>
      <version>1.1.0.Final</version>
    </dependency>

    <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>5.4.1.Final</version>
    </dependency>
    <dependency>
      <groupId>org.jboss.logging</groupId>
      <artifactId>jboss-logging</artifactId>
      <version>3.3.0.Final</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml</groupId>
      <artifactId>classmate</artifactId>
      <version>1.3.3</version>
    </dependency>
    <!--数据库驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>
    <!-- 数据库连接池 -->
    <dependency>
      <groupId>com.mchange</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.5.2</version>
    </dependency>
    <!--Servlet - JSP -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.2</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>

    <!--Mybatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.2</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.2</version>
    </dependency>

    <!--Spring-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.9.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.1.9.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
    <dependency>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
      <version>1.1.1</version>
    </dependency>

    <!--    ajax json依赖的jar-->
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.8</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.8</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.8</version>
    </dependency>
    <!--        引入lombok-->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.10</version>
    </dependency>
    <!--   文件上传-->
    <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.4</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
    </dependency>
    <!--    aop辅助类-->
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.2</version>
    </dependency>
<!--    日志-->
    <!-- https://mvnrepository.com/artifact/log4j/log4j -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
  </dependencies>
```



.1)Spring-----SpringMVC----MyBatis

​	1.第一步整合Spring---MyBatis:将mybatis的SqlSessionFactory交给Spring来管理

​		1.2 谁整合谁   谁是老大

​			1.2.1 spring -mybatis     Strut2    springMvc  dubbo   Quartz  ....... 

​			1.2.2 spring是老大   所以mybatis是整合到spring

​				12.2.1 SqlSessionFactory-->SqlSession  --->StudentMapper--->CRUD

​				1.2.2.2 可以发现,mybatis的最终通过SqlSessionFactory来操作数据库

​					Spring整合Mybatis 其实就是 将mybatis的SqlSessionFactory交给Spring来管理

​				1.2.2.3.开始整合

​					1.2.2.3.1 导入jar包 笔记的开始都已经全部导入了

​					1.2.2.3.2  创建类和表 

​					1.2.2.3.3 通过Mapper.xml 将类和表建立关系				

```xml
<?xml version="1.0" encoding="UTF-8"?>
         <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
    <select id="queryStudentById" parameterType="int" resultType="net.bdqn.hbz.pojo.Student">
        
    </select>
</mapper>
```

​					1.2.2.3.4  之前使用mybatis:config.xml --->SqlSessionFactory

​						现在整合的时候,需要通过Spring 来管理SqlSessionFactory,因此 产生qlSessionFactory所需要的数据库信息  不在放入config.xml 而需要放入spring配置文件中

​					1.2.2.3.5 如何配置spring的配置文件

​						1.2.2.3.5.0 如何把Spring加入到web项目中(在Web.xml中配置)

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
<!--  在web项目中引入spring-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
</web-app>
```

​						1.2.2.3.5.1 第一步创建db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/smbms
username=root
password=root
```

​						1.2.2.3.5.2 配置数据源信息

```xml
<!--    注入properties文件-->
    <bean id="config" class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">
<!--        加载文件 可以配置配入多个文件-->
        <property name="locations">
            <array>
                <value>classpath:db.properties</value>
            </array>
        </property>
    </bean>
    <!--    配置数据库信息(替代mybatis的配置文件config.xml)-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${driver}"></property>
        <property name="jdbcUrl" value="${url}"></property>
        <property name="user" value="${username}"></property>
        <property name="password" value="${password}"></property>
    </bean>
```

​						1.2.2.3.5.3 注入mybatis核心对象SqlSessionFactory

```xml
  <!--    注入mybatis核心对象SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--            将SqlSessionFactoryBean的属性dataSource注入值-->
        <property name="dataSource" ref="dataSource"/>
        <!--            注入SQL映射路径-->
        <property name="mapperLocations" value="classpath:net/bt/dao/*.xml"/>
        <!--       配置setting-->
        <property name="configuration">
            <bean class="org.apache.ibatis.session.Configuration">
<!--                开启延迟加载-->
                <property name="lazyLoadingEnabled" value="true"/>
<!--                开启延迟加载-->
                <property name="aggressiveLazyLoading" value="true"/>
<!--                开启日志-->
                <property name="logImpl" value="org.apache.ibatis.logging.stdout.StdOutImpl"/>
            </bean>
        </property>
<!--        设置别名-->
        <property name="typeAliasesPackage" value="net.bt.pojo"/>
<!--        安装分页插件-->
        <property name="plugins">
            <array>
                <bean class="com.github.pagehelper.PageInterceptor">
                    <property name="properties">
                        <value>
                            autoRuntimeDialect=true
                            reasonable=true
                        </value>
                    </property>
                </bean>
            </array>
        </property>
    </bean>
```

​						1.2.2.3.5.4 将sqlSessionFactory交给spring

```xml
    <bean id="mappers" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!--        注入SqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
<!--        批量指定哪个包的对象-->
        <property name="basePackage" value="net.bdqn.hbz.dao"></property>
    </bean>
```



​	2.第二步整合Spring---SpringMVC:就是将Spring和SpringMVC各自配置一遍

​		1.将SpringMVC配置一遍   开始整合  其实不是整合本来就是一家!!!!

​			1.1 第一步 :需要在web.xml配置SpringMVC自带的Servlet   交给SpringMVC处理   

```xml
<!--  拦截所有请求交给springMVC-->
  <servlet>
    <servlet-name>springMVC</servlet-name>
    <!--servlet-class中的值是spring-webmvc包提供的类，即前端控制器，用于控制所有请求 -->
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:MVCContext.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--url-pattern（重点）中有3个值，分别为/、 /*、 *.action  -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

​			1.2 第二步 配置SpringMVC文件

```xml
<!--    配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value=""/>
        <property name="suffix" value=""/>
    </bean>
<!--    SpringMVC基础配置-->
    <mvc:annotation-driven/>
```

