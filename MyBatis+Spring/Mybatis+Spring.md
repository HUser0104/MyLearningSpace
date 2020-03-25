### 	课时13:Spring整合MyBatis思路

.1)谁整合谁   谁是老大

1. spring -mybatis     Strut2    springMvc  dubbo   Quartz  ....... 
2. spring是老大   所以mybatis是整合到spring

.2)Spring整合MyBatis思路

​	1.SqlSessionFactory-->SqlSession  --->StudentMapper--->CRUD

​	2.可以发现,mybatis的最终通过SqlSessionFactory来操作数据库

​		Spring整合Mybatis 其实就是 将mybatis的SqlSessionFactory交给Spring来管理

​	3.开始整合

​		3.1 导入jar包

```tex
commons-dbcp-1.4.jar     commons-logging-1.1.1.jar       commons-pool-1.6.jar
log4j-1.2.17.jar          mybatis-3.4.6.jar               mybatis-spring-1.3.1.jar
mysql-connector-java-5.1.6.jar			spring-aop-4.3.9.RELEASE.jar
spring-beans-4.3.9.RELEASE.jar			spring-context-4.3.9.RELEASE.jar
spring-core-4.3.9.RELEASE.jar			spring-expression-4.3.9.RELEASE.jar
spring-jdbc-4.3.9.RELEASE.jar			spring-tx-4.3.9.RELEASE.jar
spring-web-4.3.9.RELEASE.jar			spring-context-support-4.3.9.RELEASE.jar
```

​	  3.2  创建类和表 

​	  3.3 创建mybatis主配置文件(可以省略全部交给Spring来管理)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

<!--数据库信息-->
<!--    加载映射文件SQL-->
</configuration>
```

​		3.4 通过Mapper.xml 将类和表建立关系

```xml
<?xml version="1.0" encoding="UTF-8"?>
         <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
    <select id="queryStudentById" parameterType="int" resultType="net.bdqn.hbz.pojo.Student">
        
    </select>
</mapper>
```

​		3.5  之前使用mybatis:config.xml --->SqlSessionFactory

​				现在整合的时候,需要通过Spring 来管理SqlSessionFactory,因此 产生qlSessionFactory所需要的数据库信息  不在放入config.xml 而需要放入spring配置文件中

​		3.6 如何配置spring的配置文件

​			3.6.1 第一步创建db.properties

​			3.6.2 配置数据源信息

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

​			3.6.3 注入mybatis核心对象SqlSessionFactory

```xml
<!--    在Spring容器中创建mybatis的核心类SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
<!--        配置数据源-->
        <property name="dataSource" ref="dataSource"/>
<!--        加载mybatis的主配置文件到spring-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>
```

​		3.7 使用Spring-Mybatis整合产物开发程序

​		3.8 目标:通过spring产生的mybatis最终操作需要的动态Mapper对象(StudentMapper)

​		3.9 第一种方式实现

​			3.9.1 在dao层的实现类继承SqlSessionDaoSupport类获得SqlSession  

```java
package net.bdqn.hbz.dao.impl;

import net.bdqn.hbz.dao.IStudentMapper;
import net.bdqn.hbz.pojo.Student;
import org.apache.ibatis.session.SqlSession;
import org.mybatis.spring.support.SqlSessionDaoSupport;

/**
 * 学生实现类
 */
public class IStudentMapperImpl extends SqlSessionDaoSupport implements IStudentMapper {
    public Integer addStudent(Student stduent) {
        //获取SqlSession
        SqlSession session=super.getSqlSession();
        IStudentMapper mapper = session.getMapper(IStudentMapper.class);
        return mapper.addStudent(stduent);
    }
}

```

​			3.9.2 获得sqlSession的前提需要一个SqlSessionFactory  所以在spring配置文件中把SqlSessionFactory注入到上面这个类的父类(SqlSessionDaoSupport)  

```xml
<!--    注入学生dao-->
    <bean id="studentDao" class="net.bdqn.hbz.dao.impl.IStudentMapperImpl">
<!--        注入IStudentMapperImpl父类的SqlSessionFactory属性值-->
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

​				ref的值就是spring管理的SqlSessionFactory的id

​			3.9.3 编写业务层

```java
package net.bdqn.hbz.service.impl;

import net.bdqn.hbz.dao.IStudentMapper;
import net.bdqn.hbz.pojo.Student;
import net.bdqn.hbz.service.IStudentService;

public class IStudentServiceImpl implements IStudentService {
    //注入dao层
    private IStudentMapper iStudentMapper;

    public void setiStudentMapper(IStudentMapper iStudentMapper) {
        this.iStudentMapper = iStudentMapper;
    }

    public Integer addStudent(Student student) {
        return iStudentMapper.addStudent(student);
    }
}

```

​			3.9.4 把业务层注入并且把dao层依赖也注入

```xml
<!--    注入学生业务-->
    <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
        <property name="iStudentMapper" ref="studentDao"/>
    </bean>
```

​			3.9.5 注意mybatis的主配置文件还是写mapper映射到SQL映射文件 了

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

<!--数据库信息-->
<!--    加载映射文件SQL-->
    <mappers>
        <package name="net.bdqn.hbz.dao"/>
    </mappers>
</configuration>
```

  也可以不写mappers的话spring就要处理了

```xml
<!--    在Spring容器中创建mybatis的核心类SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
<!--        配置数据源-->
        <property name="dataSource" ref="dataSource"/>
<!--        加载mybatis的主配置文件到spring-->
<!--        <property name="configLocation" value="classpath:mybatis-config.xml"/>-->
<!--        配置映射到SQL映射我呢间-->
        <property name="mapperLocations" value="net/bdqn/hbz/dao/*.xml"></property>
    </bean>
```

​		3.9.6 最后编写SQL映射文件SQL语句   然后测试

### 课时14:Spring整合MyBatis的几种方式  前面一个课时已经讲了一种思路了

.1)第二种方式 :就是省略掉第一种方式的实现类

​		2.1 在在第一种方式的基础上改造

​			2.1.1 dao的实现类可以删除了

​			2.1.2 改造spring配置文件

```xml
  <bean id="studentDao" class="org.mybatis.spring.mapper.MapperFactoryBean">
        <property name="mapperInterface" value="net.bdqn.hbz.dao.IStudentMapper"/>
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

​			mapperInterface代表你要给哪个接口创建代理   sqlSessionFactory代表告知数据库配置信息  就是第一种方式注入的sqlSessionFactory

.2）第三种方式:简化第二种方式   (也就是简化每创建一个接口都要写一次配置) 批量产生

​	1.在第二个基础上改造

​		1.1 改造spring配置文件

```xml
    <bean id="mappers" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!--        注入SqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
<!--        批量指定哪个包的对象-->
        <property name="basePackage" value="net.bdqn.hbz.dao"></property>
    </bean> 
<!--    注入学生业务-->
    <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
        <property name="iStudentMapper" ref="IStudentMapper"/>
    </bean>
```

 	2.批量产生mapper对在StringIOC的id 默认就是接口名称(接口名=id值而不是等于mappers) ref="接口名“

```xml
  <bean id="studentService" class="net.bdqn.hbz.service.impl.IStudentServiceImpl">
        <property name="iStudentMapper" ref="IStudentMapper"/>
    </bean>
```

​	