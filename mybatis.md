# mybatis

**ORM框架：**ORM（Object Relational Mapping）框架采用[元数据](https://baike.baidu.com/item/%E5%85%83%E6%95%B0%E6%8D%AE/1946090)来描述对象与关系映射的细节，元数据一般采用XML格式，并且存放在专门的对象一映射文件中。只要提供了持久化类与表的映射关系，ORM框架在运行时就能参照映射文件的信息，把对象持久化到数据库中。当前ORM框架主要有五种：Hibernate(Nhibernate)，iBATIS，mybatis，EclipseLink，JFinal。

ORM是通过使用描述对象和数据库之间映射的元数据,在我们想到描述的时候自然就想到了xml和特性(Attribute).目前的ORM框架中,Hibernate就是典型的使用xml文件作为描述实体对象的映射框架,而大名鼎鼎的Linq则是使用特性(Attribute)来描述的。



**元数据：**是描述其它数据的数据 （data about other data），或者说是用于提供某种资源的有关信息的结构数据（structured data）。元数据是描述信息资源或数据等对象的数据，其使用目的在于：识别资源；评价资源；追踪资源在使用过程中的变化；实现简单高效地管理大量网络化数据；实现信息资源的有效发现、查找、一体化组织和对使用资源的有效管理。



---

### 配置文件约束

```html
<!--全局config.xml约束-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">



<!--Mapper.xml的约束-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper  
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

```



### 完整的CRUD操作

1.创建UserDao接口

2.创建UserDaoImpl实现类

3.编写UserDao对应的UserDaoMapper.xml



动态代理 this.userDao = sqlSession.getMapper(UserDao.class);

如果希望使用mybatis通过的动态代理的接口，就需要namespace中的值，和需要对应的Mapper(dao)接口的全路径一致。Mapper中Namespace的定义本身是没有限制的，只要不重复即可，但如果使用Mybatis的DAO接口动态代理，则namespace必须为DAO接口的全路径，例如：com.zpc.mybatis.dao.UserDao

<mapper namespace="com.zpc.mybatis.dao.UserDao">

![1590376303306](.\images\1590376303306.png)







### mybatis-config.xml详解

![1590377092165](.\images\1590377092165.png)







![1590383601347](.\images\1590383601347.png)





#{} 只是替换？，相当于PreparedStatement使用占位符去替换参数，可以防止sql注入。
${} 是进行字符串拼接，相当于sql语句中的Statement，使用字符串去拼接sql；$可以是sql中的任一部分传入到Statement中，不能防止sql注入。

使用${} 去取出参数值信息，需要使用${value}
#{} 只是表示占位，与参数的名字无关，如果只有一个参数，会自动对应。
![1590384158681](.\images\1590384158681.png)





### Mybatis中的延迟加载

问题1：在一对多中，当我们有一个用户，他又100个账户。在查询的时候要不要把关联的账户查出来？

问题2：在查询账户的时候，要不要把关联的用户查出来？

解答1：在查询用户时，用户下的账户信息应该需要使用时再查询

解答2：在查询账户时，账户的所属用户信息应该是随着账户查询时一起查询出来

##### 什么是延迟加载？

​	在真正使用数据时才发起查询，不用的时候不查询，按需加载（懒加载）

##### 什么是立即加载？

​	不管用不用，只要一调用方法，马上发起查询

多对一，一对一：通常情况下采用立即加载

一对多，多对多：通常情况下采用延迟加载







---

### Mybatis中的缓存

##### 什么是缓存？

存在于内存中的临时数据

##### 为什么使用缓存

减少和数据库的交互次数，提高执行效率

##### 什么样的数据能使用缓存，什么样的数据不能使用

适用于缓存：经常查询，并且不经常改变的。数据的正确与否对最终结构影响不大（缓存中数据可能和数据库中不一致）

不适用于缓存：经常改变的数据，数据的正确与否对最终结果的影响很大（如商品库存，银行汇率，股市牌价）

##### Mybatis中的一级缓存和二级缓存

一级缓存：他指的是mybatis中SqlSession对象的缓存。当我们执行查询之后，查询的结果会同事存入到SqlSession为我们提供的一块区域中。该区域的结构是一个Map。当我们再次查询同样的数据，mybatis会先去SqlSession中查看是否有，有的话直接拿出来用。当SqlSession对象消失时，mybatis的一级缓存就消失了。

当调用sqlsession的修改，添加，删除，commit（），close（）等方法时，就会清空一级缓存。

二级缓存：它指的是mybatis中SqlSessionFactory对象的缓存。由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。二级缓存中存放的数据，而不是对象。

二级缓存的使用步骤：

​	1.让Mybatis框架支持二级缓存（在SqlMapConfig.xml中配置）

```xml
<settings>
	<setting name="cacheEnabled" value="ture"/>   不配置也行，默认为 ture
</settings>
```

 	2.让当前的映射文件支持二级缓存（在IUserDao.xml中配置）

```
<cache/>
```

​	3.让当前操作支持二级缓存（在select标签中配置）

```xml
<select  ....  useCache="true">
    
</select>
```







---

### Mybatis中的注解开发

环境搭建

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
		PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--引入外部配置文件 -->
    <properties resouce="jdbcConfig.properties"></properties>
    <!--配置别名-->
    <typeAliases>
        <package name="com.infocore.domain"></package>
    </typeAliases>
    
    <!-- 配置环境-->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"></property>
                <property name="url" value="${jdbc.url}"></property>
                <property name="username" value="${jdbc.username}"></property>
                <property name="password" value="${jdbc.password}"></property>
            </dataSource>
        </environment>
    </environments>

</configuration>
```



针对CURD操作的注解

@Select("select * from user")

@Insert("insesrt info user(username,address) values(#{username},#{address})")

@Update

@Delete

@Results(  id="userMap" ,value={

​	@Result(id=ture, column="id",property="userId"),      //id=true表示是主键

​	@Result(column="username",property="userName"),

​	@Result(property="user", colume="uid", one=@One(select="com.itheima.dao.IUserDao.findById", fetchType=FetchType.EAGER))

})             

@ResultMap("userMap")



在类名上@CacheNamespace（blocking =true） 开启二级缓存



单表CRUD操作（代理Dao方法）

多表查询操作

缓存的配置