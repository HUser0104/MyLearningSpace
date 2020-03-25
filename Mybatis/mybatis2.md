### 课时17:鉴别器和别名问题

.1)通过resultMap来解决数据库的字段和类中的属性不一致的问题

​	1.在mapper文件中编写如下

```xml
<select id="queryStudentByNoWithResultMap" resultMap="StudqueryStudentByNoWithMap">
        select * from student where sno=#{xx}
    </select>
    <resultMap id="StudqueryStudentByNoWithMap" type="student">
        <id property="stuno" column="sno"></id>
        <result property="stuName" column="sname"></result>
        <result property="stuAge" column="sage"></result>
        <result property="graName" column="gname"></result>
        <result property="stuSex" column="ssex"></result>
    </resultMap>
```

​		1.1 resultMap分为主键与非主键  

​		1.2 通过column指定的字段返回的结果填入property中填写的java属性



.2)鉴别器(对查询的结果进行分支处理)

​	1.列如    如果查询出的年级为a年级就显示昵称  	如果查询出的年级为 b年级就显示b年级就显示真实名称

​		1.1 实体不需要改造因为最终的结果只有一个 可以使用stuName来接收名称或者昵称

​		1.2 鉴别器的编写

```xml
 <select id="queryStudentsWithResultMap" resultMap="StudqueryStudentByNoWithMap2">
        select * from student
    </select>
    <resultMap id="StudqueryStudentByNoWithMap2" type="student">
        <id property="stuno" column="sno"></id>

        <result property="stuAge" column="sage"></result>
        <result property="graName" column="rname"></result>
        <result property="stuSex" column="ssex"></result>
        <discriminator javaType="String" column="rname">
            <case value="S1" resultType="student">
                <result property="stuName" column="sname"></result>
            </case>
            <case value="aji" resultType="student">
                <result property="stuName" column="nickname"></result>
            </case>
        </discriminator>
    </resultMap>
```

​			1.2.1 javaType:这个要被赋值的属性类型   column:用于做条件的字段  value:条件具体值   resultType:返回的类型   

​			1.2.2 最后填写映射内容

```xml
<result property="stuName" column="sname"></result>
<result property="stuName" column="nickname"></result>
```

.3) 别名的问题

​	1.配置别名 (包和子包)中的所有类 起了别名

```xml
<typeAliases>
        <package name="org.hbz.entity"/>
    </typeAliases>
```

​	2.引发了一个问题加入我本包里面的类名与子包里面的类名一致了怎么办?

![1583848683739](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1583848683739.png)

​		2.1 只需要在两个类任意一个类上面加上这么一个注解 就可以区分了

```java
@Alias("MyStudent")
public class Student {
}
```

### 课时18：trim标签与MyBatis内置参数

.1)trim标签的使用

​	1.使用trim处理查询和修改操作

```xml
<!--查询操作处理-->
<select id="queryStudentByNoWithONGL" resultType="Student" parameterType="student">
        select * from student
        <trim prefix="where" prefixOverrides="and|or">
            <if test="stuName!=null and stuName!=''">and stuName like '%${stuName}%'</if>
            <if test="graName!=null and graName!=''">and graName like '%${graName}%'</if>
            <if test="stuAge!=null and stuAge!=''">and stuAge = #{stuAge} </if>
        </trim>
    </select>
<!--修改操作处理-->
<update id="updateSmbmsProviderByIdTrim" parameterType="SmbmsProvider">
        update smbms_provider
        <trim prefix="set" suffix="where id=#{id}" suffixOverrides=",">
        <if test="proCode!=null and proCode!=''">proCode=#{proCode},</if>
        <if test="proName!=null and proName!=''">proName=#{proName},</if>
        <if test="proDesc!=null and proDesc!=''">proDesc=#{proDesc},</if>
        <if test="proContact!=null and proContact!=''">proContact=#{proContact},</if>
        <if test="proPhone!=null and proPhone!=''">proPhone=#{proPhone},</if>
        <if test="proAddress!=null and proAddress!=''">proAddress=#{proAddress},</if>
        <if test="proFax!=null and proFax!=''">proFax=#{proFax},</if>
        <if test="createdBy!=null">createdBy=#{createdBy},</if>
        <if test="creationDate!=null">creationDate=#{creationDate},</if>
        <if test="modifyDate!=null">modifyDate=#{modifyDate},</if>
        <if test="modifyBy!=null">modifyBy=#{modifyBy},</if>
        </trim>
    </update>
```

​	2.trim标签里面的属性的作用:

​		2.1 prefix:在前缀加上标签或者加上一些语句      suffix:在后缀加上标签或者加上一些语句

