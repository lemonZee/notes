# 易混淆知识点

## @Controller和@RestController的区别

知识点：@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。

1) 如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，或者html，配置的视图解析器 InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。

2) 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
    如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。

即

@RestController：返回Return里的内容

@Controller：若配合视图解析器，则返回到指定页面，若配合@RequestBody，则可返回JSON、XML火自定义的内容到页面



## bootstrap和application的区别

用过 Spring Boot 的都知道在 **Spring Boot 中有以下两种配置文件**

- bootstrap (.yml 或者 .properties)
- application (.yml 或者 .properties)

**Spring Cloud 的官方文档**

Spring Cloud 构建于 Spring Boot 之上，在 Spring Boot 中有两种上下文，一种是 bootstrap, 另外一种是 application, bootstrap 是应用程序的父上下文，也就是说 bootstrap 加载优先于 applicaton。bootstrap 主要用于从额外的资源来加载配置信息，还可以在本地外部配置文件中解密属性。这两个上下文共用一个环境，它是任何Spring应用程序的外部属性的来源。**bootstrap 里面的属性会优先加载**，它们默认也不能被本地相同配置覆盖。

**因此，对比 application 配置文件，bootstrap 配置文件具有以下几个特性。**

- boostrap 由父 ApplicationContext 加载，比 applicaton 优先加载
- boostrap 里面的属性不能被覆盖

#### bootstrap/ application 的应用场景

application 配置文件这个容易理解，主要用于 Spring Boot 项目的自动化配置。

bootstrap 配置文件有以下几个应用场景。

- <u>使用 Spring Cloud Config 配置中心时</u>，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
- 一些固定的不能被覆盖的属性
- 一些加密/解密的场景；

# Nacos

Nacos：Dynamic Naming and Configuration Service

可作为服务的注册中心与配置中心。



## 启动时注意

windows下载下来的nacos-server使用的时cluster集群模式，这个模式是要求使用mysql的。

解决办法：（实验环境下将 nacos-server的启动方式改为单例就行了。

编辑 startup.cmd

````
set MODE="cluster"  ->  set Mode="standalone"
````

再次启动

## 服务注册到Nacos

### 引入依赖

在父pom中添加，控制版本

```xml
<dependency> 
    <groupId> com.alibaba.cloud </ groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-discovery </ artifactId> 
    <version>2.1.0.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</ dependency>
```

在子pom中添加

```xml
<dependency> 
    <groupId> com.alibaba.cloud </ groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-discovery </ artifactId> 
</dependency>
```



### nacos服务器启动

访问问[http:// ip:8848](http://ip:8848/)以查看nacous控制台（默认帐户名/密码为nacos / nacos）



### 配置Pom

如何向Nacos注册服务？

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
</dependencies>
```



### 配置application.yaml

```yaml
##properties格式
server.port = 8081 
spring.application.name = cloud-xxx-service
spring.cloud.nacos.discovery.server-addr = 127.0.0.1：8848   ##nacos服务器ip：端口
management.endpoints.web.exposure.include = *   ##暴露所有端点


##yaml格式
server: 
	port: 8081
spring:
	application:
		name: cloud-xxx-service
spring:
	cloud:
		nacous:
			discovery:
				server-addr: 127.0.0.1：8848 

management:
  endpoints:
    web:
      exposure:
        include: '*'
```



### 编写启动类

```java
@SpringBootApplication
@EnableDiscoveryClient  // 注册到nacos需要开启此注解
public class PaymentMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain9001.class, args);
    }
}

```



### 编写Controller

```java
@RestController
public class Payment9001Controller {

    @Value("${server.port}")    //从配置中自动注入server.port
    private String serverPort;

    @ResponseBody
    @GetMapping(value = "/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") String id)
    {
        return "nacos registry, serverPort: "+ serverPort+"\t id:"+id;
    }

}
```

在启动消费者应用程序之前，请启动Nacos。

接下来，访问`http://ip:port/payment/nacos/{id}`使用者提供的界面。

根据以上的例子，访问http://localhost:9001/payment/nacos/{id}



## 配置Nacos config

Nacos同springcloud-config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置之后，才能保证项目的正常启动。



### 导入依赖

```xml
<dependency> 
    <groupId> com.alibaba.cloud </ groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-config </ artifactId> 
</dependency>
```



### 配置Yaml

```yaml
#bootstrap.yaml
server: 
	port: 8081
spring:
	application:
		name: cloud-xxx-service
spiring:
	cloud:
		nacous:
			config:  ##config属性必须放进bootstrap配置中
				- file-extension: yaml
				- namespace: b3404bc0-d7dc-4855-b519-570ed34b62d7  ##默认public
				- group: DEVELOP_GROUP  ##默认DEFAULT_GROUP
```



nacos服务器8848上配置文件的名称要配置为

````
${spring.application.name}-${profile}. ${file-extension:properties}
````

${profile} 为bootstrap.properties中的spring.profiles.activce

当spring.profiles.active为空时，拼接格式为：${spring.application.name}.${file-extension:properties}

根据上述配置，配置文件名称应为 cloud-xxx-service-dev.yaml



### 使用方法

```java
@RestController
@RefreshScope //支持nacos的动态刷新功能
public class configController {

    //在成员变量上使用@Value注解  ${config.info}的值为nacos配置文件中的值
    @Value("${config.info}") 
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```



nacos服务器上配置详情：

![](.\image\Snipaste_2020-09-04_09-49-27.png)



# OpenFeign

### 配置Pom

增加openFeign依赖

```xml
<dependency> 
    <groupId> org.springframework.cloud </ groupId> 
    <artifactId> spring-cloud-starter-openfeign </ artifactId> 
</dependency>
```

### demo

消费者若想调用提供者的接口，需要做以下两件事：

**1.定义接口来调用其他微服务**

```java
@FeignClient(value = "nacos-payment-provider")   //value为调用的微服务名称
public interface PaymentFeignService {   //方法名任意

    //这里为所调用的微服务controller中的方法声明
    @ResponseBody
    @GetMapping(value = "/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") String id);
}
```

**2.调用定义的接口**

```java
@RestController
public class OrderNacosController {

    @Autowired
    private PaymentFeignService paymentFeignService;


    @GetMapping(value = "/consumer/payment/get/{id}")
    public String getPaymentById(@PathVariable("id") String id) {
        return paymentFeignService.getPayment(id);  // 此处调用了接口
    }

}
```

所以，两个服务若想进行远程调用，需要提前定义好接口声明





