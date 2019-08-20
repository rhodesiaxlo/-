# mybatis

1. 单表的CRUD 操作

   findAll

   findById

   findByName 模糊查询

   save

   update

   delete

   findTotal

2. 代码重现 mybatis 实现原理

3. Dao 实现类的 单表 CRUD 操作

4. 复杂条件查询 QueryObject

5. 动态sql

   * if
   * where
   * foreach

6. 事务控制,log4f 的配置

7. 多表查询

   * one one
   * one many
   * many many

8. 懒加载 迫切加载，以及 mybatis 的配置和验证

9. mybatis 的缓存机制，一级缓存 二级缓存 以及相关配置



# spring

1. 有 jdbc 实现引除的依赖和耦合问题

2.  FactoryPattern  + reflect 降低依赖和耦合

3. 单例，多例以及容器的概念

4. spring ioc 的基本实现步骤 

5. bean 注册的3中方式

   * 默认构造行数
   * 工厂方法
   * 静态工厂方法

6. bean 的作用范围和 init-method destory-method

   * singleton
   * prototype
   * request
   * session
   * global-session

7. spring DI 的三种方式

   * 基本数据类型
   * bean对象
   * 集合类型
     * list array set
     * map properties

8. 注解开发

   * 声明一个 bean（factory, 构造函数 factory ,静态factory)

     * component
     * controller
     * repository
     * service

   * 声明作用范围(scope)

   * 声明声明周期(init-method, destory-method)

     * Post-construct pre-destory

   * 完全移除 xml

     * @configuration

     * @componentScan(basePackages="") 或者 @componentScan({"",""})

     * 返回第三方jar包中的对象

       ```java
       /**
       *  ds 通过 autowire 在容器中查找，如果没有，报错
       */
       @bean(name="runner")
       public QueryRunner createQueryRunner(Datasource ds) {
         return new QueryRunner(ds);
       }
       ```

     * @import EL 表达式

       ```java
       <import resource="classpath:spring/spring-*.xml"></import>
       ```

       

     * @import

       ```java
       <context:property-placeholder location="classpath:database.properties"/>
       ```

       

     * 

9. 注解 和 xml 的几个不同之处

   * 按照属性注入时， xml 需要 setter方法， 注解不需要，xml 需要id或者全类名，注解不需要。从这里也引发出一个问题，如果注解开发过程中有2个类型和autowire匹配，如何匹配？

     解决方案：  @autowire(value=) + @qualifiere  或者 resource(name="")

   * 注解开发 component 是可以不写 id 的， 那如果不写，默认又是多少呢？

   * 外部引入的jar包，因为不可以修改源代码，不能增加 注解，也无法保证在 component-scan 的目录中，只能使用 xml 配置

   * 有些 bean 在xml中定义更好（queryrunner) ，有些注解更简单，那如果有是纯注解，且存在外部jar包呢

10. spring 整合 junit

    * 导入 spring-test 包

    * 在 junit 中使用 @runwith 引导 junit 加载 spring 容器

      ```java
      @runwith(springjunit4ClassRunner.class)
      ```

    * 告知配置位置

      ```java
      @contextConfiguration(localtion/class)
      ```

      

11. Spring 整合 log4j

