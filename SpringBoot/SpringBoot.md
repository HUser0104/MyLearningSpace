# 			第一章:Spring Boot入门

## p1:Spring Boot_入门简介

### 	1.Spring Boot来简化Spring开发,约定大于配置,去繁从简,just run就能创建一个独立的,产品级别的应用

### 	2.Spring Boot的一些优点

​		2.1 -快速创建独立运行的Spring项目以及与主流框架集成

​		2.2 -使用嵌入式的Servlet容器,应用无需打成WAR包

​		2.3 - starters自动依赖与版本控制

​		2.4 -大量的自动配置,简化开发,也可修改默认值

​		2.5 -无需配置XML ,无代码生成,开箱即用

​		2.6 -准生产环境的运行时应用监控

​		2.7-与云计算的天然集成

## p2:微服务简介

​		2014  martin fowler

​		微服务:架构风格

​		一个应用应该是一组小型服务;可以通过HTTP的方式进行沟通;



​		每一个功能元素最终都是一个可独立替换和独立升级的软件单元

​		详细参照微服务文档https://martinfowler.com/articles/microservices

​		![1585044961075](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585044961075.png)

## p3:环境准备	

### 	1.你必须掌握以下内容:

​		1.1 - Spring框架的使用经验

​		1.2 -熟练使用Maven进行项目构建和依赖管理

​		1.3 -熟练使用Eclipse或者IDEA

### 	2.环境约束

​		2.1 -jdk1.8 : Spring Boot 1.7及以上

​		2.2 -maven3.x

​		2.3 -IntellijIDEA2017

​		2.4 -SpringBoot 1.5.9.RELEASE

### 	3.maven的配置

​		3.1 在maven的stting.xml配置文件中的profiles中添加

```xml
		<profile>
      <id>jdk-1.8</id>
        <activation>
            <activeByDefault>true</activeByDefault>
            <jdk>1.8</jdk>
        </activation>
        
        <properties>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
        </properties>
    </profile>
```

### 	4.idea配置maven

​		4.1 打开idea点击setting

![1585046404633](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585046404633.png)

​		4.2 配置本地自己的maven路径

![1585046502669](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585046502669.png)

## p4:springboot-helloworld

### 	1.创建一个maven工程(jar)

### 	2.导入springboot依赖

```xml
 <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 	3.编写主程序启动SpringBoot  

```java

/**
 * @SpringBootApplication 来标注一个主程序类   说明是一个SpringBoot类
 */
@SpringBootApplication
public class HelloWoldApplication {
    public static void main(String[] args) {
        //Spring应用启动起来
        SpringApplication.run(HelloWoldApplication.class);
    }
}
```

### 	4.编写业务  controller service ....

```java
@Controller
public class HelloController {
    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World";
    }
}
```

### 	5.运行主程序测试

### 	6.简化部署 打包

​		6.1 导入打包插件

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

​		6.2 点击maven---Lifecycle---package

​		6.3 在项目的target里面会生成jar包   之后打开PowerShell   输入 

```xml
java -jar .\后面更上jar的名称
```

## p5:HelloWorld细节-场景启动器（starter）

### 	1.pom文件

​		1.1 父项目

```xml
   <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
她的父项目是
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-dependencies</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath>../../spring-boot-dependencies</relativePath>
	</parent>
她来真正的管理SpringBoot应用里面的所有依赖版本;
Spring boot版本仲裁中心
```

​			1.1.1 以后我们导入依赖默认不需要写版本号；(没有在dependencies里面管理的自然要写版本号)

### 	2.启动器

```xml
		 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

​		2.1 **spring-boot-starter**-web  拆分为 **spring-boot-starter**与web

​			2.1.1  **spring-boot-starter**:场景启动器,帮我们导入了web模块正常运行所依赖的组件

​		2.2 Spring Boot将所有的功能场景都抽取出来,做成-个个的starters (启动器) , 只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器

## p6:HelloWorld细节-自动配置

### 	1.主程序类丶主入口类

```java
package org.bt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}

```

​		1.1 @SpringBootApplication:Spring Boot应用所标注在某个类上说明这个类是SpringBoot的主配置				类,Springboot就应该运行这个类的main方法来启动SpringBoot应用;

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

​		1.2 @SpringBootConfiguration:SpringBoot的配置类;

​			1.2.1 标注在某个类上,标识这是一个spring boot的配置类;

​					1.2.1.1 @Configuration :配置类上来标注这个注解:

​						配置类--------配置文件:配置类也是spring的一个组件:@Component

​		1.3 @EnableAutoConfiguration:开启自动配置功能;

​			1.3.1 以前我们需要配置的东西,SpringBoot帮我们自动配置;@EnableAutoConfiguration告诉SpringBoot开启自动配置功能;这样就可以生效;

