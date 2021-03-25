# Spring

### spring的概述[了解]

###### spring是什么？

###### spring的两大核心

IOC（反转控制）  和AOP（面向切面编程）

###### spring的发展历程和优势

###### spring的体系结构

### 程序的耦合以及解耦

耦合：程序间的依赖关系

​	包括：

​		类之间的依赖：

​		方法间的依赖：

解耦：降低程序间的依赖关系

实际开发中应该做到：

​	编译时不依赖，运行时才依赖

解耦的思路：

​	1.使用反射来创建对象，而避免使用new关键字

​	2.通过读取配置文件来获取要创建的对象全限定类名

Bean：在计算机英语中，有可重用组件的含义。

javabean：用java语言编写的可重用组件。javabean >实体类	

工厂模式解耦



# IOC概念（反转控制）

IOC：把创建对象的权利交给框架，是框架的重要特征，并非面向对象编程的专用术语。它包括依赖注入（DI）、和依赖查找。

IOC的作用：削减计算机程序的耦合（解除我们代码中的依赖关系）。（并不能完全消除依赖关系）

### **spring中基于xml的IOC环境搭建**

```xml
<!--把对象的创建交给spring来管理-->
spring对bean的管理细节
    1.创建bean的三种方式
    2.bean对象的作用范围
    3.bean对象的生命周期


<!--创建bean的三种方式-->
1. 创建bean的第一种方式 使用默认构造函数创建
        在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时，
        采用的就是默认构造函数创建bean对象，此时如果没有默认构造函数，则对象无法创建
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>

2. 创建bean的第二种方式  使用使用普通工厂中的方法创建对象（某个类中的方法创建对象），并存入spring容器
<bean id="instanceFactory" class="com.itheima.factory.InstanceFactory"></bean>
<bean id="accountService"  factory-bean="instanceFactory" factory-method="getAccountService"></bean>

3. 创建bean的第三种方式  使用某个类中的静态方法创建对象，并存入spring容器
<bean id="accountService"  class ="com.itheima.factory.InstanceFactory"  factory-method="getAccountService"></bean>


<!--bean的作用范围调整-->
    bean标签的scope属性：用于执行bean的作用范围
        取值：常用是单例和多例
            singleton：单例，默认值
            prototype：多例
            request：作用于web应用的请求范围
            session：作用于web应用的会话范围
            global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session


<!--bean对象的生命周期-->
 单例对象：
        出生：当容器创建时，对象出生
        活着：只要容器还在，对象就在
        死亡：容器销毁，对象死
        总结：单例对象的声明周期和容器相同
 多例对象：
        出生：当我们使用对象时spring框架为我们创建
        或者：对象使用过程中一直活着
        死亡：当对象长时间不用，且没有别的对象引用时，由java的垃圾回收器回收
```





### 依赖注入（Dependency Injection)

依赖注入：依赖关系的维护。能注入的数据有三类：

​	1.基本类型和String

​	2.其他bean类型（在配置文件中或者注解配置过的bean）

​	3.复杂类型/集合类型

注入的方式有三种：

​	1.使用构造函数提供

​	2.使用set方法提供

​	3.使用注解提供

依赖关系的管理：以后都交给spring来维护。在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明