​		2.2 suffixOverrides:在后缀去某些信息    prefixOverrides:在在前缀去掉某些信息

.2)mybatis的内置参数

​	1.内置参数 _parameter :代表mybatis的输入参数

```xml
<select id="queryStudentByNoWithONGL" resultType="Student" parameterType="student">
        select * from student
        <trim prefix="where" prefixOverrides="and|or">
            <if test="stuName!=null and stuName!=''">and stuName like '%${_parameter.stuName}%'</if>
            <if test="graName!=null and graName!=''">and graName like '%${graName}%'</if>
            <if test="stuAge!=null and stuAge!=''">and stuAge = #{stuAge} </if>
        </trim>
    </select>
```

​			1.1 如果是基本类型+String 直接填写这个内置参数即可

​	2.内置参数_databaseId:代表当前数据库的名字

​		这个标签的作用就是可以动态的知道数据库环境

### 课时19: 模糊查询三种解决方式

.1)有这几种针对模糊查询的方式

​	1.${} 

​		1.1 使用如下 

```xml
 <if test="stuName!=null and stuName!=''">and stuName like '%${stuName}%'</if>
```

​			1.1.1 这种方法时不推荐使用的   安全隐患问题

​	2.传值时,直接传%xxx%   然后使用#{值} 来接收

​		2.1 使用如下

```java
 student.setStuName("%p%");
```

```xml
<if test="stuName!=null and stuName!=''">and stuName like #{stuName}</if>
```

​	3.band标签来处理

​		3.1 使用如下

```xml
			<bind name="_stuName" value="'%'+stuName+'%'"></bind>
            <if test="stuName!=null and stuName!=''">and stuName like #{_stuName}</if>
```

​			3.1.1 可以有多个bind  相当于一个中专站  处理好了的值   通过name的名称来访问

### 课时 20:MyBatis架构和SqlSessionFactory源码分析

.1)分析mybatis的架构(四层架构)

​	1.接口层(广义接口)

​		1.1 增加接口方法   删除 增加  修改 查询   各种直接使用的配置方法

​	2.数据处理层 

​		2.1 列如select * from student where name=#{} and age=#{}

​			2.1.1 首先处理参数  

​			2.1.2 处理完参数  之后解析SQL

​			2.1.3 解析完SQL之后数据库就知道你要干嘛了   开始执行SQL(Executor)

​			2.1.4 执行完SQL    开始处理结果

​	3.框架支撑层

​		3.1 事务管理  缓存机制   连接池管理等等(各种辅助功能)

​	4.引导层

​		4.1 SQL语句的方式:XML配置      or    注解方式



.2)获取SqlSessionFactory源码分析

​	1.获取了一个parse解析器

```java
XMLConfigBuilder parser = new XMLConfigBuilder(reader, environment, properties);
```

​	2.通过configuration标签 设置了很多个子参数 properties丶settings丶typeAliases等等子标签

```java
 this.parseConfiguration(this.parser.evalNode("/configuration"));
```

​		2.1 上面调用的方法如下

```java
 private void parseConfiguration(XNode root) {
        try {
            this.propertiesElement(root.evalNode("properties"));
            Properties settings = this.settingsAsProperties(root.evalNode("settings"));
            this.loadCustomVfs(settings);
            this.typeAliasesElement(root.evalNode("typeAliases"));
            this.pluginElement(root.evalNode("plugins"));
            this.objectFactoryElement(root.evalNode("objectFactory"));
            this.objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
            this.reflectorFactoryElement(root.evalNode("reflectorFactory"));
            this.settingsElement(settings);
            this.environmentsElement(root.evalNode("environments"));
            this.databaseIdProviderElement(root.evalNode("databaseIdProvider"));
            this.typeHandlerElement(root.evalNode("typeHandlers"));
            this.mapperElement(root.evalNode("mappers"));
        } catch (Exception var3) {
            throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + var3, var3);
        }
    }
```

 		2.2 分析几个代表性的标签

​			2.2.1 settings分析

​				2.2.1.1 设置标签中通过settingsElment()方法  创建了很多子元素 并且带有默认值

