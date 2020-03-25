### 课时1:SpringMVC环境搭建及第一个程序

.1)第一个SpringMVC程序

​	1.servirt-----------SpringMVC

​	2.常用的头文件

```xml
 xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
  http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
```

​	3.流程

​		3.1 普通的servlet流程

```xml
 <servlet>
    <servlet-name>servlet</servlet-name>
    <servlet-class>net.bt.servlet.TestServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>servlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

​			3.1.1 请求 <url-pattern>-交给对应的servlet处理

​		3.2 如果现在想用SpringMVC，而不是普通的servlet,如何告知程序？-如何让springMVC介入程序:

​			3.2.1 需要在web.xml配置SpringMVC自带的Servlet   交给SpringMVC处理  

```xml
 <servlet>
    <servlet-name>springmvc</servlet-name>
    <!--servlet-class中的值是spring-webmvc包提供的类，即前端控制器，用于控制所有请求 -->
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springMVC.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <!--url-pattern（重点）中有3个值，分别为/、 /*、 *.action  -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

​				3.2.1.1 /是拦截所有的请求     如果想拦截指定的请求/user/xxxx

​			3.2.2 在springmvc中配置如下  注解的方式

```xml
<!--        配置扫描器-->
    <context:component-scan base-package="net.bt.handler"/>

    <!--    配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/views/">
        </property>
        <property name="suffix" value=".jsp">
        </property>
    </bean>
```

​			3.2.3 编写servlet类

```java
package net.bt.handler;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class TestServlet {

    @RequestMapping("we")
    public String welcome(){
        return "success";
    }
}

```

### 课时2:RequestMapping映射及个属性

.1 )RequestMapping映射及个属性

​	1.映射  去匹配@RequestMapping注解,可以和方法名类名不一致   访问就是cc/we

```java
package net.bt.handler;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("cc")
public class TestServlet {

    @RequestMapping("we")
    public String welcome(){
        return "success";
    }
}

```

​	2.默认使用了请求转发的跳转方式

​	3.@RequestMapping中的属性method   处理请求方式处理(post,get,put,delete)

​		3.1 只处理post请求

```java
@RequestMapping(value = "we",method = RequestMethod.POST)
```

​		3.2 只处理get请求

```java
@RequestMapping(value = "we",method = RequestMethod.GET)
```

​	4.@RequestMapping中的属性params  处理请求中必须包含某个参数值

```xml
  @RequestMapping(value = "we",method = RequestMethod.POST,params = {"name",age!=23})
```

​		4.1 必须参数必须有name并且age的不能等于23   列如

```jsp
<a href="cc/we?name=zs">faqiqingqiu</a>
```

​	5.ant风格的请求路径

​		5.1 ?:单个字符      *任意个字符(0或多个字符)          ** 任意目录

```xml
  @RequestMapping(value = "**/we1")
    public String welcome1(){
        return "success";
    }
```

​			5.1 .2 请求时路径这么写

```xml
<a href="cc/ashhasaasasasasq/we1">faqiqingqiu</a>  《!--中间任意目录都可以  只要首尾符合就可以-->
```

​	6.如何接受html/jsp发送过来的数据  方式一

​		6.1 假如前端要传过来一个zs   可以这样写

```jsp
<a href="cc/we2/zs">faqiqingqiu</a>
```

​		6.2 在springmvc如何接收呢 

```java
   @RequestMapping(value = "we2/{name}")
    public String welcome2(@PathVariable("name")String name){
        System.out.println(name);
        return "success";
    }
```

​	7..如何接受html/jsp发送过来的数据  方式二

​		7.1 假如前端要传过来一个参数   可以这样写

```html
<a href="cc/we3?uname=张三">请求</a>
<form action="cc/we3">
    <input type="text" name="uname">
    <input type="submit" />
</form>
```

​		7.2  在springmvc如何接收呢 

```java
 	@RequestMapping(value = "we3")
    public String welcome3(@RequestParam("uname") String name){
        System.out.println(name);
        return "success";
    }
```

​			7.2.1  通过@RequestParam(传过来的参数) 获取   然后在赋值给name形参   就可以拿到 等价于request.getParameter()  

​			7.2.2 加入前端传过来的少了参数 而我接收的多了   如何避免报错

```java
    @RequestMapping(value = "we3")
    public String welcome3(@RequestParam("uname") String name,@RequestParam(value = "uage",required = false,defaultValue = "20")Integer age){
        System.out.println(name);
        return "success";
    }