```java
@AutoConfigurationPackage
@Import({EnableAutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

​				1.3.1.1 @AutoConfigurationPackage：自动配置包

​					1.3.1.1.1 @Import({Registrar.class}):spring的底层注解@import,给容器导入一个组件

​	**将配置类(SpringBootApplication标注的类)的所在及下面所有的子包扫描	到Spring容器中;**

​					1.3.1.1.2  @Import({Registrar.class}):给容器如何导入组件?:

​							EnableAutoConfigurationImportSelector :导入哪些组件的选择器;

​							将所有需要导入的组件以全类名的方式返回;这些组件就会被添加到容器中

​							会给容器中导入非常多的自动配置类(xxxAutoConfiguration)

​							就是给容器中导入这个场景需要的所有组件,并配置好组件

![1585104941399](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585104941399.png)

​					有了自动配置类,免去了我们手动配置注入功能组件等的工作：SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classLoader) ; .

​				Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作;以前我们需要自己配置的东西，自动配置类都帮我们;

![1585105554374](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585105554374.png)

##### 自动配置总结:整个j2ee的整体整合解决方案和自动配置都是在org.springframework.boot.autoconfigure

## p7:使用向导Spring Initializer快速创建Spring Boot应用

### 以idea为例快速搭建springboot的项目:

### 	1.选择Spring Initializer---->然后选择好自己的jdk--->点击next

![1585106112776](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106112776.png)

### 	2.编辑信息

​	![1585106557883](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106557883.png)

​	3.选择模块  springweb为例  后期可以自己想要什么功能就勾选什么功能 比如安全的  缓存的  等等 

![1585106677182](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106677182.png)

###  	4.默认生成的SpringBoot项目

​		4.1 主程序已经写好了,我们只需要写我们自己的业务逻辑

​		4.2 resources文件夹中目录结构

​			4.2.1 static:保存所有的静态文件;列如：css丶js丶images

​			4.2.2 templates:保存所有的模板页面;(SpringBoot默认jar包使用嵌入式的tomcat,不支持jsp页面);可以使用			模板引擎

​			4.2.3 :application.properties:Spring Boot应用的配置文件;可以修改一些默认配置;列如端口号

# 			第二章:Spring Boot配置

## p1:配置-yaml简介

### 	1.SpringBoot使用一个全局配置文件;配置文件名字是固定的

​		1.1 application.properties

​		1.2 application.yml

### 	2.配置文件的作用:修改SpringBoot的默认配置值:SpringBoot在底层都给我们自动配置好;

### 	3.YAML (YAML Ain'Markup Languge)

​		3.1 YAML  A Markup Languge:是一个标记语言

​		3.2 YAML isn't Markup Languge:不是一个标记语言

### 	4.标记语言

​		4.1 以前配置文件:大多数都是使用xxx.xml

​		4.2 YMAL: 以数据为中心,比json丶xml等更适合作为配置文件

​		4.3 yaml配置例子

```yaml
server:
  port: 1024
```

## p2::配置-yaml语法

### 	1.基本语法

​		1.1 k v: 值:表示key---value(空格必须要有的)

​		1.2 以**空格**的缩进来控制层级关系;只要是左对齐的一列数据,都是同一一个层级的

```yaml
server:
 port: 1024
 path: /hello
```

​			1.2.1 属性对大小写很敏感的;也是把空格挖玩到极致的男人

### 	2.值的写法

​		2.1 字面量:普通的值(数字,字符串,布尔)

​			2.1.1 k: v :字面直接来写

​				2.1.1.1 字符串默认不用加上单引号或者双引号:

​					"":双引号;不会转义字符串里面的特殊字符;特殊字符会作为本身想表示的意思

​						name: "zhangsan \n lisi" :输出; zhangsan 换行 lisi

​					‘’:单引号;name :'zhangsan \n lisi':输出;zhangsan \n lisi

​		2.2 对象;Map(属性的值)(键对值):

​			2.2.1 对象还是k: v方式 :在对象的下一行写对象的属性和值的关系;注意缩进

```yaml
student:
 name: zhangsan
 age: 23
```

​			2.2.2 行内写法

```yaml
student: {name: zhangsan,age: 23}
```

​		2.3 数组(List,Set)

​			2.3.1 用-值来表示一个元素值

```yaml
pets:
 - cat
 - dog
 - pig
```

### 			2.3.2 行内写法

```yaml
pets: [cat,dog,pig]
```

## p3:配置-yaml配置文件值获取

### 	1.配置文件

```yaml
proson:
  name: 张三
  age: 20
  boss: false
  birth: 2017/01/04
  maps:
    k1: v1
    k2: v2
  lists:
    -lisi
    -wu
  dog:
    name: 小狗
    age: 2
```

### 	2.javaBean

```java
/**
 * 降配置文件中配置的每一个属性,映射到这个组件
 * @ConfigurationProperties  告诉SpringBoot将本类中的所有的属性和配置文件中相关的配置进行绑定
 * 只有是荣琼中的组件 才能使用容器的功能
 */
@ConfigurationProperties(prefix = "proson")
@Component
public class Proson {
    private String name;
    private Integer age;
    private Boolean boss;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```

### 	3.我们可以导入一个配置处理器

```xml
<!--        导入配置文件处理器,配置文件进行绑定就会有提示-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
       
```

## p4:配置-properties配置文件编码问题

### 	1.properties实现proson

```proper
proson.name=张三
proson.age=20
proson.birth=2017/01/01
proson.boss=false
proson.lists=1,2,3
proson.maps.k1=v1
proson.maps..k2=v2
proson.dog.name=dog
proson.dog.age=20
```

### 	2.注意如果出现中文乱码问题需要手动设置 设置步骤如下

​		2.1 setting-----file *encoding*----Default encoding for properties files 改成utf-8----勾选Transparent native- to- ascii conversion即可解决

## p5:配置-@ConfigurationProperties与@Value区别

### 	1.区别

|                | @ConfigurationProperties | @Value     |
| -------------- | ------------------------ | ---------- |
| 功能           | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定       | 支持                     | 不支持     |
| SpEL           | 不支持                   | 支持       |
| JSR303数据校验 | 支持                     | 不支持     |
| 复杂类型       | 支持                     | 不支持     |

### 	2.不管配置文件是yaml还是properties都能获取到值

​		2.1 如果说,我们只是在某个业务逻辑中需要获取一下配置 文件中的某项值,使用@Value ;

```java
@RestController
public class HelloName {
    @Value("${proson.name}")
    private String name;