```java
private void settingsElement(Properties props) throws Exception {
        this.configuration.setAutoMappingBehavior(AutoMappingBehavior.valueOf(props.getProperty("autoMappingBehavior", "PARTIAL")));
        this.configuration.setAutoMappingUnknownColumnBehavior(AutoMappingUnknownColumnBehavior.valueOf(props.getProperty("autoMappingUnknownColumnBehavior", "NONE")));
        this.configuration.setCacheEnabled(this.booleanValueOf(props.getProperty("cacheEnabled"), true));
        this.configuration.setProxyFactory((ProxyFactory)this.createInstance(props.getProperty("proxyFactory")));
        this.configuration.setLazyLoadingEnabled(this.booleanValueOf(props.getProperty("lazyLoadingEnabled"), false));
        this.configuration.setAggressiveLazyLoading(this.booleanValueOf(props.getProperty("aggressiveLazyLoading"), false));
        this.configuration.setMultipleResultSetsEnabled(this.booleanValueOf(props.getProperty("multipleResultSetsEnabled"), true));
        this.configuration.setUseColumnLabel(this.booleanValueOf(props.getProperty("useColumnLabel"), true));
        this.configuration.setUseGeneratedKeys(this.booleanValueOf(props.getProperty("useGeneratedKeys"), false));
        this.configuration.setDefaultExecutorType(ExecutorType.valueOf(props.getProperty("defaultExecutorType", "SIMPLE")));
        this.configuration.setDefaultStatementTimeout(this.integerValueOf(props.getProperty("defaultStatementTimeout"), (Integer)null));
        this.configuration.setDefaultFetchSize(this.integerValueOf(props.getProperty("defaultFetchSize"), (Integer)null));
        this.configuration.setMapUnderscoreToCamelCase(this.booleanValueOf(props.getProperty("mapUnderscoreToCamelCase"), false));
        this.configuration.setSafeRowBoundsEnabled(this.booleanValueOf(props.getProperty("safeRowBoundsEnabled"), false));
        this.configuration.setLocalCacheScope(LocalCacheScope.valueOf(props.getProperty("localCacheScope", "SESSION")));
        this.configuration.setJdbcTypeForNull(JdbcType.valueOf(props.getProperty("jdbcTypeForNull", "OTHER")));
        this.configuration.setLazyLoadTriggerMethods(this.stringSetValueOf(props.getProperty("lazyLoadTriggerMethods"), "equals,clone,hashCode,toString"));
        this.configuration.setSafeResultHandlerEnabled(this.booleanValueOf(props.getProperty("safeResultHandlerEnabled"), true));
        this.configuration.setDefaultScriptingLanguage(this.resolveClass(props.getProperty("defaultScriptingLanguage")));
        Class<? extends TypeHandler> typeHandler = this.resolveClass(props.getProperty("defaultEnumTypeHandler"));
        this.configuration.setDefaultEnumTypeHandler(typeHandler);
        this.configuration.setCallSettersOnNulls(this.booleanValueOf(props.getProperty("callSettersOnNulls"), false));
        this.configuration.setUseActualParamName(this.booleanValueOf(props.getProperty("useActualParamName"), true));
        this.configuration.setReturnInstanceForEmptyRow(this.booleanValueOf(props.getProperty("returnInstanceForEmptyRow"), false));
        this.configuration.setLogPrefix(props.getProperty("logPrefix"));
        Class<? extends Log> logImpl = this.resolveClass(props.getProperty("logImpl"));
        this.configuration.setLogImpl(logImpl);
        this.configuration.setConfigurationFactory(this.resolveClass(props.getProperty("configurationFactory")));
    }
```

​			2.2.2 分析mappers分析

​				2.2.2.1 通过这个来分析不同的映射SQL映射文件

```java
 private void mapperElement(XNode parent) throws Exception {
        if (parent != null) {
            Iterator var2 = parent.getChildren().iterator();

            while(true) {
                while(var2.hasNext()) {
                    XNode child = (XNode)var2.next();
                    String resource;
                    if ("package".equals(child.getName())) {
                        resource = child.getStringAttribute("name");
                        this.configuration.addMappers(resource);
                    } else {
                        resource = child.getStringAttribute("resource");
                        String url = child.getStringAttribute("url");
                        String mapperClass = child.getStringAttribute("class");
                        XMLMapperBuilder mapperParser;
                        InputStream inputStream;
                        if (resource != null && url == null && mapperClass == null) {
                            ErrorContext.instance().resource(resource);
                            inputStream = Resources.getResourceAsStream(resource);
                            mapperParser = new XMLMapperBuilder(inputStream, this.configuration, resource, this.configuration.getSqlFragments());
                            mapperParser.parse();
                        } else if (resource == null && url != null && mapperClass == null) {
                            ErrorContext.instance().resource(url);
                            inputStream = Resources.getUrlAsStream(url);
                            mapperParser = new XMLMapperBuilder(inputStream, this.configuration, url, this.configuration.getSqlFragments());
                            mapperParser.parse();
                        } else {
                            if (resource != null || url != null || mapperClass == null) {
                                throw new BuilderException("A mapper element may only specify a url, resource or class, but not more than one.");
                            }

                            Class<?> mapperInterface = Resources.classForName(mapperClass);
                            this.configuration.addMapper(mapperInterface);
                        }
                    }
                }

                return;
            }
        }
    }
```