```

​				required = false参数可有可无   defaultValue = "20"默认值

### 课时3:Result风格请求

.1)Result风格:软件编程风格

​	1.springmvc:GET查    POST 增       DELETE删          PUT改

​	2.普通浏览器 只支持get   post方式

​	3.如何解决不支持DELETE,PUT的问题呢   使用过滤器

​		3.1 但是需要满足条件 约定   才能够被过滤改成PUT|DELETE

```html
<input type="hidden" name="_method" value="DELETE|PUT"/>
```

​		3.2 并且请求方式必须是post

​		3.3 配置web.xml

```xml
  <!--  过滤请求-->
  <filter>
    <filter-name>filter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>filter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

### 课时4:如何获得头信息,获取cookie,使用实体类传送数据自动装配

.1)通过@RequestHeader(头信息的key)注解实现

​	1.实现如下

```java
    @RequestMapping(value = "requestHeader")
    public String requestHeader(@RequestHeader("Accept-Language")String header){
        System.out.println(header);
        return "success";
    }
```

​		1.1 获取头信息的Accept-Language的值   并且将值赋值给header变量中

.2)获取cookie

​	1.通过mvc获取cookie的值(JSESSIONID)

```java
@RequestMapping(value = "cookie")
    public String cookie(@CookieValue("JSESSIONID")String cookie){
        System.out.println(cookie);
        return "success";
    }
```

​		1.1 通过@CookieValue获取到值   赋值给cookie变量

​	

​	2.小结:SpringMVC处理各种参数的流程/逻辑:

​		2.1 请求:   前端发起请求--->@RequestMapping--->处理请求中的参数

.3)使用实体类传送数据

​	1.如何自动装配   前提条件就是符合约定   参数的名称必须和类中的属性一摸一样  (支持级联属性)

```jsp
<form action="cc/testStudent" method="get">
    学生名称<input name="name"/>
    家庭住址<input name="address.homeAddress"/>
    学校地址<input name="address.schoolAddress"/>
    <input type="submit" value="提交">
</form>
```

```java
 @RequestMapping(value = "testStudent")
    public String cookie(Student student){
        System.out.println("学生名称"+student.getName()+",学生地址"+student.getAddress().getSchoolAddress()+",家庭住址"+
                student.getAddress().getHomeAddress());
        return "success";
    }
```

### 

### 课时5:处理模型数据(传送数据到页面)及ModelAttribute等注解的使用

.1) 如果需要跳转时需要带数据:V,M,则可以使用以下方式:

​	1.ModelAndView，ModelMap,Map,Model---->放在了request作用域

​		1.1 ModelAndView如何使用

```java
    @RequestMapping(value = "modelAndView")
    public ModelAndView modelAndView(){
//        这个构造参数传递想要跳转的页面   视图的构建
        ModelAndView modelAndView=new ModelAndView("success");
        Student student=new Student();
        student.setName("张三");
        //等价于request.setAttribute  
        modelAndView.addObject("stu",student);
        return modelAndView;
    }

//在前端接收 示例通过el表达式接收
${stu.name}
```

​		1.2 ModelMap,Map,Model的使用是都差不多的

```java
    @RequestMapping(value = "ModelMap")
    public String testtestModelMap(ModelMap modelMap){
        Student student=new Student();
        student.setName("张三");
        //等价于request.setAttribute
        modelMap.put("stu",student);
        return "success";//视图
    }
@RequestMapping(value = "Map")
    public String testtestModelMap(Map<String,Object> m){
        Student student=new Student();
        student.setName("张三");
        //等价于request.setAttribute
        m.put("stu",student);
        return "success";//视图
    }
@RequestMapping(value = "Model")
    public String testtestModelMap(Model mo){
        Student student=new Student();
        student.setName("张三");
        //等价于request.setAttribute
        mo.add("stu",student);
        return "success";//视图
    }
```

​	2.@SessionAttributes，@ModelAttribute

​		2.1 如何将一个数据放入request同时也放入session中  @SessionAttributes

​			2.1.1 需要在类的上面加上这个注解@SessionAttributes 这个注解里面有些参数  解释如下