```xml
<!--构造函数多参数注入-->
constructor-arg的属性
    type: 用于指定要注入的数据的数据类型，改数据类型也是构造函数中某个或某些参数的类型
    index: 用于指定要注入的数据给构造函数中指定索引位置的参数赋值，参数索引的位置从0开始
    name: 用于指定给构造函数中指定名称的参数赋值
    =======以上三个用于指定给构造函数中哪个参数赋值  常用name=========
    value: 用于提供基本类型和String类型的数据
    ref: 指定其他的bean类型的数据，可以通过id引用。它指的是在spring的Ico容器中出现过的bean对象

    优势：在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功
    弊端：改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供

<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
        <constructor-arg name="name" value="test"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="now"></constructor-arg>
</bean>-->
<!--配置一个日期对象-->
<bean id="now" class="java.util.Date"></bean>-->


<!--set方法注入-->
property标签
    出现的位置：bean标签的内部
    标签的属性：
        name: 用于指定注入时所调用的set方法名称
        value: 用于提供基本类型和String类型的数据
        ref: 指定其他的bean类型的数据，可以通过id引用。它指的是在spring的Ico容器中出现过的bean对象
    优势：创建对象时，没有明确的限制，可以直接使用默认构造函数
    弊端：如果有某个成员必须有值，则获取对象时set方法可能没有执行
<bean id="accountService2" class="com.itheima.service.impl.AccountServiceImpl2">       			<property name="name" value="TEST"></property>
        <property name="age" value="23"></property>
        <property name="birthday" value="TEST"></property>
</bean>


<!--复杂类型的注入/集合类型的注入
    用于给List结构注入的标签
        list array set
    用于给Map结构集合注入的标签：
        map props

    ===结构相同，标签可以互换===
-->
<bean id="accountService3" class="com.itheima.service.impl.AccountServiceImpl3">
    <property name="myStrs">
        <array>
            <value>AAA</value>
            <value>BBB</value>
            <value>ccc</value>
        </array>
    </property>
    <property name="myList">
        <list>
            <value>AAA</value>
            <value>BBB</value>
            <value>ccc</value>
        </list>
    </property>
    <property name="mySet">
        <set>
            <value>AAA</value>
            <value>BBB</value>
            <value>ccc</value>
        </set>
    </property>
    <property name="myMap">
        <map>
            <entry key="aaa" value="aaa"></entry>
            <entry key="bbb" value="bbb"></entry>
            <entry key="bbb" value="bbb"></entry>
        </map>
    </property>
    <property name="myProps">
        <props>
            <prop key="aaa">aaa</prop>
            <prop key="bbb">bbb</prop>
            <prop key="ccc">ccc</prop>
        </props>
    </property>
</bean>
```



### spring中ioc的常用注解

当使用注解配置时，需要配置component-scan

```xml
<!--告知spring在创建容器是要扫描的包，配置所需要的的标签不是在beans的约束中，而是一个名称为context名称空间和约束中-->
<context:component-scan base-package="com.itheima"></context:component-scan>
```

```java
学习ioc注解

//1.用户创建对象的：他们的作用就和在xml配置文件中编写一个bean标签实现的功能是一样的
@component
     作用：用于把当前类的对象存入spring容器中
     属性：
         value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母改小写。
@Controller：一般用于表现层
@Service：一般用在业务层
@Repository：一般用于持久层
     
以上三个注解他们的作用和属性与Component一模一样
他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰

```

```java
//2.用于注入数据的：他们的作用就和在xml配置文件中bean标签中写一个property标签的作用是一样的
@Autowired:
    作用：自动按照--类型--注入，只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
    出现位置：可以是变量上，也可以是方法上
    细节：在使用注解注入时，set方法就不是必须的了
@Qualifier:
	依赖Autowired，需要配合Atuwired注解一起使用
    作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用，但是在给方法参数注入时可以。
    属性：
        value：用于指定注入bean的id
@Resource
    作用：直接按照bean的id注入，可以独立使用
    属性
         name：用于指定bean的id
 
 以上三个注入都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现
 另外，集合类型的注入只能通过xml来实现

 @Value
    作用：用于注入基本类型和String类型的数据
    属性：
        value：用于指定数据的值。可以使用spring中的SpEl（也就是spring中的el表达式）
                SpEl的写法：${表达式}


```

```java
//3.用于改变作用范围的：他们的作用就和在bean标签中使用scope属性实现的功能是一样的
     @scope
         作用：用于指定bean的作用范围
         属性：
             value：指定范围的取值。常用取值：singleton（单例）     prototype（多例）  默认为单例
```

```java
//4.和生命周期相关：他们的作用就和在bean标签中使用init-methosd和destroy-method的作用一样的 【了解】
     @PreDestroy
         作用：用于指定销毁方法
     @PostConstruct
         作用：用于指定初始化方法
```