```java
mapperParser.parse();
```

​				2.2.2.2  通过进一步的发现  开始解析SQL文件的增删改查操作

```java
this.configurationElement(this.parser.evalNode("/mapper"));
```

​				2.2.2.3 通过上面的方法开始解析SQL映射文件

```java
 this.buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
```

​				2.2.2.4 parseStatementNode()方法来解析标签属性

```java
public void parseStatementNode() {
        String id = this.context.getStringAttribute("id");
        String databaseId = this.context.getStringAttribute("databaseId");
        if (this.databaseIdMatchesCurrent(id, databaseId, this.requiredDatabaseId)) {
            Integer fetchSize = this.context.getIntAttribute("fetchSize");
            Integer timeout = this.context.getIntAttribute("timeout");
            String parameterMap = this.context.getStringAttribute("parameterMap");
            String parameterType = this.context.getStringAttribute("parameterType");
            Class<?> parameterTypeClass = this.resolveClass(parameterType);
            String resultMap = this.context.getStringAttribute("resultMap");
            String resultType = this.context.getStringAttribute("resultType");
            String lang = this.context.getStringAttribute("lang");
            LanguageDriver langDriver = this.getLanguageDriver(lang);
            Class<?> resultTypeClass = this.resolveClass(resultType);
            String resultSetType = this.context.getStringAttribute("resultSetType");
            StatementType statementType = StatementType.valueOf(this.context.getStringAttribute("statementType", StatementType.PREPARED.toString()));
            ResultSetType resultSetTypeEnum = this.resolveResultSetType(resultSetType);
            String nodeName = this.context.getNode().getNodeName();
            SqlCommandType sqlCommandType = SqlCommandType.valueOf(nodeName.toUpperCase(Locale.ENGLISH));
            boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
            boolean flushCache = this.context.getBooleanAttribute("flushCache", !isSelect);
            boolean useCache = this.context.getBooleanAttribute("useCache", isSelect);
            boolean resultOrdered = this.context.getBooleanAttribute("resultOrdered", false);
            XMLIncludeTransformer includeParser = new XMLIncludeTransformer(this.configuration, this.builderAssistant);
            includeParser.applyIncludes(this.context.getNode());
            this.processSelectKeyNodes(id, parameterTypeClass, langDriver);
            SqlSource sqlSource = langDriver.createSqlSource(this.configuration, this.context, parameterTypeClass);
            String resultSets = this.context.getStringAttribute("resultSets");
            String keyProperty = this.context.getStringAttribute("keyProperty");
            String keyColumn = this.context.getStringAttribute("keyColumn");
            String keyStatementId = id + "!selectKey";
            keyStatementId = this.builderAssistant.applyCurrentNamespace(keyStatementId, true);
            Object keyGenerator;
            if (this.configuration.hasKeyGenerator(keyStatementId)) {
                keyGenerator = this.configuration.getKeyGenerator(keyStatementId);
            } else {
                keyGenerator = this.context.getBooleanAttribute("useGeneratedKeys", this.configuration.isUseGeneratedKeys() && SqlCommandType.INSERT.equals(sqlCommandType)) ? Jdbc3KeyGenerator.INSTANCE : NoKeyGenerator.INSTANCE;
            }
	//这个方法用于构建出一个statement对象
            this.builderAssistant.addMappedStatement(id, sqlSource, statementType, sqlCommandType, fetchSize, timeout, parameterMap, parameterTypeClass, resultMap, resultTypeClass, resultSetTypeEnum, flushCache, useCache, resultOrdered, (KeyGenerator)keyGenerator, keyProperty, keyColumn, databaseId, langDriver, resultSets);
        }
    }
```

​				2.2.2.5 在解析完成会将select等标签封装成一个MappedStatement类

​								即   MappedStatement就是select 标签

