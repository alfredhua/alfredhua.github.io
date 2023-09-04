---
title: MyBatis面试题库
date: 2021-11-11
keywords: MyBatis面试题,MyBatis
description: MyBatis面试题,MyBatis
top: true
tags:
	- MyBatis
categories:
  - MyBatis
---


## 什么是MyBatis?

1. MyBatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态sql，可以严格控制sql执行性能，灵活度高。
2. MyBatis 可以使用 XML 或注解来配置和映射原生信息，将 POJO映射成数据库中的记录，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。
3. 通过xml 文件或注解的方式将要执行的各种 statement 配置起来，并通过java对象和 statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。（从执行sql到返回result的过程）。

## Mybaits的优点

- 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。
- 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；
- 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）
- 能够与Spring很好的集 成
- 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

## MyBatis框架的缺点

- SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。
- SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

## MyBatis框架适用场合

- MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。
- 对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。

## MyBatis与Hibernate有哪些不同

- MyBatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。
- MyBatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。 
- Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。

## #{}和${}的区别是什么

#{}是预编译处理，${}是字符串替换。

MyBatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；

MyBatis在处理${}时，就是把${}替换成变量的值。

使用#{}可以有效的防止SQL注入，提高系统安全性。



## 当实体类中的属性名和表中的字段名不一样 ，怎么办

- 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致
- 通过 &lt;resultMap&gt; 来映射字段名和实体类属性名的一一对应的关系。

## 通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

​	Dao接口即Mapper接口。接口的全限名，就是映射文件中的namespace的值；接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。

​	Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在MyBatis中，每一个&lt;select&gt;、&lt;insert&gt;、&lt;update&gt;、&lt;delete&gt;标签，都会被解析为一个MapperStatement对象。

​	举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面 id 为 findStudentById 的 MapperStatement。

​	Mapper接口里的方法，是不能重载的，因为是使用 全限名+方法名 的保存和寻找策略。Mapper 接口的工作原理是JDK动态代理，MyBatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，转而执行MapperStatement所代表的sql，然后将sql执行结果返回。

## MyBatis是如何进行分页的？分页插件的原理是什么？

​	MyBatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

​	 分页插件的基本原理是使用MyBatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

## MyBatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

​	第一种是使用<resultMap>标签，逐一定义数据库列名和对象属性名之间的映射关系。

​	第二种是使用sql列的别名功能，将列的别名书写为对象属性名。

有了列名与属性名的映射关系后，MyBatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。



## MyBatis是否支持延迟加载？如果支持，它的实现原理是什么？

​	MyBatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在MyBatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

​	它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

​	resultMap可以实现高级映射（使用association、collection实现一对一及一对多映射），association、collection具备延迟加载功能。

需求：如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查下用户信息。把对用户信息的按需去查询就是延迟加载。

延迟加载：先从单表查询、需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快。

```java
<!-- 延迟加载的resultMap  -->
    <resultMap type="joanna.yan.mybatis.entity.Orders" id="OrdersUserLazyLoadingResultMap">
        <!-- 1.对订单信息进行映射配置 -->
        <id column="id" property="id"/>
        <result column="user_id" property="userId"/>
        <result column="number" property="number"/>
        <result column="createtime" property="createtime"/>
        <result column="note" property="note"/>
        <!-- 2.实现对用户信息进行延迟加载 -->
        <!-- select:指定延迟加载需要执行的statement的id（是根据user_id查询用户信息的statement） 
               要使用UserMapper.xml中findUserById完成根据用户id(user_id)用户信息的查询，
               如果findUserById不在本mapper中需要前边加namespace。
             column:订单信息中关联用户信息查询的列，是user_id
              关联查询的sql理解为：
             SELECT orders.*,
                (SELECT username FROM USER WHERE orders.user_id = user.id)username,
                (SELECT sex FROM USER WHERE orders.user_id = user.id)sex
             FROM orders
        -->
        <association property="user" javaType="joanna.yan.mybatis.entity.User"
            select="joanna.yan.mybatis.mapper.UserMapper.findUserById" column="user_id">
        </association>
    </resultMap>
```



## MyBatis的一级、二级缓存

- 一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。
- 二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/> ；对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear 掉并重新更新，如果开启了二级缓存，则只根据配置判断是否刷新。
  
  

## N+1问题？

官方文档不建议使用嵌套的select语句的形式

无论是association元素还是collection元素都会遇到，本文以更为典型的collection元素为例。

```
	select * from teacher

```
此时可查询出多条（记为N）教师记录。为了进一步查询出教师指导的学生的信息，需要针对每一条教师记录，生成一条SQL语句，即：select * from student where supervisor_id=?

​	以上SQL语句中的“?”就代表了每个教师的id。显而易见，这样的语句被生成了N条（“N+1问题”中的N）。这样在整个过程中，就总共执行了N+1条SQL语句，即N+1次数据库查询。而数据库查询通常是应用程序性能的瓶颈，一般应尽量减少数据库查询的次数，那么这种方式就会大大降低系统的性能。 