​				2.1.1.1 如果要在request中存放stu对象,则同时放入session ( 按照key来找)

```java
@SessionAttributes(value="stu")
如果要放多个
@SessionAttributes(value={"stu","xxxx"})
```

​				2.1.1.2 如果要在request中存放Student类型的对象,则同时放入session ( 按照类型来找)

```java
@SessionAttributes(types = Student.class)
如果要放多个
@SessionAttributes(types={Student.class,xxx.class})
```

​		2.2 i.经常在更新的时候使用   @ModelAttribute  查询+修改

​			 ii.在不改变原来的代码   增加一个新方法  

​			 iii.但是每次请求该类之前都会执行

​			2.2.1 通过@ModelAttribute修饰的方法.会在每次请求先执行;并且该方法的参数map.put()可以将对象放入即将查询的参数中  必须满足的约定  map.put(k,v)其中k必须是吉祥查询的方法参数类型的首字母小写

```java
   @ModelAttribute
    public void queryStudent(Map<String,Object> map){
        Student student=new Student();
        student.setName("zs");
        map.put("student",student);
    }
```

```java
    @RequestMapping(value = "ModelAttribute")
    public String testtestModelMap(Student student,ModelMap modelMap){
        System.out.println(student);
        modelMap.put("s",student);
        return "success";//视图
    }
```

### 课时6:SpringMVC视图解析器常见功能、处理静态资源、类型转换

.1)InternalResourceViewResolver其他的一些功能

​	1.如何实现直接跳转   index.jsp-------->success.jsp

​		1.1 通过<mvc:view-controller path="前端请求的路径" view-name="返回的路径"/>标签来实现

```xml
<mvc:view-controller path="cc/testMvcViewCont" view-name="success"/>
```

​		1.2 前端示例代码

```jsp
<a href="cc/testMvcViewCont">testMvcViewCont</a>
```

​		3.3 如果使用前面的标签会屏蔽掉Controller里面的RequestMaping("a") 如何共存呢?

​			3.3.1 需要加入一个注解解决:

```xml
<!--    该标签是mvc的基础配置,很多功能都需要该标签来协调-->
    <mvc:annotation-driven/>
```

​	2.如果不用mvc拼接自己指定跳转方式:需要注意这种方式不会被视图解析器所加上前缀和后缀

​		2.1 在处理类中编写    请求转发:forward:                      重定向:redirect:

```java
 @RequestMapping(value = "we",method = RequestMethod.GET,params = {"name"})
    public String welcome(){
        return "forward:/test/test.jsp";
    }
```

.2)怎么处理静态资源

​	1.在SpringMVC中,如果直接访问静态资源:404。

​		1.1 原因:之前将所有的请求  通过通配符"/“拦截,进而交给SpringMVC入口DispatcherServlet去处理;找该请求映射对应的@RequestMapping

http://localhost:1024/SpringMVCProjectCC/pin.png

​	2.如何解决:如果是需要mvc处理的,则交给@RequestMapping("img.png")处理;如果不需要springmvc处理,则使用tomcat默认servilet去处理

​		2.1 实现方法 只需要添加二个配置即可 (在SpringMVC.xml)

```xml
<!--    改配置 会在SpringMVC 接收到一个请求 没有对应的@RequestMapping时,将该请求交给默认的Servlet处理(直接访问)-->
    <mvc:default-servlet-handler/>
 	<mvc:annotation-driven/>
```

.3)类型转换器

​	1.springmvc自带了一些转换器  比如前端传过来一个int数字   可以用String接收一个用Integer接收

​	2.如何自定义转换器

​		2.1 编写自定义转换器的类(实现一个Converter接口)

```xml
package net.bt.converter;

import net.bt.pojo.Student;
import org.springframework.core.convert.converter.Converter;

public class MyConverter implements Converter<String, Student> {

    /**
     * 前端传过来的数据转成Student  列如1-zh-22
     * @param s 前端传过来的数据
     * @return 一个学生
     */
    public Student convert(String s) {
        //把这个s才分
        String [] studentStrArr = s.split("-");
        //创建对象
        Student student=new Student();
        student.setId(Integer.parseInt(studentStrArr[0]));
        student.setName(studentStrArr[1]);
        student.setAge(Integer.parseInt(studentStrArr[2]));
        return student;
    }
}
```

​		2.2 编写配置(StringMVC.xml)  分为三大步