    @RequestMapping("/hello")
    public String sayHello(){
        return "hello"+name;
    }
}
```

​		2.2 如果说,我们专门编写了- -个javaBean来和配置文件进行映射,我们就直接使用@ConfigurationProperties ;

### 	3.配置文件注入数据校验

```java
/**
 * 降配置文件中配置的每一个属性,映射到这个组件
 * @ConfigurationProperties  告诉SpringBoot将本类中的所有的属性和配置文件中相关的配置进行绑定
 * 只有是荣琼中的组件 才能使用容器的功能
 */
@ConfigurationProperties(prefix = "proson")
@Component
@Validated
public class Proson {
//    @Value("${proson.name}")
    @Email
    private String name;
//    @Value("#{22*2}")
    private Integer age;
//    @Value("true")
    private Boolean boss;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```

## p6:配置引入Spring的组件-@PropertySource、@ImportResource、@Bean

​	1.@PropertySource:加载指定的配置文件;@ConfigurationProperties默认读取的是主配置 不想让主要的配置变得不可读

```java
@PropertySource(value = {"classpath:proson.properties"})
@Component
@ConfigurationProperties(prefix = "proson")
//@Validated
public class Proson {
//    @Value("${proson.name}")
//    @Email
    private String name;
//    @Value("#{22*2}")
    private Integer age;
//    @Value("true")
    private Boolean boss;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```

​	2.@ImportResource::导入spring的配置文件,让配置文件里面的内容生效

​		2.1 SpringBoot 里面没有Spring的配置文件,我们自己编写的配置文件,也不能识别;

​		2.2 想让Spring的配置文件生效,加载进来; .@ImportResource标注在一个配置类上

```java

@ImportResource(value = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringBoot02ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBoot02ConfigApplication.class, args);
    }
}
导入spring的配置文件  让其生效
```

​	

​	不来编写spring的配置文件了

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean class="org.bt.bean.Dog" id="dog"></bean>
</beans>
```

​	3.SpringBoot推荐给容器中添加组件的方式:全注解的方式

​		3.1 配置类=========配置文件

```java
/**
 * @Configuration 来指明当前类为spring的配置类
 */
@Configuration
public class MyAppConfig {

    //@Bean注解来标明此方法为<bean></bean>   bean默认名称就是方法的名称
    @Bean
    public Dog dog(){
        return new Dog();
    }
}
```

## p7:配置-配置文件占位符

​	1.随机数

```xml
${random. value}、${random.int}、 ${random.long}
${random. int(10)}、${random. int[1024, 65536]}
```

​	2.占位符获取之前配置的值,如果没有指定的值:指定默认值

```properties
proson.name=张三${random.uuid}
proson.age=${random.int(100)}
proson.birth=2017/01/01
proson.boss=false
proson.lists=1,2,3
proson.maps.k1=v1
proson.maps..k2=v2
proson.dog.name=${proson.name:xiaomi}_dog
proson.dog.age=20
```

## p8:配置-Profile多环境支持

​	1.多Profile环境文件

​		1.1 我们在编写主文件的时候，文件名可以是application-{profile}.properties/yaml

​		1.2 默认使用的是application.proeperties的配置

​	2.yaml支持多文档快方式

```yaml
server:
  port: 1024
spring:
  profiles:
    active: prod
#  开发环境
---
server:
  port: 1029
spring:
  profiles: dev
#发布环境
---
server:
  port: 10299
spring:
  profiles: prod
```

​	3.激活指定profile

​		3.1 在配置文件中指定spring.profile.active=dev或者spring.profile.active=prod

```properties
#激活开发环境
spring.profiles.active=dev
#激活发布环境
spring.profiles.active=prod
```

​		3.2 命令行指定环境

​			3.2.1 点你edit Configurations

![1585195179190](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585195179190.png)

​			3.2.2  在这个里面输入  ---spring.profiles.active=dev或者prod

![1585195380417](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585195380417.png)

​		3.3 打包之后切换环境：java -jar .\spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev

​		![1585195786384](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585195786384.png)

​		3.4 虚拟机切换环境:-Dspring.profiles.active=dev

![1585195910261](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585195910261.png)

## p9:配置-配置文件的加载位置

​	1.SpringBoot启动会扫描一下的位置application.properties 或者 application.yaml文件作为SpringBoot默认配置文件

​		1.1 file:../config/

​		1.2 file:../

​		1.3 classpath:/config/

​		1.4 classpath:/

​	2.优先级由高到底,高的优先级配置会覆盖底的优先级配置

​	3.SpringBoot会从四个位置全部加载主配置文件;**互补位置**

​		3.1 列如高的优先级只配置了端口号   而优先级配置了项目访问路径  则也会采纳低优先级的配置  不是有了高优先级配置就不读优先级配置了

## p10:配置-外部配置加载顺序

​	1.命令行参数

```cmd
java -jar.\spring-boot02-config02-0.0.1-SNAPSHOT.jar --server.port=8888 --server.servlet.context-path=/abc

多个配置用空格分开 --参数名称
```

​	**由jar包外向jar包内进行寻找;**

​	**优先加载待profile的**

​	2.jar包外部的application-{profile}.properties或application.ym(带spring.profile)配置文件

​	3.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件

​	**再加载不带profile的**

​	4.jar包外部的application.properties或application.yml(不带spring.profile)配置文件

​	5.jar包内部的application.properties或application.yml(不带spring.profile)配置文件

[参考文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-external-config)

## p:11配置-自动配置原理

​	1.配置文件到底能配置哪些属性?怎么写?自动配置原理

​		[配置文件能配置的属性参照](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#common-application-properties)

​	2.**自动配置原理分析**

​		2.1 SpringBoot启动的时候加载主配置类,开启了自动配置功能@EnableAutoConfiguration

​		2.2 @EnableAutoConfiguration作用如下：

​			2.2.1 利用EnableAutoConfigurationImportSelector给容器中导入组件?

​			2.2.2 可以查看selectImports()方法的内容;

​			List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);//获取候选的配置

```java
SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader())
    扫描所有jar包类路径下的META-INF/spring.factories
    把所有扫描的对象封装成properties
    从properties中获取EnableAutoConfiguration.class类(类名)对应的值;然后把它们添加到容器中
```

![1585225689879](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585225689879.png)

​			2.2.3 将类路径下的META-INF/spring.factories配置的所有EnableAutoConfiguration加入到了容器中

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.DeviceResolverAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.DeviceDelegatingViewResolverAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.SitePreferenceAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
org.springframework.boot.autoconfigure.reactor.ReactorAutoConfiguration,\
org.springframework.boot.autoconfigure.security.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.FallbackWebSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.OAuth2AutoConfiguration,\
org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
org.springframework.boot.autoconfigure.social.SocialWebAutoConfiguration,\
org.springframework.boot.autoconfigure.social.FacebookAutoConfiguration,\
org.springframework.boot.autoconfigure.social.LinkedInAutoConfiguration,\
org.springframework.boot.autoconfigure.social.TwitterAutoConfiguration,\
org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
org.springframework.boot.autoconfigure.web.DispatcherServletAutoConfiguration,\
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.web.HttpEncodingAutoConfiguration,\
org.springframework.boot.autoconfigure.web.HttpMessageConvertersAutoConfiguration,\
org.springframework.boot.autoconfigure.web.MultipartAutoConfiguration,\
org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.web.WebClientAutoConfiguration,\
org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.WebSocketAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.WebSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration
```

​		每一个这样的xxxautoconfigure类都是一个组件,都加入到容器中;他们来做自动配置

​	3.每一个自动配置类进行自动配置

​	4.HttpEncodingAutoConfiguration为例解释自动配置原理 (帮我们解决乱码问题)

```java
@Configuration//表示这是一个配置类,以前我们用品那个配置文件一样,也可以在这里面添加组件
@EnableConfigurationProperties({HttpEncodingProperties.class})//自动指定类的ConfigurationProperties功能;

@ConditionalOnWebApplication //Spring底层@Conditional注解,根据不同的条件,如果满足指定条件,则整个配置文件生效;   判断当前的应用是否是web应用 如果是则生效  如果不是则不生效整个配置
@ConditionalOnClass({CharacterEncodingFilter.class})//判断当前项目有没有这个类 CharacterEncodingFilter;SpringMVC中进行乱码问题的过滤器
@ConditionalOnProperty(
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true
)//判断配置文件中是否存在某个配置 spring.http.encoding.enabled;如果不存在  判断也是成立的
//即使我们配置文件中不配置spring.http.encoding.enabled=true ,也是默认生效的
public class HttpEncodingAutoConfiguration {
    //通过这个类来导入到这个启动类 所有的属性都是来自这个类
     private final HttpEncodingProperties properties;
    //只有一个有参构造的情况下,参数的值就会从容器中拿
     public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
        this.properties = properties;
    }
    //从SpringIOC容器中添加一个bean组件,这个组件需要从properties中获取
       @Bean
    @ConditionalOnMissingBean({CharacterEncodingFilter.class})
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
        return filter;
    }