12. 银行转账示例引发的代码重复问题

    * 单个操作 findTotal，findById, findByName, save, update, delete 可以控制事务，多个操作组合 Transfer不行

    * 事务操作应该封装到 业务层, 流程应该设计为

      ```java
      // service 层
      try {
      	beginTransation
      	// do something
      	commit
      } catch(Exception e) {
      	rollback
      	
      }
      ```

      而且，所有的 dao 操作应该共用一个 connection

    * 改写 dao， 单一dao操作传入 local_thread 的 connection, 为所有的service层方法增加 上面提到的包装层

    * 业务层代码大量冗余，如何解决？

      答：代理

      * 基于接口的代理， JDK自己提供，被代理类至少继承一个接口

        * 实现步骤

          ```java
          Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterface(), new InvocationHandler() {
            public object invoke(Object obj, Method method, Object[] args) {
              Object rtvalue;
              // 增加方法
              rtvalue = method.invoke(args);
              return rtvalue;
            }
          })
          ```

          [基于接口的代理示例](https://blog.csdn.net/lan12334321234/article/details/70048780)

        * 基于类的动态代理

          * 类不可以是最终类，由第三方 cglib 提供， 实现步骤

            ```java
            Enhancer.create(target.getClass(), new MethodInterceptor {
            	@override
            	public Object interceptor(Object obj, Method method, Object[] args, MethodProxy proxy) {
            			// 方法曾江
            			Object rtValue = null;
            			rtvalue = method.invoke(args);
            			return rtvalue;
            	}
            })
            ```

            [基于类的动态代理](https://blog.csdn.net/lan12334321234/article/details/70048780)

13.  spring AOP

    * 相关术语

      * jointpoint
      * pointcut
      * advice
      * introduction
      * Weaving
      * target
      * aspect

    * 一般步骤

      * 加入 aop 约束

      * 通知类型

        * 前置通知

        * 后置通知

        * 异常通知

        * 最终通知

          ```java
          try {
          	// 前置通知
          	// do something
          	// 后置通知
          } catch(Exception e) {
          	// 异常通知
          }
          ```

        * 执行方法 的通配 

          一般格式 返回值  包名.包名.. 包名.类名.方法名(参数列表)

          通配

          ​                *  *..\*\.\*(\.\.)

      * 一般步骤

        * 引入坐标 aop

        * 配置 aop

          ```java
          <aop:config>
          	<aop:aspect id="{aspect_id}" ref={advisor_id}>
          		<!-- 前置通知 -->
          		<before method={advisor_method} pointcut={execute({class_fullpath})} />
          		<!-- 后置通知 -->
              <after-returning method={advisor_method} pointcut={execute({class_fullpath})} />
              <!-- 异常通知 -->
              <after-throwing method={advisor_method} pointcut={execute({class_fullpath})} />
              <!-- 最终通知 -->
              <after method={advisor_method} pointcut={execute({class_fullpath})} />
              <!-- 环绕通知 -->
              <around method={advisor_method} pointcut={execute({class_fullpath})} />
          	</aop:aspect>
          </aop:config>
          
          public void around(proceddingjointpoint pjt) {
          	Object[] args = pjt.getArgs();
          	pjt.proceed(args);
          }
          ```

    * 注解开发

      * 导入 aspectj

      * <aop:aspectj-autoproxy></aop:aspectj-autoproxy>

      * 配置

        ```
        @component()
        @aspect
        class Advisor {
        
        	@pointcut("{pointcut_execution}")
        	private void pt1(){}
        	
        	@before("pt1()")
        	public void before()
        	{
        	}
        }
        ```

      *  spring 基于注解的 aop 在顺序上存在问题，所以实际项目中一般通过自己实现 环绕通知来实现其他通知

14. spring 的事务控制

    * 相关接口

      * platformTransactionManager

        org.springframework.jdbc.datasource.datasourceTransactionManager

        org.springframework.orm.hibernate5.hibernateTransactionmanager

      * transactionDefinitio(name propogation_type read_only timeout)

      * 什么是隔离级别



# spring mvc





# SSM 环境整合

1. 配置 mybatis 并测试

2. 配置 spring 并测试

3. 配置 spring mvc 并测试

4. Spring + spring mvc 整合  controller 中调用service

   无需创建 applicationcontext，在控制器中可以完成注入，即标志成功

5. spring + spring mvc + mybatis 整合， 代理对象获取成功标志整合成功

6. 事务控制整合

# springboot

1. 搭建 springboot 环境

   * 导入 起步依赖
   * 导入功能坐标
   * 导入配置

2. 启动原理，覆盖tmcat 8080 为8888 ???

3. 加载自定义数据 @ConfigurationProperties

   @value

   ```xml
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot.configuration-processor<artifactId>
   ```

   

4. 整合 mybastic

   * 导入mybatis-sprintboot 坐标，导入mysql驱动坐标

     ```xml
     		<dependency>
     			<groupId>org.mybatis.spring.boot</groupId>
     			<artifactId>mybatis-spring-boot-starter</artifactId>
     			<version>1.3.0</version>
     		</dependency>
     		<dependency>
     			<groupId>mysql</groupId>
     			<artifactId>mysql-connector-java</artifactId>
     			<version>5.1.35</version>
     		</dependency>
     ```

     

   * 配置数据库连接信息

     ```yaml
     spring:
         datasource:
             name: test
             url: jdbc:mysql://127.0.0.1:3306/depot
             username: root
             password: root
             # 使用druid数据源
             type: com.alibaba.druid.pool.DruidDataSource
             driver-class-name: com.mysql.jdbc.Driver
             filters: stat
             maxActive: 20
             initialSize: 1
             maxWait: 60000
             minIdle: 1
             timeBetweenEvictionRunsMillis: 60000
             minEvictableIdleTimeMillis: 300000
             validationQuery: select 'x'
             testWhileIdle: true
             testOnBorrow: false
             testOnReturn: false
             poolPreparedStatements: true
             maxOpenPreparedStatements: 20
     ```

     

   * 编写实体类和 mapper配置文件

   * 配置 实体类包信息和 mapper文件路径信息

     ```yaml
     mybatis:
       mapper-locations: classpath:mapping/*.xml  #注意：一定要对应mapper映射xml文件的所在路径
       type-aliases-package: com.winter.model  # 注意：对应实体类的路径
     ```

     或者，在 springboot 的引导程序中增加

      ```java
     @mapperScan(basepackage="")
      ```

     

5. 整合 junit

   | 方法                 | 描述 |      |
   | -------------------- | ---- | ---- |
   | beforeClass          |      |      |
   | afterClass           |      |      |
   | Before               |      |      |
   | After                |      |      |
   | test(timeout=1000)   |      |      |
   | test(expected=class) |      |      |
   | Runwith              |      |      |
   | Test                 |      |      |

   

   * 导入起步依赖

     ```xml
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-starter-test</artifactId>
                 <scope>test</scope>
     ```

     

6. 整合 log4j

   ```java
   @RunWith(SpringRunner.class)
   //ItodoApplication springboot的启动类
   @SpringBootTest(classes = ItodoApplication.class)			
   ```

7. 整合事务支持

   ```java
   @enableTransactionManagement
   
   // 在业务层
   @Transactional
   ```

8. html 页面跳转

9. springboot的监听器 applicationlistener

   * ApplicationContextEvent
   * ApplicationRefreshEvent
   * ApplicationStartEvent
   * ApplicationStopEvent
   * ApplicationCloseEvent

# netty

1. 网络知识 
   * 三次握手，四次挥手
   * 标志位
   * 同步异步 阻塞非阻塞
2. Netty 的 reactor 模型
   * 单线程模型
   * 多线程模型
   * 主从多线程模型
3. netty 前后端的环境搭建
   * 发起连接，建立连接，发送消息，广播消息
   * 上线下线
     * onopen
     * onmessage
     * onclose
     * sendMessage
   * 制定转发
   * 上线加载

