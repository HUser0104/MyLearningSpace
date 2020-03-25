



### 课时:8:基于注解形式的AOP实现

​	1..导入相应jar包  前置通知示例已经有了  这里就不做过多的讲解

​	2.配置

​		2.1.添加扫描  把通知类加入ioc容器

```xml
<!--    添加扫描器扫描aop包的bean-->
    <context:component-scan base-package="net.bdqn.hbz.aop"></context:component-scan>
```

​		2.1.2 开启注解对aop的支持

```xml
<!--    开启注解对aop的支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

​	3.编写通知类

```java
package net.bdqn.hbz.aop;

import net.bdqn.hbz.pojo.Student;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

/**
 * 通过注解方式实现aop
 */
//将这个类纳入容器
    @Component("la")
    //将这个类变成通知类
    @Aspect
public class LogAnnoetion {

    /**
     * 前置通知
     */
    @Before("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
        public void myBefore() {
        System.out.println("通过注解的方式实现的前置通知");
    }

    /**
     * 后置通知
     */
    @AfterReturning("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myAfter(){
        System.out.println("通过注解的方式实现的后置通知");
    }
    
     /**
     * 环绕通知
     */
    @Around("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myAround(ProceedingJoinPoint point){
        //执行前置通知
        System.out.println("[环绕]执行前置通知");
        try {
            //执行方法
            point.proceed();
            //执行后置通知
            System.out.println("[环绕]执行后置通知");
        } catch (Throwable throwable) {
            //执行异常通知
            System.out.println("[环绕]执行异常通知");
        }finally {
            //执行最终通知
            System.out.println("[环绕]执行最终通知");
        }
    }
    
     /**
     * 异常通知
     */
    @AfterThrowing(pointcut = "execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myExecution(JoinPoint pointcut){
        System.out.println("【异常通知】发生异常了");
    }
    /**
     * 最终通知
     */
    @After("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myFinally(JoinPoint pointcut){

        System.out.println("【最终通知】执行了");
    }
    
}

```

​		3.1 一些注解的意思:

​			3.1.1  @Component("la")  //将这个类纳入容器

​			3.1.2   @Aspect   //声明成通知类

​			3.1.3 前置通知: @Before("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")  与目标对象连接

​			3.1.4 后置通知: @AfterReturning("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")  与目标对象连接

​			3.1.5 环绕通知:@Around("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")

​			3.1.6 异常通知: @AfterThrowing(pointcut = "execution(public void addStudent(net.bdqn.hbz.pojo.Student))")

​			3.1.7 最终通知:@After("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")

​	4.通过注解形式 实现aop,如果想获取 目标对象的一些参数,则需要使用一个对象参数类:JoinPoint  但是环绕通知是ProceedingJoinPoint

```java
package net.bdqn.hbz.aop;

import net.bdqn.hbz.pojo.Student;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

/**
 * 通过注解方式实现aop
 */
//将这个类纳入容器
    @Component("la")
    //将这个类变成通知类
    @Aspect
public class LogAnnoetion {

    /**
     * 前置通知
     */
    @Before("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
        public void myBefore() {
        System.out.println("通过注解的方式实现的前置通知");
    }

    /**
     * 后置通知
     */
    @AfterReturning(pointcut = "execution(public void addStudent(net.bdqn.hbz.pojo.Student))",returning = "returning")
    public void myAfter(JoinPoint point,Object returning){
        System.out.println("通过注解的方式实现的后置通知");
        System.out.println("通过注解拿到的参数:目标对象:"+point.getTarget()+",方法名称"+point.getSignature().getName()
        +",方法参数"+point.getArgs().length+",目标返回值:"+returning);
    }
    
     /**
     * 环绕通知
     */
    @Around("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myAround(ProceedingJoinPoint point){
        //执行前置通知
        System.out.println("[环绕]执行前置通知");
        try {
            //执行方法
            point.proceed();
            //执行后置通知
            System.out.println("[环绕]执行后置通知");
        } catch (Throwable throwable) {
            //执行异常通知
            System.out.println("[环绕]执行异常通知");
        }finally {
            //执行最终通知
            System.out.println("[环绕]执行最终通知");
        }
    }
}