```java
 public MappedStatement addMappedStatement(String id, SqlSource sqlSource, StatementType statementType, SqlCommandType sqlCommandType, Integer fetchSize, Integer timeout, String parameterMap, Class<?> parameterType, String resultMap, Class<?> resultType, ResultSetType resultSetType, boolean flushCache, boolean useCache, boolean resultOrdered, KeyGenerator keyGenerator, String keyProperty, String keyColumn, String databaseId, LanguageDriver lang, String resultSets) {
        if (this.unresolvedCacheRef) {
            throw new IncompleteElementException("Cache-ref not yet resolved");
        } else {
            id = this.applyCurrentNamespace(id, false);
            boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
            org.apache.ibatis.mapping.MappedStatement.Builder statementBuilder = (new org.apache.ibatis.mapping.MappedStatement.Builder(this.configuration, id, sqlSource, sqlCommandType)).resource(this.resource).fetchSize(fetchSize).timeout(timeout).statementType(statementType).keyGenerator(keyGenerator).keyProperty(keyProperty).keyColumn(keyColumn).databaseId(databaseId).lang(lang).resultOrdered(resultOrdered).resultSets(resultSets).resultMaps(this.getStatementResultMaps(resultMap, resultType, id)).resultSetType(resultSetType).flushCacheRequired((Boolean)this.valueOrDefault(flushCache, !isSelect)).useCache((Boolean)this.valueOrDefault(useCache, isSelect)).cache(this.currentCache);
            ParameterMap statementParameterMap = this.getStatementParameterMap(parameterMap, parameterType, id);
            if (statementParameterMap != null) {
                statementBuilder.parameterMap(statementParameterMap);
            }

            MappedStatement statement = statementBuilder.build();
            this.configuration.addMappedStatement(statement);
            return statement;
        }
    }
```

​	3.MappedStatement --->存在于Configuration中 environment--->存在于Configuration中

​		所有的配置信息都存在于Configuration中

​		Configuration有存在于DefaultSqlSessionFactory对象中(SqlSessionFactory)-->SqlSessionFactory 多态的体现

​	4.结构

​		4.1 SqlSessionFactory --> DefaultSqlSessionFactory-->Configuration-->包含了一切信息

### 课时21 :使用MyBatis实现批量操作

.1) 批量操作DML 方式一 推荐方式

​	1.如果想提升处理速度可以加上这个代码  测试过实现差不多可以提升1.多倍的性能    

```java
 //打开一次会话
        SqlSession session=sessionFactory.openSession(ExecutorType.BATCH);
```

​	2.BATCH:预编译一次,其余次数  只需要设置参数值即可

```xml
    <insert id="addStudent"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
        insert into student(stuNo,stuName,stuAge)
        values(#{stuno},#{stuName},#{stuAge})
    </insert>
```

​	3.没有BATCCH:

​								预编译N次,每次DML都需要执行完整的SQL

.2)不推荐的方式:SQL拼接   (就是说把SQL语句拼接完成一次性插入)

​	1.mysql:批量插入

​	insert  into student(stuno,stuName,stuAge) values (21,'asas',20), (22,'as',21)

```xml
  <insert id="addStudent" pareameter="list"  databaseId="mysql"  useGeneratedKeys="true" keyProperty="stuno">
       insert  into student(stuno,stuName,stuAge) values
       <foreach collection="list" item="student" separator="," close=";">
           (#{student.stuno},#{student.stuName},#{student.stuAge})
       </foreach>

    </insert>

```

​	2.核心:将SQL拼接成mysql能够认识的SQL:collection的参数必须是collection或list

​	3.这种方式不推荐

​		3.1 没有用到mybatis对批量插入的支持  BATCH

​		3.2 不适合数据库的迁移

​		3.3 如果大量的数据,则会将  拼接的SQL拉的很长,而部分数据库对SQL语句的长度有限制

### 课时22::PageHelper分页插件

.1)实现的步骤

​	1.引入jar包

```xml
    <!--添加pageHelper分页-->
    <dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.0-beta</version>
    </dependency>
```

​	2.配置主文件

```xml
 <plugins>
        <!-- com.github.pagehelper为PageHelper类所在包名 -->
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
            <property name="reasonable" value="true"/>
        </plugin>
    </plugins>
```

​	3.如何实现插件

```java
 //执行方法
        Page<Object> pages = PageHelper.startPage(2, 5);
        List<Student> student= iStudentDao.queryPageStudentOrderByNo();
       for (Student s:student){
           System.out.println(s);
       }
        System.out.println("总页码:"+pages.getPages());
        System.out.println("当前页码："+pages.getPageNum());
        System.out.println("当前数据量："+pages.getPageSize());
        System.out.println("总数据量："+pages.getTotal());
```

```xml
  Page<Student> page = 
                PageHelper.startPage(1, 10).doSelectPage(()-> iStudentDao.queryPageStudentOrderByNo());
        List<Student> result = page.getResult();
        for (Student s:result){
           System.out.println(s);
       }
```

​	4.如果阿香拿到更多的数据可以

```java
PageInfo pageInfo=new PageInfo(把查出来的集合放入即可);列如可以拿到一个页码数组 
```



​	5.详细使用可以参考https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md