### spring和Junit的整合

1.应用程序的入口

​	main方法

2.junit单元测试中没没有main方法也能执行

​	junit继承了一个main方法

​	该方法会判断当前测试类中有哪些方法有@Test注解

​	junit就让有Test注解的方法执行

3.junit不会管我们是否采用spring框架

​	在执行测试方法时，junit根本不知道我们是否使用了spring框架，所以也就不会为我们读取配置文件或配置类，创建spring核心容器

4. 由以上三点可知，当测试方法执行时，没有Ioc容器，就算写了Autowired注解，也无法实现注入







# AOP的概念

简单地说它是把我们的程序重复的代码抽取出来，在需要执行的时候，使用动态代理的技术，在不修改源码的基础上，对我们的已有方法进行增强。

作用：在程序运行期间，不修改源码，对已有的方法进行增强

优势：

​	减少重复代码

​	提高开发效率

​	维护方便

AOP的实现方式：使用**动态代理**的技术，AOP的底层就是动态代理，容器中保存的组件是他的代理对象

我们学习spring的aop，是通过配置的方式

### spring中的AOP相关术语

**Joinpoint（连接点）：**

​	所谓连接点是指那些被拦截到的点，在spring中，这些点指的是方法，因为spring只支持方法类型的连接点。

**Pointcut（切入点）：**

​	所谓切入点是指我们要对哪些Joinpoint进行拦截的定义。被增强的连接点。

**Advice（通知/增强）：**

​	所谓通知是指拦截到Joinpoint之后所要做的事情。

​	通知的类型：前置通知，后置通知，异常通知，最终通知，环绕通知

**Introduction（引介）：**

​	引介是一种特殊的通知在不修改代码的前提下，Introduction可以在运行期为类动态的添加一些方法或Field

**Target（目标对象）：**

​	代理的目标对象

**Weaving（织入）：**

​	是指把增强应用到目标对象来创建新的代理对象的过程。

​	spring采用动态代理织入，而AspectJ采用编译器织入和类装载期织入

**Proxy（代理）：**

​	一个被AOP织入增强后，就产生一个结果代理类。

**Aspect（切面）：**

​	是切入点和通知（引介）的结合



### spring中基于xml和注解的AOP配置

注解配置简单，但是spring通知顺序有bug，建议用环绕通知

```java
/**
 * 接口不加在容器中
 * 实际上可以加，加了也不创建对象，只要一看这个组件是一个接口
 * 相当于告诉Spring，ioc容器中可能有这种类型的组件
 */
```

基于注解的AOP配置

```xml
<!--开启基于注解的AOP-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

```java
@Component
@Aspect
public class LogUtils {

    /**
     * 告诉spring每个方法都什么时候运行
     * try{
     	 	@Befor
     		method.invok(Obj, args);
    		@AfterReturning

       }catch(e){
  		  @AfterThrowing
       }finally{
    	  @After
      }


     * @Before： 在目标方法之前运行   前置通知
     * @After: 在目标方法结束之后     后置通知
     * @AfterReturning: 在目标方法正常返回后   返回通知
     * @AfterThrowing： 在目标方法抛出异常之后    异常通知
     * @Around: 环绕通知
     */

    /**
     * 抽取可重用的切入点表达式：
     * 1. 随便声明一个没有实现的返回void的空方法
     * 2. 给方法标注@Pointcut注解
     */
    @Pointcut("execution(public int com.atguigu.impl.MyMathCalculator.*(int,int))")
    public void myPoint() {

    }

    //想在目标方法执行之前执行
    //excution(方法签名)
    @Before(value = "myPoint()")
    public static void logStart(JoinPoint joinPoint) {
        //获取到目标方法运行时使用的参数
        Object[] args = joinPoint.getArgs();
        //获取方法名
        String name = joinPoint.getSignature().getName();

        System.out.println("【" + name + "】方法开始执行");
    }