 	/**
     * 异常通知
     */
    @AfterThrowing(pointcut = "execution(public void addStudent(net.bdqn.hbz.pojo.Student))",throwing = "e")
    public void myExecution(JoinPoint pointcut,NullPointerException e){
        System.out.println("【异常通知】发生异常了");
        System.out.println(e.getMessage());
    }
    /**
     * 最终通知
     */
    @After("execution(public void addStudent(net.bdqn.hbz.pojo.Student))")
    public void myFinally(JoinPoint pointcut){

        System.out.println("【最终通知】执行了");
    }

```

​		4.1 通过returning=“returning”来指定形参表示返回值  其他的通知都差不多这样拿返回值但是不是全部通知都是

​		4.2 异常通知只捕获某些异常可以加上这个throwing = "e"  然后形参指定类型即可

​		4.2 在通知的方法不要乱写参数   可能会异常

### 课时13:补漏：注解形式的依赖注入

.1)注解形式的依赖注入

​	1.配置扫描器

```xml
<!--    配置扫描器 扫描注解形式的bean对象-->
    <context:component-scan base-package="net.bdqn.hbz.dao,net.bdqn.hbz.service"/>
```

​	2.加注解

```java
@Service("studentService")
public class IStudentServiceImpl implements IStudentService {
    @Autowired
    private IStudentDao iStudentDao;

    public void setiStudentDao(IStudentDaoImpl iStudentDao) {
        this.iStudentDao = iStudentDao;
    }
```

​		2.1 @Service("studentService")把这个类加入到bean里面  (Service层)

​			@Repository("studentDao")	把这个类加入到bean里面  (Dao层)

​			@Controller("studentController")把这个类加入到bean里面  (控制层)

​			还有个最大的@Component("xxx")  把这个类加入到bean里面 什么层都可以(一般写在一些不属于三层的类中)

​		2.2  @Autowired 依赖注入IStudentDao  (前提是已经有dao层的bean)  自动装配按照byType方式

​		2.3 如果想更精确查找bean在@Autowired下面写@Qualifier("studentDao") 相当于byName

### 课时14:开发基于注解的Spring

.1)创建一个ioc配置类加上@Configuration  相当于applicationContext.xml     

​	1.如何使用注解来创建ioc里面bean

​		1.1 第一种方式自定义方法方式 在配置类中定义

```java
   @Bean
    public Student beanStudent(){
        return new Student();
    }
```

​			1.1.1 bean的id默认就是方法名称     bean的值就是返回值就是bean的类型

​		1.2 第二种方式就是在要放入bean的类上面加上这些注解

​			@Service("studentService")把这个类加入到bean里面  (Service层)

​			@Repository("studentDao")	把这个类加入到bean里面  (Dao层)

​			@Controller("studentController")把这个类加入到bean里面  (控制层)

​			还有个最大的@Component("xxx")  把这个类加入到bean里面 什么层都可以(一般写在一些不属于三层的类中)

​			1.1.2 这种方式必须在配置类中添加扫描器

```java
@ComponentScan(value = "org.bd")
或者
@ComponentScans({})
```

​		1.3 如果想给对象的值主入值在属性的上面加上

```java
 @Autowired
    @Qualifier("iStudentDaoImpl")
    private IStudentDao iStudentDao;

    public void setiStudentDao(IStudentDao iStudentDao) {
        this.iStudentDao = iStudentDao;
    }
```

​		1.4 测试

```java
public static void test1(){
        ApplicationContext context=new AnnotationConfigApplicationContext(MyConfig.class);
        IStudentServiceImpl iStudentService=
                (IStudentServiceImpl)context.getBean("iStudentServiceImpl");
        //查看所有的beanid
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(beanDefinitionNames);
        iStudentService.queryStudent();
    }
```

​	2.扫描器

​		2.1 三层扫描器 改变控制规则 在配置中编写

​		2.2FilterType(ANNOTATION(包),ANNOTATION_TYPE(具体的类),CUSTOM(自定义))

​			2.1.1  扫描不包含这些类  excludeFilters

```java
@ComponentScan(value = "org.bd",excludeFilters={@ComponentScan.Filter(type = FilterType.ANNOTATION,value = value = {Service.class, Repository.class})})//排除了是@Service和@Repository注解加入的bean
```

​			2.1.2 扫描只包含    includeFilters    注意 ：需要关闭默认行为

```java
@ComponentScan(value = "org.bd",includeFilters={@ComponentScan.Filter(type = FilterType.ANNOTATION_TYPE,value = {IStudentDao.class})},useDefaultFilters = false)
```

​			2.1.3 自定义扫描哪些类

​				2.1.3.1 编自定义一个类  该类实现TypeFilter

```java
public class MyFiter implements TypeFilter {
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        if ("IStudent".equals(classMetadata.getClassName())){
            return true;
        }
        return false;
    }
}
```

​		

  