```

​	根据当前不同的条件判断 ,决定这个配置文件是否生效?

​		一旦这个配置生效;这个类就会去给容器中添加各种组件,这些组件的属性是从对应的properties中获取

的,这些类里面的每一个属性又是和配置文件绑定的;列如下面就是我们能配置的

```properties
#所有的可以配置的属性都是在xxxproperties中
spring.http.encoding.charset=utf-8
spring.http.encoding.force=true
而这些属性都是来自HttpEncodingProperties类中的属性
```



​		4.1 @EnableConfigurationProperties({HttpEncodingProperties.class})

​			4.1.2 所有在配置文件中能配置的属性都在xxxEncodingProperties中，配置文件能配置什么参考某个功能对应的这个属性类

```java
@ConfigurationProperties(
    prefix = "spring.http.encoding"
)//从配置文件中获取指定的值和bean的属性进行绑定,将配置文件中的对应的值和HttpEncodingProperties这个类绑定起来
public class HttpEncodingProperties {
```

##### 	5.精髓:

​		5.1 spring Boot启动会加载大量的自动配置类

​		5.2 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类

​		5.3 我们再来看这个自动配置类到底配置了哪些组件(只要我们要用的组件有,那么我们就不需要自己配置了)

​		5.4 给容器中自动配置类添加组件的时候,会从properties类中获取某些属性,我们就可以在配置文件中指定这些属性的值

## p12:配置-@Conditional&自动配置报告

​	1.@Conditional派生注解

​		1.1 作用必须是.@Conditional指定的条件成立,才给容器中添加组件,配置配置里面的所有内容才生效

![1585270449587](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585270449587.png)

​		1.2 **自动配置类必须满足一些条件下才会生效**

​	2.我们怎么知道哪些配置类生效:**自动配置报告**

​		2.1 我们可以配置debug=true 属性;来让控制台打印自动配置报告，这样我们可以很方便定位知道哪些生效了

```java
============================
CONDITIONS EVALUATION REPORT
============================


Positive matches:（自动配置类启用的）
-----------------

