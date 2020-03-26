# 			第一章:Spring Boot入门

## p1:Spring Boot_入门简介

​	1.Spring Boot来简化Spring开发,约定大于配置,去繁从简,just run就能创建一个独立的,产品级别的应用

​	2.Spring Boot的一些优点

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

​	1.你必须掌握以下内容:

​		1.1 - Spring框架的使用经验

​		1.2 -熟练使用Maven进行项目构建和依赖管理

​		1.3 -熟练使用Eclipse或者IDEA

​	2.环境约束

​		2.1 -jdk1.8 : Spring Boot 1.7及以上

​		2.2 -maven3.x

​		2.3 -IntellijIDEA2017

​		2.4 -SpringBoot 1.5.9.RELEASE

​	3.maven的配置

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

​	4.idea配置maven

​		4.1 打开idea点击setting

![1585046404633](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585046404633.png)

​		4.2 配置本地自己的maven路径

![1585046502669](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585046502669.png)

## p4:springboot-helloworld

​	1.创建一个maven工程(jar)

​	2.导入springboot依赖

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

​	3.编写主程序启动SpringBoot  

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

​	4.编写业务  controller service ....

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

​	5.运行主程序测试

​	6.简化部署 打包

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

​	1.pom文件

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

​	2.启动器

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

​	1.主程序类丶主入口类

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

​	1.选择Spring Initializer---->然后选择好自己的jdk--->点击next

![1585106112776](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106112776.png)

​	2.编辑信息

​	![1585106557883](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106557883.png)

​	3.选择模块  springweb为例  后期可以自己想要什么功能就勾选什么功能 比如安全的  缓存的  等等 

![1585106677182](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585106677182.png)

 	4.默认生成的SpringBoot项目

​		4.1 主程序已经写好了,我们只需要写我们自己的业务逻辑

​		4.2 resources文件夹中目录结构

​			4.2.1 static:保存所有的静态文件;列如：css丶js丶images

​			4.2.2 templates:保存所有的模板页面;(SpringBoot默认jar包使用嵌入式的tomcat,不支持jsp页面);可以使用			模板引擎

​			4.2.3 :application.properties:Spring Boot应用的配置文件;可以修改一些默认配置;列如端口号

# 			第二章:Spring Boot配置

## p1:配置-yaml简介

​	1.SpringBoot使用一个全局配置文件;配置文件名字是固定的

​		1.1 application.properties

​		1.2 application.yml

​	2.配置文件的作用:修改SpringBoot的默认配置值:SpringBoot在底层都给我们自动配置好;

​	3.YAML (YAML Ain'Markup Languge)

​		3.1 YAML  A Markup Languge:是一个标记语言

​		3.2 YAML isn't Markup Languge:不是一个标记语言

​	4.标记语言

​		4.1 以前配置文件:大多数都是使用xxx.xml

​		4.2 YMAL: 以数据为中心,比json丶xml等更适合作为配置文件

​		4.3 yaml配置例子

```yaml
server:
  port: 1024
```

## p2::配置-yaml语法

​	1.基本语法

​		1.1 k v: 值:表示key---value(空格必须要有的)

​		1.2 以**空格**的缩进来控制层级关系;只要是左对齐的一列数据,都是同一一个层级的

```yaml
server:
 port: 1024
 path: /hello
```

​			1.2.1 属性对大小写很敏感的;也是把空格挖玩到极致的男人

​	2.值的写法

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

​			2.3.2 行内写法

```yaml
pets: [cat,dog,pig]
```

## p3:配置-yaml配置文件值获取

​	1.配置文件

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

​	2.javaBean

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

​	4.我们可以导入一个配置处理器

```xml
<!--        导入配置文件处理器,配置文件进行绑定就会有提示-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
       
```

## p4:配置-properties配置文件编码问题

​	1.properties实现proson

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

​	2.注意如果出现中文乱码问题需要手动设置 设置步骤如下

​		2.1 setting-----file *encoding*----Default encoding for properties files 改成utf-8----勾选Transparent native- to- ascii conversion即可解决

## p5:配置-@ConfigurationProperties与@Value区别

​	1.

|                | @ConfigurationProperties | @Value     |
| -------------- | ------------------------ | ---------- |
| 功能           | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定       | 支持                     | 不支持     |
| SpEL           | 不支持                   | 支持       |
| JSR303数据校验 | 支持                     | 不支持     |
| 复杂类型       | 支持                     | 不支持     |

​	2.配置文件yaml还是properties都能获取到值

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

​	3.配置文件注入数据校验

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

p10:配置-外部配置加载顺序

​	1.命令行参数

```cmd
java -jar.\spring-boot02-config02-0.0.1-SNAPSHOT.jar --server.port=8888 --server.servlet.context-path=/abc

多个配置用空格分开 --参数名称
```

​	**由jar包外想jar包内进行寻找;**

​	**优先加载待profile的**

​	2.jar包外部的application-{profile}.properties或application.ym(带spring.profile)配置文件

​	3.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件

​	**再加载不带profile的**

​	4.jar包外部的application.properties或application.yml(不带spring.profile)配置文件

​	5.jar包内部的application.properties或application.yml(不带spring.profile)配置文件

[参考文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-external-config)

### p:10配置-自动配置原理

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

# 			第三章:Spring Boot与日志