```xml
<!--        配置扫描器-->
    <context:component-scan base-package="net.bt.handler"/>

<!--    1自定义转换器纳入ioc容器中-->
    <bean id="myConverter" class="net.bt.converter.MyConverter"/>
<!--    2将myConverter再纳入SpringMVC提供的转换器bean-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myConverter"/>
            </set>
        </property>
    </bean>
<!--    3将conversionService注册到annotation-driven中-->
    <mvc:annotation-driven conversion-service="conversionService"/>
```

​		2.3 测试

​			2.3.1 前端代码

```jsp
<form action="cc/testConverter">
    <input type="text" name="converterInfo"/>
    <input type="submit" value="转换"/>
</form>
```

​			2.3.2 控制器代码

```java
  @RequestMapping("testConverter")
    public String testConverter(@RequestParam("converterInfo") Student student){
        System.out.println(student.getId()+","+student.getName()+","+student.getAge());
        return "success";
    }
```

​				其中(@RequestParam("converterInfo")是触发转换器的桥梁

### 课时7:数据格式化

.1)数据格式化

​	1.列如SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd hh:mm:ss")

​	2.SpringMVC提供了很多注解,方便我们数据格式化

​		2.1 实现步骤

​			2.1.1 配置Spring.xml

```xml
<!--    配置  数据格式化 注解 所依赖的bean-->
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean"/>
```

​			2.1.2 在属性上面加上注解即可

```xm
 @DateTimeFormat(pattern ="yyyy-MM-dd")
    private Date day;
```

​	3.FormattingConversionServiceFactoryBean这个bean可以实现类型转换也可以数据格式化

```xml
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myConverter"/>
            </set>
        </property>
    </bean>
```

### 课时8:错误消息处理、数据校验

.1)错误消息处理

public String testDateTimeFormat (Student student， BindingResult result , Map<String, Object> map)

​	1.需要验证的数据是Student中 的birthday, SPringMVC要求 如果校验失败  则将错误信息 自动放入该对象之后紧挨着的BindingResult result之间不能有其他参数。、

​	2.显示，则可以将错误消息对象放入request域中， 然后在jsp中从request中获取。

```java
  @RequestMapping(value = "dateTimeFormat")
    public String dateTimeFormat(Student student, BindingResult result , Map<String, Object> map){
      System.out.println(student.getDay());
//      获取错误信息
      List<FieldError> allErrors = result.getFieldErrors();
      map.put("errors",allErrors);
      return "success";
    }

//前端
<form action="cc/dateTimeFormat" method="get">
    学号:<input name="id"/>
    学生名称<input name="name"/>
    年龄:<input name="age"/>
    生日:<input name="day"/>
    <input type="submit" value="提交">
</form>
```

.2)数据校验

​	1.hibernate-validator能干的一些事情

```xml
Bean Validation 中内置的 constraint         
@Null   被注释的元素必须为 null    
@NotNull    被注释的元素必须不为 null    
@AssertTrue     被注释的元素必须为 true    
@AssertFalse    被注释的元素必须为 false    
@Min(value)     被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@Max(value)     被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@Size(max=, min=)   被注释的元素的大小必须在指定的范围内    
@Digits (integer, fraction)     被注释的元素必须是一个数字，其值必须在可接受的范围内    
@Past   被注释的元素必须是一个过去的日期    
@Future     被注释的元素必须是一个将来的日期    
@Pattern(regex=,flag=)  被注释的元素必须符合指定的正则表达式    
    
Hibernate Validator 附加的 constraint    
@NotBlank(message =)   验证字符串非null，且长度必须大于0    
@Email  被注释的元素必须是电子邮箱地址    
@Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
@NotEmpty   被注释的字符串的必须非空    
@Range(min=,max=,message=)  被注释的元素必须在合适的范围内
```

​	2.hibernate-validator实现步骤：

​		2.1.导入jar包

```xml
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
```

​		2.2 配置

```xml
<!--    配置支持注解-->
    <mvc:annotation-driven/>
```

​		2.3 在实体类中加上注解 具体的一些注解参考前面的 

```java
  @Past
    private String birthday;
```

​		2.4 在控制类中编写  @Valid 表示需要校验的实体