   AopAutoConfiguration matched:
      - @ConditionalOnProperty (spring.aop.auto=true) matched (OnPropertyCondition)

Negative matches:(自动配置类没有启用的)
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)

   AopAutoConfiguration.AspectJAutoProxyingConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'org.aspectj.weaver.Advice' (OnClassCondition)
```



# 			第三章:Spring Boot与日志

## p1日志-日志框架分类和选择

​	1.市面上的日志框架

​		1.2 JUL,jCL,Jboos-logging,log4j,log4j2,slf4j

 

| 日志门面(日志的抽象层)                                       | 日志实现                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ~~JCL ( Jakarta Commons Logging )~~ SLF4j ( Simple Logging Facade for java) ~~jboss-logging~~ | Log4j JUL ( java.til.logging )Facade for Java)  Log4j2  Logback |

​		1.3 左边选择一个门面(抽象层),右边来选一个实现;

​			1.3.1 抽象们面选择:SLF4j ( Simple Logging Facade for java)

​			1.3.2 日志实现选择:logback

​		1.2 SpringBoot:底层是Spring框架,Spring框架默认是用JCL;

​			1.2.1 SpringBoot选择的是SLF4j 和Logback

## p2:日志-slf4j使用原理

​	1.如何在系统中使用slf4j

​		1.1 以后在开发的时候,日志记录方法的调用,不应该直接调用实现类,而是调用日志抽象层的方法

​				给系统导入slf4j的jar包和logback的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

​	1.2 图示

![图示](E:\workJavaEE\Y2笔记\SpringBoot\images\concrete-bindings.png)

​	每一个日志的实现框架都是有自己的配置文件,使用slf4j以后,配置文件还是做成日志实现框架的配置文件;

## p3:日志-其他日志框架统一转换为slf4j

​	1.a ( slf4j+logback ) : Spring ( commons-logging )、Hibernate ( jboss-logging)、MyBatis、 xXX

​		1.1统一日志记录,即使是别的框架和我一起统一使用slf4j进行输出?

![legacy](E:\workJavaEE\Y2笔记\SpringBoot\images\legacy.png)

​	2**.如何让系统中所有的日志记录都统一slf4j?**

​		**2.1 将系统中的其他日志框架先排除出去**

​		**2.2 用中间包来替换原来框架的日志**

​		**2.3 我们导入其他slf4j其他的实现**

## p4:日志-SpringBoot日志关系

```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-starter</artifactId>
		</dependency>
```

​	1.SpringBoot使用它来做日志功能

```xml
	<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.2.5.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```

![1585280610947](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585280610947.png)

**总结**:

​	1.SpringBoot底层也是使用slf4j+logback的方式进行日志记录

​	2.SpringBoot也罢其他的日志都替换成了slf4j

​	3.中间的替换包? 包名都没改但是包里面的类都改变了  就可以偷梁换柱了

```java
public abstract class LogFactory {
    static String UNSUPPORTED_OPERATION_IN_JCL_OVER_SLF4J = "http://www.slf4j.org/codes.html#unsupported_operation_in_jcl_over_slf4j";
    static LogFactory logFactory = new SLF4JLogFactory();
```

![1585284253291](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585284253291.png)

​	4.如果我们引入了其他的框架，一定要这个框架的默认日志依赖移除掉？

​		4.1 Spring框架用的是commons-logging日志；SrpingBoot底层也移出了这个依赖

```xml
<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<exclusions>
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
```

​		4.2 **SpringBoot能 自动适配所有的日志,而且底层使用的是slf4j+logback的方式记录日志,引入其他的框架的时候,只需要这个框架依赖的日志框架排除掉**

## p5:日志-SpringBoot默认配置

 	1.SpringBoot默认帮我们配置好了日志

```java
	//记录器
	Logger logger= LoggerFactory.getLogger(getClass());
	@Test
	void contextLoads() {
		//日志记录级别
		//由低到高  trace<debug<info<warn<error
		//可以调整可以输入出的日志级别；如果调整了日志级别以后的高级别生效
		logger.trace("这是trace日志记录");
		logger.debug("这是debug日志记录");
		//SpringBoot默认使用的是info级别的，没有指定级别就用SpringBoot默认指定的级别用root级别的
		logger.info("这是info日志记录");
		logger.warn("这是warn日志记录");
		logger.error("这是error日志记录");
	}
```

​	2.输出格式参考:

```properties

 %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
日志输出格式：
        %d表示日期时间，
        %thread表示线程名，
        %-5level：级别从左显示5个字符宽度
        %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
        %msg：日志消息，
        %n是换行符
    -->
   