    //想在目标方法执行完成之后执行
    //告诉springresult用来接收返回值 只有AfterReturnning才有returnning属性
    @AfterReturning(value = "execution(public int com.atguigu.impl.MyMathCalculator.*(int,int))", returning = "result")
    public static void logReturn(JoinPoint joinPoint, Object result) {

        System.out.println("[xxx]方法执行完成，返回值=" + result);
    }

    //想在目标方法执行出现异常执行
    //告诉spring exception用来接收异常对象 ，只有afterThrowing才有
    @AfterThrowing(value = "execution(public int com.atguigu.impl.MyMathCalculator.*(int,int))", throwing = "exception")
    public static void logException(JoinPoint joinPoint, Exception exception) {
        System.out.println("[xxx]方法执行出现异常:" + exception);
    }

    //想在目标方法结束的时候执行
    @After("execution(public int com.atguigu.impl.MyMathCalculator.*(int,int))")
    public static void logEnd() {
        System.out.println("[xxx]方法执行结束");
    }


    /**
     * 环绕通知：是Spring中最强大的通知
     *
     * @return
     */
    @Around("myPoint()")
    public Object myAround(ProceedingJoinPoint pjp) throws Throwable {

        //获取方法的参数
        Object[] args = pjp.getArgs();

        Object proceed = null;
        try {

            System.out.println("环绕前置");

            //就是利用反射调用目标方法即可,就是method.invoke(obj,args)
            proceed = pjp.proceed(args);

            System.out.println("环绕返回通知afterreturning");

        } catch (Exception e) {
            System.out.println("环绕异常通知");

        } finally {

            System.out.println("环绕后置通知after");
        }


        //反射调用后的返回值也一定返回出去
        return proceed;
    }


}
```



# spring中的JdbcTemplate

​	JdbcTemplate的作用：

​		用于和数据交互的，实现对表的CRUD操作



# Bean

### Spring创建bean的三种方式

#### 1.调用构造器创建Bean

调用构造方法创建Bean是**最常用**的一种情况，Spring容器通过**new关键字**调用构造器来创建Bean实例，通过class属性指定Bean实例的实现类。调用构造方法创建Bean实例，我们需要为该Bean类提供无参数的构造器。

```xml
<bean id="person" class="com.mao.gouzao.Person">
	  <!-- 通过调用setName方法，将VipMao作为参数传入 -->
	  <property name="name" value="VipMao"></property>
</bean>

```

另外我们定义了一个name属性，并提供了setter方法，**Spring容器通过setter方法为name属性注入参数。**



测试

```java
 public static void main(String[]args)  
    {  
        //创建Spring容器  
        ApplicationContext ctx=new ClassPathXmlApplicationContext("beans.xml");  
        //通过getBean()方法获取Bean实例  
        Person person=(Person) ctx.getBean("person");  
        person.input();  
    }  
```





#### 2.通过静态工厂方法创建Bean

顾名思义，咱们把创建Bean的任务交给了静态工厂，而不是构造函数，这个静态工厂就是一个Java类，那么使用静态工厂创建Bean咱们又需要添加哪些属性呢？我们同样需要在<bean/>元素内添加class属性，上面也说了，静态工厂是一个Java类，那么该**class属性指定的就是该工厂的实现类**，而不再是Bean的实现类，告诉Spring这个Bean应该由哪个静态工厂创建，另外我们还需要添加**factory-method属性来指定由工厂的哪个方法来创建Bean实例**，因此使用静态工厂方法创建Bean实例需要为<bean/>元素指定如下属性：

**class**：指定静态工厂的实现类，告诉Spring该Bean实例应该由哪个静态工厂创建（指定工厂地址）

**factory-method:**指定由静态工厂的哪个方法创建该Bean实例（指定由工厂的哪个车间创建Bean）

如果静态工厂方法需要参数，则使用**<constructor-arg.../>**元素传入

下面是一个简单的通过静态工厂方法创建Bean

```xml
 <bean id="chinese" class="com.staticFactory.PersonFactory" factory-method="getPerson">  
      <constructor-arg value="chinese"/>  
      <!-- 调用setMsg()方法为msg属性注入参数值 -->  
      <property name="msg" value="我是中国人"/>  
 </bean>     
