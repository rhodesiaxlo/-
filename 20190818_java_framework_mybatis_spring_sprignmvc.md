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
   * 声明作用范围
   * 声明声明周期(init-method, destory-method)
   * 