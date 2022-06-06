## 什么是MyBatis 

MyBatis 是一款优秀的持久层框架，一个半 ORM（对象关系映射）框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。


## Mybatis优缺点

**优点**

与传统的数据库访问技术相比，ORM有以下优点：

1. 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；
2. 提供XML标签，支持编写动态SQL语句，并可重用
3. 与JDBC相比，减少了代码量，不需要手动开关连接,很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）
4. 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护
5. 能够与Spring很好的集成

**缺点**

1. SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求
2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库

## #{}和${}的区别是什么？

```java
#{}是预编译处理，${}是字符串替换。使用#设置参数时，MyBatis会创建预编译的SQL语句，
然后在执行SQL时MyBatis会为预编译SQL中的占位符（?）赋值。
预编译的SQL语句执行效率高，并且可以防止注入攻击。使用$设置参数时，MyBatis只是创建普通的SQL语句，
然后在执行SQL语句时MyBatis将参数直接拼入到SQL里。这种方式在效率、安全性上均不如前者。
```



## 既然${}不安全，为什么还需要${}，什么时候会用到它？

它可以解决一些特殊情况下的问题。例如，在一些动态表格（根据不同的条件产生不同的动态列）中，我们要传递SQL的列名，根据某些列进行排序，或者传递列名给SQL都是比较常见的场景，这就无法使用预编译的方式了。

## 为什么需要预编译

预编译阶段可以优化sql 的执行。 预编译之后的sql 多数情况下可以直接执行，DBMS 不需要再次编译，越复杂的sql，编译的复杂度将越大，预编译阶段可以合并多次操作为一个操作。 预编译语句对象可以重复利用。

## 在mapper中如何传递多个参数

### 方法1：顺序传参法

public User selectUser(String name, int deptId);

```xml
<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{0} and dept_id = #{1}
</select>
```

#{}里面的数字代表传入参数的顺序。

这种方法不建议使用，sql层表达不直观，且一旦顺序调整容易出错。

### 方法2：@Param注解传参法

public User selectUser(@Param("userName") String name, int @Param("deptId") deptId);

```xml
<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>
```

#{}里面的名称对应的是注解@Param括号里面修饰的名称。

这种方法在参数不多的情况还是比较直观的，推荐使用。

### 方法3：Map传参法

public User selectUser(Map<String, Object> params);

```xml
<select id="selectUser" parameterType="java.util.Map" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>
```

#{}里面的名称对应的是Map里面的key名称。

这种方法适合传递多个参数，且参数易变能灵活传递的情况。

### 方法4：Java Bean传参法

public User selectUser(User user);

```xml
<select id="selectUser" parameterType="com.jourwon.pojo.User" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>
```

#{}里面的名称对应的是User类里面的成员属性。

这种方法直观，需要建一个实体类，扩展不容易，需要加属性，但代码可读性强，业务逻辑处理方便，推荐使用。



## MyBatis 有几种分页方式？

数组分页

sql分页

拦截器分页

RowBounds分页

## MyBatis 逻辑分页和物理分页的区别是什么？

1：逻辑分页 内存开销比较大,在数据量比较小的情况下效率比物理分页高;在数据量很大的情况下,内存开销过大,容易内存溢出,不建议使用

2：物理分页 内存开销比较小,在数据量比较小的情况下效率比逻辑分页还是低,在数据量很大的情况下,建议使用物理分页



## MyBatis是如何进行分页的？分页插件的原理是什么？

Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，对其添加对应的物理分页语句和物理分页参数。

举例：select * from student，拦截sql后重写为：select t.* from (select * from student) t limit 0, 10


## MyBatis的xml文件和Mapper接口是怎么绑定的？

是通过xml文件中，<mapper> 根标签的namespace属性进行绑定的，即namespace属性的值需要配置成接口的全限定名称，MyBatis内部就会通过这个值将这个接口与这个xml关联起来。

## 当实体类中的属性名和表中的字段名不一样 ，怎么办？

第1种： 通过在查询的SQL语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

```xml
<select id="getOrder" parameterType="int" resultType="com.jourwon.pojo.Order">
       select order_id id, order_no orderno ,order_price price form orders where order_id=#{id};
</select>
```

第2种： 通过<resultMap>来映射字段名和实体类属性名的一一对应的关系。

```xml
<select id="getOrder" parameterType="int" resultMap="orderResultMap">
	select * from orders where order_id=#{id}
</select>

<resultMap type="com.jourwon.pojo.Order" id="orderResultMap">
    <!–用id属性来映射主键字段–>
    <id property="id" column="order_id">

    <!–用result属性来映射非主键字段，property为实体类属性名，column为数据库表中的属性–>
    <result property ="orderno" column ="order_no"/>
    <result property="price" column="order_price" />
</reslutMap>
```



## Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；毕竟namespace不是必须的，只是最佳实践而已。

原因就是namespace+id是作为Map<String, MappedStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。



## Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

第一种是使用<resultMap>标签，逐一定义列名和对象属性名之间的映射关系。

第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。



## Mybatis动态sql是做什么的？都有哪些动态sql？

Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。



## Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

还有很多其他的标签，<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，其中<sql>为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签。


## MyBatis里如何实现一对多关联查询？

一对多映射有两种配置方式，都是使用collection标签实现的。在此之前，为了能够存储一对多的数据，需要在主表对应的实体类中增加集合属性，用于封装子表对应的实体类。

### 嵌套查询：

1. 通过select标签定义查询主表的SQL，返回结果通过reusltMap进行映射。
2. 在resultMap中，除了映射主表属性，还要通过collection标签映射子表属性，该标签需包含如下内容：
   - 通过property属性指定子表属性名；
   - 通过javaType属性指定封装子表属性的集合类型；
   - 通过ofType属性指定子表的实体类型；
   - 通过select属性指定查询子表所依赖的SQL，这个SQL需单独定义，内部包含查询子表的语句。

### 嵌套结果：

1. 通过select标签定义关联查询主表和子表的SQL，返回结果通过resultMap进行映射。
2. 在resultMap中，除了映射主表属性，还要通过collection标签映射子表属性，该标签需包含如下内容：
   - 通过property属性指定子表属性名；
   - 通过ofType属性指定子表的实体类型；
   - 通过result子标签定义子表字段和属性的映射关系。