```java
 @RequestMapping("weFormats")
    public String weFormats(@Valid Student student, BindingResult result){
        List<FieldError> fieldErrors = result.getFieldErrors();
        FieldError fieldError = fieldErrors.get(0);
        System.out.println( fieldError.getDefaultMessage());
        System.out.println(student.getBirthday());
        return "success";
    }
```

### 课时9:SpringMVC通过Ajax处理json**数据**

.1)Ajax请求SpringMVC,并且返回json格式

​	1.引入相应的jar

```xml
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
```

​	2.编写前台

```xml
<script type="text/javascript" src="js/jquery-1.12.4.js"></script>
<script type="text/javascript">
    function testJson() {
        $.ajax({
            url:"testJson",
            type:"post",
            data:{"name":"zs"},
            dataType:"json",
            success:function (result) {
                alert(result)
            }
        })
    }
</script>
```



​	3.在控制器类中用@ResponseBody修饰会返回一个json数组的形式返回给前台   说明这个方法返回的不是一个view而是一个ajax请求

```java
	@ResponseBody
    @RequestMapping("testJson")
    public  List<Student> testJson(){
        //模拟三层
        Student student=new Student();
        student.setBirthday("2020-10-10");
        Student student1=new Student();
        student1.setBirthday("2020-10-10");
        Student student2=new Student();
        student2.setBirthday("2020-10-10");
        List<Student> list=new ArrayList<>();
        list.add(student);
        list.add(student1);
        list.add(student2);
        return list;
    }
```

### 课时10:SpringMVC实现文件上传、拦截器

.1) SpringMVC实现上传文件和Servlet的本质是一样的 ，都是通过commons-io.jar和commons-fileupload.jar

springMVC可以简化文件上传的代码,但是必须瞒足条件:实现MultiparResolver接口；w而该接口的实现类SpringMVC已经提供了CommonsMultiparResolver

​	1.具体步骤:

​		1.1 导入jar包

```xml
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
```

​		1.2 在SpringMVC.xml配置实现类  注意CommonsMultipartResolver里面有很多属性  就不一一写了  可以去查资料

```xml
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
<!--        传送编码-->
        <property name="defaultEncoding" value="UTF-8"/>
<!--        文件最大上传-->
        <property name="maxUploadSize" value="10240000"/>
    </bean>
```

​		1.3 编写控制类   

```java
 @RequestMapping("testFile")
    public String testFile(@RequestParam("desc")String desc, @RequestParam("file")MultipartFile multipartFile) throws IOException {
        System.out.println("输文件描述"+desc);
        //首先那拿输入流
        InputStream inputStream = multipartFile.getInputStream();
        //创建输出流
        OutputStream outputStream=new FileOutputStream("E:\\"+multipartFile.getOriginalFilename());
        byte [] bytes=new byte[1024];
        int len=-1;
        while ((len=inputStream.read(bytes))!=-1){
            outputStream.write(bytes,0,len);
        }
        inputStream.close();
        outputStream.close();
        System.out.println("文件上传成功");
        return "success";
    }
```

​		1.4  编写前端

```html
<form action="testFile" method="post" enctype="multipart/form-data">
    描述<input type="text" name="desc">
    文件<input type="file" name="file">
    <input type="submit" value="提交"/>
</form>
```

.2)拦截器

​	1.拦截器的原理和过滤器是一样的	

​	2.SpringMVC:要想实现拦截器,必须实现一个接口HandlerInterceptor

```java
package net.bt.lanjie;

import org.springframework.lang.Nullable;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截请求");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
        System.out.println("拦截响应");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
        System.out.println("渲染成功");
    }
}

```

​	3.配置  如果不指定拦截哪些请求默认就是拦截全部

```xml
<!--    配置拦截器 没有指定  默认拦截所有请求-->
    <mvc:interceptors>
        <bean class="net.bt.lanjie.MyInterceptor"/>
    </mvc:interceptors>
```

​	4.那如何配置具体拦截的路径呢

```xml
    <mvc:interceptors>

        <mvc:interceptor>

<!--           指定拦截的路径 -->
            <mvc:mapping path="/**"/>
<!--            指定不拦截的路径-->
            <mvc:exclude-mapping path="/we"/>
            <!--            自己编写的拦截 导入进来-->
            <bean class="net.bt.lanjie.MyInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```

​	5.配置多个拦截器 只会渲染一次  血染的是最后一个拦截器