    %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n%d{yyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
```

​	3.slf4j的日志设置示例

```properties
#设置输出等级 如果没有达到等级则不会输出
logging.level.org.bt=warn
#如果没有磁盘  默认再来项目的路径下产生log文件
#logging.file.name=log.log
#如果指定了某个磁盘的文件夹  就在指定的文件夹产生文件
#logging.file.name=E:/log.log
#在当前根目录下创建爱你spring文件和里面的log文件夹,使用spring.log默认文件
logging.file.path=/spring/log
```

## p6:日志-指定日志文件和日志Profile功能

​	1.给路径下放上每个日志框架自己的配置文件即可;SpringBoot就不使用他默认配置了

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml` or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

​		1.1 lombok.xml:直接就被日志框架识别

​		1.2 lombok-spring.xml :日志框架不直接加载配置项,由SpringBoot,可以使用SpringBoot的高级Profile功能

```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
      可以指定某段配置只在某个环境下生效
</springProfile>
```

​		如：

```xml
<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
            %d表示日期时间，
            %thread表示线程名，
            %-5level：级别从左显示5个字符宽度
            %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
            %msg：日志消息，
            %n是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
```

​		1.3 如果使用logback.xml作为日志配置文件，还要使用profile功能，会有以下错误

```error
no applicable action for [springProfile]
```

# 		第四章:SpringBoot-Web开发

## p1:SpringBoot-Web开发-简介

​	1.创建SpringBoot应用,选中我们需要的模块；

​	2.SpringBoot已经默认将这些场景配置好了,只需要在配置文件中指定少量的配置即可运行起来、

​	3.自己编写逻辑代码

​	4.自动配置原理

​		4.1 这个场景SpringBoot帮我们配置了什么？能不能修改,能修改哪些配置,能不能扩展?xxx

```java
XXXXAutoConfiguration   给我们容器中自动配置组件
EnableConfigurationProperties 配置类来封装配置文件的属性
```

## p2:web开发-webjars&静态资源映射规则

​	1.SpringBoot对静态资源映射规则

```java
@ConfigurationProperties(
    prefix = "spring.resources",
    ignoreUnknownFields = false
)
public class ResourceProperties {
    //可以设置和静态资源相关的参数,缓存时间等
```



```java
//以jar包的形式引入静态资源
public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Integer cachePeriod = this.resourceProperties.getCachePeriod();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(cachePeriod));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(this.resourceProperties.getStaticLocations()).setCachePeriod(cachePeriod));
                }

            }
     
     //配置欢迎页配置  
     @Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(
ResourceProperties resourceProperties) {
return new WelcomePageHandlerMapping(resourceProperties . getwelcomePage(),
this . mvcProperties . getStaticPathPattern());
}
     
		//配置页面ioc 
     public static class FaviconConfiguration {
            private final ResourceProperties resourceProperties;

            public FaviconConfiguration(ResourceProperties resourceProperties) {
                this.resourceProperties = resourceProperties;
            }

            @Bean
            public SimpleUrlHandlerMapping faviconHandlerMapping() {
                SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
                mapping.setOrder(-2147483647);
                mapping.setUrlMap(Collections.singletonMap("**/favicon.ico", this.faviconRequestHandler()));
                return mapping;
            }

            @Bean
            public ResourceHttpRequestHandler faviconRequestHandler() {
                ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
                requestHandler.setLocations(this.resourceProperties.getFaviconLocations());
                return requestHandler;
            }
        }
```

​	2.所有/webjars/**,都去META-INF/resources/webjars/找资源;

​		webjars：以jar包的方式引入静态资源;正好符合约定  其他的依赖可以参考下面链接

​		https://www.webjars.org/

![1585300764912](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585300764912.png)

​		localhost:8080/webjars/jquery/3.3.1/jquery.js

​		2.1 依赖如下

```xml
<!--引入jquery文件-->
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>jquery</artifactId>
			<version>3.1.1</version>
		</dependency> 
```

​	3./**访问当前项目的任何资源(静态资源文件夹)

```xml
classpath: /META- INF/ resources/
classpath: /resources/
classpath:/static/
classpath:/public/
"/":当前项目的根路径
```

​		localhost:8080/abc====去静态资源文件夹里面找abc

​	4.欢迎页,静态资源文件夹下的所有index.html页面;

​		http://localhost:8080/  找index页面

​	5.**所有****/favicon.ico 都是在静态资源文件下找****

## p3:web开发-引入thymeleaf (模板引擎)

![1585308604129](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585308604129.png)

​	1.SpringBoot推荐的thymeleaf 

​		1.1 语法更简单,功能更强大

 		1.2 引入jar包

```xml
	<!--引入模板引擎-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
	<!--使用这种方式来切换springboot默认的版本配置-->
<properties>
 	 <thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
        <thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
</properties>
```

## p4:web开发-thymelaef的语法

```java
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING = Charset.forName("UTF-8");
    private static final MimeType DEFAULT_CONTENT_TYPE = MimeType.valueOf("text/html");
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    //
```

​	1.只要我们把html放在classpath:/templates/,那么thymeleaf就可以生效

​	2.使用:

​		2.1 导入thymaleaf的空间

```haml
<html xmlns:th="http://www.thymeleaf.org">
```

​		2.2 使用thymeleaf的语法

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>成功</h1>
<div th:text="${hello}"></div>
</body>
</html>
```

​	3. 语法规则

​		3.1 th:text;改变当前元素的文本内容;

​		3.2 任意html属性;来替换原生的属性的值

![1585312626403](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585312626403.png)

​		3.3 我们能写那些表达式

```properties
Simple expressions:（表达式语法）
    Variable Expressions: ${...}  获取变量值
    1.可以获取对象的属性,调用方法
    2.使用内置的基本对象
        #ctx:thecontextobject.
        #vars:thecontextvariables.
        #locale:thecontextlocale.
        #request:(onlyinWebContexts)theHttpServletRequestobject.
        #response:(onlyinWebContexts)theHttpServletResponseobject.
        #session:(onlyinWebContexts)theHttpSessionobject.
        #servletContext:(onlyinWebContexts)theServletContextobject.
        
        ${session.foo}
      3.内置的一些工具对象(此处就不写了  可以参照官方文档第四章的第二小节)
      
    Selection Variable Expressions: *{...} 选择表达式 ,和${}功能是一摸一样的；
    	1.补充功能配合<tx:object="${session.user}">:
    	<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
		</div>
		
    Message Expressions: #{...} //获取国际化内容的
    
    Link URL Expressions: @{...} //定义url 
    	1.@{/order/process(execId=${execId},execType='FAST')}
    	
    Fragment Expressions: ~{...} //片段引用表达式
	1.<div th:insert="~{commons :: main}">...</div>

Literals(字面量)
    Text literals: 'one text' , 'Another one!' ,…
    Number literals: 0 , 34 , 3.0 , 12.3 ,…
    Boolean literals: true , false 
    Null literal: null 
    Literal tokens: one , sometext , main ,…
Text operations:(文本操作) 
    String concatenation: + 
    Literal substitutions: |The name is ${name}|
Arithmetic operations: (数学计算)
    Binary operators: + , - , * , / , % 
    Minus sign (unary operator): -
Boolean operations: (布尔运算)
    Binary operators: and , or 
    Boolean negation (unary operator): ! , not
Comparisons and equality: （比较运算）
	Comparators: > , < , >= , <= ( gt , lt , ge , le ) 
    Equality operators: == , != ( eq , ne )
Conditional operators: (条件运算)(三目运算符)
    If-then: (if) ? (then) 
    If-then-else: (if) ? (then) : (else) 
    Default: (value) ?: (defaultvalue)
Special tokens:()()
    No-Operation: _
```

## p5:web开发-SpringMVC自动配置原理

​	1.[SpringBootWeb开发文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications)

​	2. Spring MVC 自动配置原理

​		2.1SpringBoot已经配置好了SpringMVC

​		2.2 以下是SpringBoot对SpringMVC的默认配置:		

The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  - 自动配置了ViewResolver(视图解析器;根据方法的返回值得到视图对象(view)，视图对象决定如何渲染(转发？重定向?))
  - ContentNegotiatingViewResolver:组合所有的视图解析器
  - 如何定制:我们可以自己给容器中添加一个视图解析器；自动组合进来

- Support for serving static resources, including support for WebJars (see below).静态资源文件夹路径webjars

- 自动注册of `Converter`, `GenericConverter`, `Formatter` beans.

  - Converter:转换器 ;public String hello(User user);类型转换使用
  - Formatter` :格式化器;2017-02-10====Date

  ```java
    	 @Bean
          @ConditionalOnProperty(
              prefix = "spring.mvc",
              name = {"date-format"}
          )
          public Formatter<Date> dateFormatter() {//在配置文件中配置日期格式化的规则
              return new DateFormatter(this.mvcProperties.getDateFormat());//日期格式化组件
          }
  ```

  ​	自己添加的转换器,我们只需要放入容器中即可

  ```java
  public class MyCConvter implements Converter<String,Boolean>{
  
      @Override
      public Boolean convert(String s) {
          if (s=="true"){
              return true;
          }
          return false;
      }
  }
   @Bean
      public MyCConvter getConvter(){
          return new MyCConvter();
      }
  ```

  

- Support for `HttpMessageConverters` (see below).

  - HttpMessageConverter` ：SpringMVC用来转换Http请求和响应的:user-json
  - HttpMessageConverters 是从容器中确定;获取所有的HttpMessageConverter；
  - 自己给容器中添加HttpMessageConverter，只需要将自己的编写组件放入容器中即可

- Automatic registration of `MessageCodesResolver` (see below).

  - 定义代码生成规则的

- Static `index.html` support. 静态首页访问

- Custom `Favicon` support (see below). favicon.ico 

- Automatic use of a `ConfigurableWebBindingInitializer` bean (see below).

  - 我们可以自己配置一个ConfigurableWebBindingInitializer放入容器  因为是从容器中拿的

  ```java
  如果我们没有配置  他是从WebDataBinder初始化：
  请求数据----》javaBean
  ```

  org.springframework.boot.autoconfigure.web

If you want to keep Spring Boot MVC features, and you just want to add additional [MVC configuration](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/htmlsingle#mvc) (interceptors, formatters, view controllers etc.) you can add your own `@Configuration` class of type `WebMvcConfigurerAdapter`, but **without** `@EnableWebMvc`. If you wish to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter` or `ExceptionHandlerExceptionResolver` you can declare a `WebMvcRegistrationsAdapter` instance providing such components.

If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`.		

## p6:web开发-扩展与全面接管SpringMVC

​	1.SpringMVC扩展

```xml
<mvc:view-controller path="/hello" view-name="success"/>
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
           <bean></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

​		1.2.编写一个配置类（@Configuration）,是WebMvcConfigurerAdapte的子类,不能标注@EnableWebMVc

​	既保留了我们自己配置的  有保留了自动配置的

```java
//使用@Configuration声明配置类
//通过继承WebMvcConfigurerAdapter可以扩展SpringMVC
@Configuration
public class MySpringMVCConfig extends WebMvcConfigurerAdapter {

    //重写自己想实现的功能  ctrl+o
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/sb").setViewName("success");
    }
}
```

​		1.3原理 为什么自己的扩展配置和SpringBoot的配置不会冲突 因为他也继承了WebMvcConfigurerAdapter

​			1.3.1.WebMvcAutoConfiguration示意个SpringMVC的自动配置类

​			1.3.2.在做其他的配置时会导入;@Import(EnableWebMvcConfiguration.class)

```java
/**
	 * Configuration equivalent to {@code @EnableWebMvc}.
	 */
	@Configuration
	public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration {
        
	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();

	//把所有的configurers赋值到WebMvcConfigurerComposite里面
	@Autowired(required = false)
	public void setConfigurers(List<WebMvcConfigurer> configurers) {
		if (!CollectionUtils.isEmpty(configurers)) {
			this.configurers.addWebMvcConfigurers(configurers);
            //一个参考实现，将所有的WebMvcConfigurer都调一遍,实现一起起作用 SpringBoot+自己的配置
         //   	@Override
		//public void addViewControllers(ViewControllerRegistry registry) {
		//for (WebMvcConfigurer delegate : this.delegates) {
		//	delegate.addViewControllers(registry);
		//	}
		//	}
		}
	}
```

​		1.4.将所有的WebMvcConfigurer都调一遍,实现一起 起作用 SpringBoot+自己的配置 

​			效果:SpringMVC的自动配置和我们扩展配置都会起作用

​	2.全面接管SpringMVC

​		2.1 SpringBoot对SpringMVC的自动配置就不需要了,所有的配置都是自己来写	;所有的SpringMVC模块配置都失效

​		2.2 我们需要在配置类中添加@EnableWebMvc即可

​	3.如何修改SpringBoot的默认配置

​		3.1 模式:

​			3.1.1 SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的(@Bean。@Component)，如果有就用用户配置的,如果没有,才执行自动配置,如果有些组件可以有多个(ViewResolver)将用户的配置和自己默认的配置组合起来

​			3.1.2 在SpringBoot中会有非常多的xxxConfigurer版主我们进行扩展

## p7:web开发-【实验】-引入资源

​	1.默认访问首页

```java
  //我们自己自己添加一个组件
    @Bean
    public WebMvcConfigurerAdapter webMvcConfigurerAdapter(){
        WebMvcConfigurerAdapter webMvcConfigurerAdapter=new WebMvcConfigurerAdapter() {
            @Override
            public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/").setViewName("login");
                registry.addViewController("/login.html").setViewName("login");
            }
        };
        return webMvcConfigurerAdapter ;
    }
```

​	2.通过webjars的引入bootstrap文件

```xml
	<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>bootstrap</artifactId>
			<version>4.0.0</version>
		</dependency>
```

​	3.使用thymeleaf来访问webjars  规则就是webjars/任意路径

```html
<link  th:href="@{/webjars/bootstrap/4.0.0/css/bootstrap.css}"  rel="stylesheet">
```

## p8:web开发-【实验】-国际化

​	1.编写配置文件 在Resources创建文件 --》i18n-->自定义一个默认的xxx.properties-->之后分别创建

​		xxx_zh_CN.properties(中文的)   XXX_en_US.properties(英文的) 

![1585397672264](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585397672264.png)

​	2.SpringBoot自动配置好了管理国际化资源文件的组件

```java
@ConfigurationProperties(prefix = "spring.messages")
public class MessageSourceAutoConfiguration {
    	@Bean
	public MessageSource messageSource() {
        /**
	 * Comma-separated list of basenames, each following the ResourceBundle convention.
	 * Essentially a fully-qualified classpath location. If it doesn't contain a package
	 * qualifier (such as "org.mypackage"), it will be resolved from the classpath root.
	 */
        //这个就是基础名称  如果我们的基础名不一致  需要配置
	private String basename = "messages";
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		if (StringUtils.hasText(this.basename)) {
           //设置国际化资源文件的基础名(去掉国家语言代码的 )
			messageSource.setBasenames(StringUtils.commaDelimitedListToStringArray(
					StringUtils.trimAllWhitespace(this.basename)));
		}
		if (this.encoding != null) {
			messageSource.setDefaultEncoding(this.encoding.name());
		}
		messageSource.setFallbackToSystemLocale(this.fallbackToSystemLocale);
		messageSource.setCacheSeconds(this.cacheSeconds);
		messageSource.setAlwaysUseMessageFormat(this.alwaysUseMessageFormat);
		return messageSource;
	}
```

​	3.设置基础名称

```properties
#设置指定的国际化文件
spring.messages.basename=i18n/login
```

​	4.然后直接在html取值

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">
		<title>Signin Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
		<link  th:href="@{/webjars/bootstrap/4.0.0/css/bootstrap.css}"  rel="stylesheet">
		<!-- Custom styles for this template -->
		<link  th:href="@{/asserts/css/signin.css}" rel="stylesheet">
	</head>
	<body class="text-center">
		<form class="form-signin" action="dashboard.html">
			<img class="mb-4" th:src="@{/asserts/img/bootstrap-solid.svg}"  alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}"></h1>
			<label class="sr-only" th:text="#{login.username}"></label>
			<input type="text" class="form-control" th:placeholder="#{login.username}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.password}"></label>
			<input type="password" class="form-control" th:placeholder="#{login.password}" required="">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me"> [[#{login.remember}]]
        </label>
			</div>
			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
			<a class="btn btn-sm">中文</a>
			<a class="btn btn-sm">English</a>
		</form>

	</body>

</html>
```

​	4.效果  就是根据浏览器的语言  显示国际化

​	5.如何实现点击中文就是中文 点击英文就是英文  

​		5.1 原理:

​				国际化Locale(区域信息对象):LocaleResolver（获取区域对象）

```java
	@Bean
		@ConditionalOnMissingBean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")
		public LocaleResolver localeResolver() {
			if (this.mvcProperties
					.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.mvcProperties.getLocale());
			}
			AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
			localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
			return localeResolver;
		}
//默认的就是根据请求头带来的区域信息Locale进行国际化

```

​	6.点击连接切换国际化 

```java
package org.bt.component;


import org.springframework.util.StringUtils;
import org.springframework.web.servlet.LocaleResolver;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

/**
 * 我们可以再连接上戴上连接信息
 */
public class MyLocaleReolver implements LocaleResolver{
    //完成之后需要注入到容器
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        //获取请求信息带来的参数
        String l=request.getParameter("l");
        //定义locale对象返回
        //如果是空的  急用系统默认的
        Locale locale=Locale.getDefault();
        if (!StringUtils.isEmpty(l)){
            String[] split = l.split("_");
            locale=new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}
  //将自己写的国际化注入容器  覆盖系统配置的
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleReolver();
    }
```

