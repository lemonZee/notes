

# Active MQ

## 安装

解压即可

启动

```shell
./activemq start
```

停止

```shell
./activemq stop
```



linux端口：61616

web控制台端口：8161

注意，要修改conf/jetty.xml里的127.0.0.1为0.0.0.0，否则8161端口只可供本地访问



## JMS

java 消息 服务：是JavaEE中的一个项目

![image-20200927135136419](.\images\JMS)

### 消息头

message.setJMSxxx()

![image-20200927135409632](.\images\image-20200927135409632.png)



produce.send()

JMSDestination: 消息发送的目的地，主要指Queue和Topic

JMSDeliveryMode：持久模式和非持久模式

​									持久：如果服务器出现故障，消息并不会丢失，会在服务器恢复之后再次传递

​									非持久：如果服务器出现故障，该消息将永远丢失

JMSExpiration：设置消息在一定时间后过期，默认0永不过期

JMSPriority：消息优先级，0-9十个级别，0-4普通消息，5-9加急消息。JMS不要求MQ严格按照这个优先级发送消息，但必须保证加急消息要优先于普通消息。默认是4级。

JMSMessageID：唯一识别每个消息的标识，由MQ产生。 

![image-20200927135518538](.\images\image-20200927135518538.png)



### 消息体

![image-20200927140708277](.\images\image-20200927140708277.png)

![image-20200927140813015](.\images\image-20200927140813015.png)



### 消息属性

![image-20200927141132352](.\images\image-20200927141132352.png)

![image-20200927141212533](.\images\image-20200927141212533.png)

![image-20200927141407751](.\images\image-20200927141407751.png)

setString()也可以



### JMS开发的基本步骤

![image-20200927132353780](.\images\JMS开发的基本步骤)





## Queue：一对一

一条消息只能被一个消费者消费

若有一个生产者，多个消费者，消息会按照轮询的方式分发到各消费者。

### 消息传递特点

![image-20200927132728239](.\images\队列消息传递的特点)





## Topic：一对多

### 消息传递特点

![image-20200927133508767](.\images\主题消息传递的特点)

## Queue和Topic对比

![image-20200927135001832](.\images\queue和topic对比)



## JMS的可靠性

![image-20200927141720372](.\images\image-20200927141720372.png)

### PERSISTENT：持久性

#### 参数设置说明

![image-20200927141926569](.\images\image-20200927141926569.png)

队列默认是持久化的

![image-20200927142524778](.\images\image-20200927142524778.png)



#### 持久化Topic消费者

![image-20200927143235651](.\images\image-20200927143235651.png)

![image-20200927143813832](.\images\image-20200927143813832.png)

### 事务

事务偏生产者  批处理，同时成功或失败

![image-20200927144123810](.\images\image-20200927144123810.png)

提交：session.commit();

回滚：session.rollback();

### Acknowledge：签收

签收偏消费者

#### 非事务

![image-20200927144726606](.\images\image-20200927144726606.png)

![image-20200927151911724](.\images\image-20200927151911724.png)

![image-20200927152032273](.\images\image-20200927152032273.png)

#### 事务

![image-20200927152627353](.\images\image-20200927152627353.png)

### 事务和签收的关系

![image-20200927152734433](.\images\image-20200927152734433.png)

## JMS点对点总结

![image-20200927153059838](.\images\image-20200927153059838.png)



## JMS发布订阅总结

![image-20200927153217654](.\images\image-20200927153217654.png)



## Broker

是什么？

![image-20200927153524603](.\images\image-20200927153524603.png)



可以通过制定不同配置文件来运行不同实例

![image-20200927154014042](.\images\image-20200927154014042.png)

#### 嵌入式Broker

![image-20200927154235222](.\images\image-20200927154235222.png)

例子

![image-20200927154629866](.\images\image-20200927154629866.png)





## SpringBoot 整合ActiveMQ

#### 导入依赖

![image-20200927155546587](.\images\image-20200927155546587.png)

#### 编写application.yaml

```yaml
server:
  port: 7777

spring:
  activemq:
    broker-url: tcp://192.168.75.121:61616  #MQ服务器地址
    user: admin
    password: admin
  jms:
    pub-sub-domain: false  # false=Queue  true=Topic  默认为false


#自己定义队列名称
myqueue: boot-activemq-queue
```

#### 配置bean

队列bean

```java
package com.example.activemqspringboot.config;

import org.apache.activemq.command.ActiveMQQueue;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import javax.jms.Queue;

@Component
public class ConfigBean {

    @Value("${myqueue}")
    private String myQueue;

    @Bean
    public Queue queue() {
        return new ActiveMQQueue(myQueue);
    }
}
```

主题bean

```java
@Component
public class TopicConfigBean {

    @Value("${mytopic}")  //yaml配置文件中的名字
    private String topicName;

    @Bean
    public Topic topic() {
        return new ActiveMQTopic(topicName);
    }
}
```



#### 编写队列生产者

```java
@Component
public class QueueProduce {

    @Autowired
    private JmsMessagingTemplate jmsMessagingTemplate;

    @Autowired
    private Queue queue;

    //触发投送
    public void product() {
        jmsMessagingTemplate.convertAndSend(queue, UUID.randomUUID().toString().substring(0, 6));
    }
    
    //每3秒自动调方法  若要使用Scheduled  要在主启动类上加@EnableScheduling
    @Scheduled(fixedDelay = 3000)
    public void produceMsgScheduled() {
        jmsMessagingTemplate.convertAndSend(queue, UUID.randomUUID().toString().substring(0, 6));
    }
}
```

#### 编写队列消费者（另一个微服务，端口号要修改）

![image-20200927163046851](.\images\image-20200927163046851.png)

#### 编写主题生产者

![image-20200927163735535](.\images\image-20200927163735535.png)

#### 编写主题消费者

和队列一样	

#### 编写启动类

```java
@SpringBootApplication
@EnableJms
public class ActivemqSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(ActivemqSpringbootApplication.class, args);
    }

}
```

#### 编写测试类

```java
@SpringBootTest(classes = ActivemqSpringbootApplication.class)
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
public class TestActiveMQ {

    @Resource
    private QueueProduce queueProduce;

    @Test
    public void testSend() throws Exception {
        queueProduce.product();
    }
}
```



## ActiveMQ支持的网络协议

![image-20200927165234052](.\images\image-20200927165234052.png)

java程序一般使用tcp或nio



## 消息持久化机制

![image-20200928093331510](.\images\image-20200928093331510.png)

![image-20200928101411483](.\images\image-20200928101411483.png)

KahaDB是默认的

工作中主要用到jdbc消息存储和JDBC消息存储withActiveMQJournal

企业里一般用5，5可以看成是3的加强

![image-20200928104859521](.\images\image-20200928104859521.png)

### kahaDB

![image-20200928103322801](.\images\image-20200928103322801.png)

![image-20200928103510348](.\images\image-20200928103510348.png)



![image-20200928104235052](.\images\image-20200928104235052.png)

![image-20200928104412011](.\images\image-20200928104412011.png)

### JDBC消息存储









## 总结

在高可用上，使用主从架构来实现

在消息靠靠性上，有较低的概率会丢失数据

可以使用rabbitMQ来代替