```xml
    <mvc:interceptors>

        <mvc:interceptor>

<!--           指定拦截的路径 -->
            <mvc:mapping path="/**"/>
<!--            指定不拦截的路径-->
            <mvc:exclude-mapping path="/we"/>
            <!--            自己编写的拦截 导入进来-->
            <bean class="net.bt.lanjie.MyInterceptor"/>
        </mvc:interceptor>
         <mvc:interceptor>

<!--           指定拦截的路径 -->
            <mvc:mapping path="/**"/>
<!--            指定不拦截的路径-->
            <mvc:exclude-mapping path="/we"/>
            <!--            自己编写的拦截 导入进来-->
            <bean class="net.bt.lanjie.MyInterceptor2"/>
        </mvc:interceptor>
    </mvc:interceptors>
```

### 课时11:@ExceptionHandler(局部)和@ResponseStatus异常处理

.1)异常处理:SpringMVC:HandlerExceptionResolver接口  该接口的每个实现类都是一种异常处理方式

​	1 第一种处理异常的方式:使用@ExceptionHandler注解，并通过该注解处理异常,此方法只会处理本类中的异常,称之为局部异常捕获（本类中）

​		1.1 如何编写异常处理方法   

```java
 @ExceptionHandler({ArithmeticException.class})
    public ModelAndView exception(ArithmeticException e){
        ModelAndView modelAndView=new ModelAndView("error");
        System.out.println(e+"========");
        modelAndView.addObject("e", e);
        return modelAndView;
    }
```

​			1.1.1 @ExceptionHandler注解的参数类型是一个数组  可以想捕获多个异常

​			1.1.2 注意处理异常的方法 不能有其他的参数 否则不捕获

​			1.1.3 如果有几个@ExceptionHandler修饰的方法  以最接近这个异常的方法捕获

​	2.第二种处理方式:使用@ControllerAdvice来表示这个类是专门处理异常的(只能处理Controller类)

```java
package net.bt.handler;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

/**
 * 专门处理异常的类
 */
@ControllerAdvice
public class MyExceptionAction {
    @ExceptionHandler({Exception.class})
    public ModelAndView exception(Exception e){
        ModelAndView modelAndView=new ModelAndView("error");
        System.out.println(e+"========");
        modelAndView.addObject("e", e);
        return modelAndView;
    }
}

```

.2)ResponseStatusExceptionResover:自定义异常显示页面:@ResponseStatus

​	1.自定义某个类为自定义异常

```java
package net.bt.handler;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(value = HttpStatus.CHECKPOINT,reason = "测试异常")
public class MyExceptionAction2 extends Exception{

}

```

​		1.1 value = HttpStatus.CHECKPOINT :代表状态码   HttpStatus里面有很多枚举码

​		1.2 reason:代表提示信息

​		1.3 如何使用?    在方法里面抛出即可

```java
 @RequestMapping("testMyException")
    public String testMyException(@RequestParam("i") Integer i) throws MyExceptionAction2 {
        //来个数学异常
       if (i==3){
            throw new MyExceptionAction2();
       }
        return "success";
    }
```

​	2.自定义方法某个方法为自定义异常 如果i==3就转发到自定义异常的处理方法

```java
 @RequestMapping("testMyException")
    public String testMyException(@RequestParam("i") Integer i) throws MyExceptionAction2 {
        
       if (i==3){
            return "redirect:uu";
       }
        return "success";
    }
    @ResponseStatus(value = HttpStatus.CHECKPOINT,reason = "测试异常")
    @RequestMapping("uu")
    public String myException(){
        return "error";
    }

```

### 课时12:其他一种异常处理方式

.1)通过配置实现全局处理异常:SimpleMappingExceptionResolver

```xml
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <!--        将这个e加载到转发域中-->
        <property name="exceptionAttribute" value="e"/>
        <property name="exceptionMappings">
            <props>
<!--                相当与于catch  可以有多个catch -->
<!--                error代表跳转到指定页面-->
                <prop key="java.lang.RuntimeException">error</prop>
            </props>
        </property>
    </bean>
```

​	1.如果发生了key指定的异常,会跳转到指定error.jsp中去

​	2.如果不写 <property name="exceptionAttribute" value="e"/>默认值就是exception

### 课时13:使用IDEA开发SpringMVC程序及表单标签

.1)表单标签

