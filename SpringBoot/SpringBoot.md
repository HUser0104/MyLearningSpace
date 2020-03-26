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
        <dependency>
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

# 			第三章:Spring Boot与日志