```

class: 指向静态工厂类的全类名 

factory-method: 指向静态工厂中返回bean 实例的方法

constructor-arg: 可以传入参数选择返回的bean 实例

property：调用实例的set方法)方法为属性注入参数值



Person接口

```java
package com.mao.staticFactory;  
  
public interface Person   
{  
    public void say();  
}  
```



Chinese Bean实现类Chinese.java

```java
package com.mao.staticFactory;  
  
public class Chinese implements Person  
{  
    private String msg;  
    //提供setter方法  
    public void setMsg(String msg)   
    {  
        this.msg = msg;  
    }  
    @Override  
    public void say()   
    {  
        // TODO Auto-generated method stub  
        System.out.println(msg+"，打倒一切美帝国主义");  
    }  
  
}  
```



静态工厂

```java
package com.mao.staticFactory;  
  
public class PersonFactory {  
    public static Person getPerson(String arg)  
    {  
    if(arg.equalsIgnoreCase("chinese"))  
    {  
        return new Chinese();  
    }     
    else  
    {  
        return new American();  
    }  
  }  
} 
```



测试

```java
public static void main(String []args)  
    {  
        //创建容器  
        ApplicationContext ctx=new ClassPathXmlApplicationContext("beans.xml");  
        //获取相应实体  
        Chinese c=(Chinese) ctx.getBean("chinese" , Person.class);  
        c.say();  
        American a=(American) ctx.getBean("american" , Person.class);  
        a.say();  
    }  
```





#### 3.通过实例工厂方法创建Bean

静态工厂通过class指定静态工厂实现类然后通过相应的方法创建即可，调用实例工厂则需要先创建该工厂的Bean实例，然后引用该实例工厂Bean的id创建其他Bean，在实例工厂中通过factory-bean指定工厂Bean的实例，在调用实例化工厂方法中，不用在<bean/>中指定class属性，因为这时，咱们不用直接实例化该Bean，而是通过调用实例化工厂的方法，创建Bean实例，调用实例化工厂需要为<bean/>指定一下两个属性

factory-bean ：该属性指定工厂Bean的id

factory-method：该属性指定实例工厂的工厂方法。

下面是我们将上面调用静态方法的例子稍微改一下

```xml
 <!-- 配置工厂Bean，class指定该工厂的实现类，该Bean负责产生其他Bean实例 --> 
<bean id="personFactory" class="com.mao.instanceFactory.PersonFactory"/>  

<!-- 由实例工厂Bean的getPerson()方法，创建Chinese Bean， -->  
<bean id="ch" factory-bean="personFactory" factory-method="getPerson">  
   <!-- 为该方法传入参数为chinese -->  
  <constructor-arg value="chinese"/>  
</bean>  
 <!-- 由实例工厂Bean的getPerson()方法，创建American Bean， -->  
<bean id="usa" factory-bean="personFactory" factory-method="getPerson">  
   <constructor-arg value="american"></constructor-arg>  
</bean>  
```



#### 调用实例工厂创建Bean和调用静态工厂的区别

   调用实例工厂将工厂单独拿了出来(先实例化工厂)创建一个工厂Bean，通过工厂<bean>的class属性指定工厂的实现类，然后再需要创建其他Bean时，只需要在该<bean/>元素添加factory-bean、factory-method指定该Bean是有哪个实例工厂的哪个方法创建就可以了，放在现实生活中就是：我们先找一个地方创建一个工厂，id指定工厂注册名字（xxx有限公司），class指定公司所在地，工厂里面有车间（创造其他Bean的方法），那好有了工厂我们再需要创建其他Bean时，只需要指定这个Bean是由，哪个工厂的哪个车间创建的即可，也就是 只需要在该<bean/>元素添加factory-bean、factory-method属性即可



### Bean的生命周期