​	1.Spring EL : 1. 支持各种类型的请求方式 (查询doGet 增 加doPost、删除doDelete 、修改doPut)

​	2.可以将对象和表单绑定起来

.2)编写步骤  可以将对象和表单绑定起来

​	1.引入需要的库

```html
<%@taglib prefix="from" uri="http://www.springframework.org/tags/form" %>
```

​	2.将表单与对象绑定  前提就是类中的属性名要与path一模一样  约定大于配置

```html
  <from:form>
      学生id:<form:input path="id"/> <br>
      学生姓名:<from:input path="name"/> <br>
      <input type="submit" value="提交">
  </from:form>
```

​	3.在control里面写

```java
package org.bt.action;

import org.bt.pojo.Student;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.Map;

@Controller
@RequestMapping("FromDome")
public class FromDome {
    @RequestMapping("testFrom")
    public String testFrom(Map<String, Student> map) {
                Student student=new Student();
                student.setId(1);
                student.setName("何邦柱");
                map.put("command",student);
                return "index";
    }
}

```

​		3.1 请求填充完数据之后跳转到index   自动填充from标签

```xml
  <from:form>
      学生id:<form:input path="id"/> <br>
      学生姓名:<from:input path="name"/> <br>
      <input type="submit" value="提交">
  </from:form>
```

​		3.2 默认map里面的key必须是command   如果向自定义指定可以这样写

```xml
  <from:form commandName="pre">
      学生id:<form:input path="id"/> <br>
      学生姓名:<from:input path="name"/> <br>
      <input type="submit" value="提交">
  </from:form>
```

.3)编写步骤 支持各种类型的请求方式 (查询doGet 增 加doPost、删除doDelete 、修改doPut)

​	1.编写配置文件

```xml
    <filter>
        <filter-name>hiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>hiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

​	2.编写前端代码如下

```html
  <from:form method="get" action="FromDome/testGet">
      <input type="submit" value="get">
  </from:form>
  <from:form method="post" action="FromDome/testPost">
      <input type="submit" value="post">
  </from:form>
  <from:form method="put" action="FromDome/testPut">
      <input type="submit" value="put">
  </from:form>
  <from:form method="delete" action="FromDome/testDelete">
      <input type="submit" value="delete">
  </from:form>

```

​	3.编写controller

```java
@RequestMapping("testPost")
    public void testPost() {
        System.out.println("--->post");
    }
    @RequestMapping("testDelete")
    public void testDelete(Map<String, Student> map) {
        System.out.println("--->delete");
    }

    @RequestMapping("testGet")
    public void testGet(Map<String, Student> map) {
        System.out.println("--->get");
    }

    @RequestMapping("testPut")
    public void testPut(Map<String, Student> map) {
        System.out.println("--->put");
    }
```

### 课时14:常见SpringMVC表单标签

.1)checkbox自动绑定

​	1.绑定单个checkbox     boolean如果是true就勾选  如果是false就勾选    步骤

​		1.1  首先需要有一个实体类的属性为  boolean

​		1.2 编写controller

```java
  @RequestMapping("testCheckBox")
    public String testCheckBox(ModelMap modelMap) {
        Student student=new Student();
        student.setSex(true);
        modelMap.addAttribute("stu",student);
       return "SpringForm";
    }
```

​		1.3 编写前端接收   path:代表属性的名称

```html
<form:form commandName="stu">
    <form:checkbox path="sex"/>
</form:form>
```

​	2.绑定多个checkbox  集合   数组中的数据  

​		2.1 首先需要有一个实体类的属性为  集合类型 或者 数组

​		2.2 编写controller

```java
    @RequestMapping("testCheckBox")
    public String testCheckBox(ModelMap modelMap) {
        Student student=new Student();
        List<String> hobbs=new ArrayList<>(3);
        hobbs.add("p");
        hobbs.add("X");
        student.setHobbs(hobbs);
        modelMap.addAttribute("stu",student);
       return "SpringForm";
    }
```

​		2.3  编写前端接收   path:代表属性的名称    value:如果属性值与集合里面的值匹配则勾选

```html
<form:form commandName="stu">
    <form:checkbox path="hobbs" value="P"/>
    <form:checkbox path="hobbs" value="X"/>
    <form:checkbox path="hobbs" value="S"/>
</form:form>
```

