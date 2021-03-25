# 自动化构建工具：Maven



构建过程中的各个环节   可以自动测试、打包、安装、部署

【1】清理clean：将以前编译得到的旧的class字节码文件删除，为下一次编译做准备

【2】编译compile：将Java源程序 编译成  class字节码文件

【3】测试test：自动测试，自动调用junit程序

【4】报告：测试程序执行的结果

【5】打包package：创建JAR/WAR包如在 pom.xml 中定义提及的包

【6】安装至本地仓库install：Maven特定的概念——将打包得到的文件复制到“仓库”中的指定位置，以供其他项目使用

【7】部署到远程仓库deploy：拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程

## Maven的生命周期



![1610503547691](.\images\1610503547691.png)

| 阶段          | 处理     | 描述                                                     |
| :------------ | :------- | :------------------------------------------------------- |
| 验证 validate | 验证项目 | 验证项目是否正确且所有必须信息是可用的                   |
| 编译 compile  | 执行编译 | 源代码编译在此阶段完成                                   |
| 测试 Test     | 测试     | 使用适当的单元测试框架（例如JUnit）运行测试。            |
| 包装 package  | 打包     | 创建JAR/WAR包如在 pom.xml 中定义提及的包                 |
| 检查 verify   | 检查     | 对集成测试的结果进行检查，以保证质量达标                 |
| 安装 install  | 安装     | 安装打包的项目到本地仓库，以供其他项目使用               |
| 部署 deploy   | 部署     | 拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程 |

为了完成 default 生命周期，这些阶段（包括其他未在上面罗列的生命周期阶段）将被按顺序地执行。

Maven 有以下三个标准的生命周期：

- **clean**：项目清理的处理
- **default(或 build)**：项目部署的处理
- **site**：项目站点文档创建的处理

### Clean 生命周期

当我们执行 mvn post-clean 命令时，Maven 调用 clean 生命周期，它包含以下阶段：

- pre-clean：执行一些需要在clean之前完成的工作
- clean：移除所有上一次构建生成的文件
- post-clean：执行一些需要在clean之后立刻完成的工作

mvn clean 中的 clean 就是上面的 clean，在一个生命周期中，运行某个阶段的时候，它之前的所有阶段都会被运行，也就是说，如果执行 mvn clean 将运行以下两个生命周期阶段：

```
pre-clean, clean
```

如果我们运行 mvn post-clean ，则运行以下三个生命周期阶段：

```
pre-clean, clean, post-clean
```

我们可以通过在上面的 clean 生命周期的任何阶段定义目标来修改这部分的操作行为。

在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 pre-clean、clean 和 post-clean 阶段中。这样我们可以在 clean 生命周期的各个阶段显示文本信息。

我们已经在 C:\MVN\project 目录下创建了一个 pom.xml 文件。

<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
   <plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-antrun-plugin</artifactId>
   <version>1.1</version>
   <executions>
      <execution>
         <id>id.pre-clean</id>
         <phase>pre-clean</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>pre-clean phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.clean</id>
         <phase>clean</phase>
         <goals>
          <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>clean phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.post-clean</id>
         <phase>post-clean</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>post-clean phase</echo>
            </tasks>
         </configuration>
      </execution>
   </executions>
   </plugin>
</plugins>
</build>
</project>

现在打开命令控制台，跳转到 pom.xml 所在目录，并执行下面的 mvn 命令。

```
C:\MVN\project>mvn post-clean
```

Maven 将会开始处理并显示 clean 生命周期的所有阶段。

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [post-clean]
[INFO] ------------------------------------------------------------------
[INFO] [antrun:run {execution: id.pre-clean}]
[INFO] Executing tasks
     [echo] pre-clean phase
[INFO] Executed tasks
[INFO] [clean:clean {execution: default-clean}]
[INFO] [antrun:run {execution: id.clean}]
[INFO] Executing tasks
     [echo] clean phase
[INFO] Executed tasks
[INFO] [antrun:run {execution: id.post-clean}]
[INFO] Executing tasks
     [echo] post-clean phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: < 1 second
[INFO] Finished at: Sat Jul 07 13:38:59 IST 2012
[INFO] Final Memory: 4M/44M
[INFO] ------------------------------------------------------------------
```

你可以尝试修改 mvn clean 命令，来显示 pre-clean 和 clean，而在 post-clean 阶段不执行任何操作。

------

### Default (Build) 生命周期

这是 Maven 的主要生命周期，被用于构建应用，包括下面的 23 个阶段：

| 生命周期阶段                                | 描述                                                         |
| :------------------------------------------ | :----------------------------------------------------------- |
| validate（校验）                            | 校验项目是否正确并且所有必要的信息可以完成项目的构建过程。   |
| initialize（初始化）                        | 初始化构建状态，比如设置属性值。                             |
| generate-sources（生成源代码）              | 生成包含在编译阶段中的任何源代码。                           |
| process-sources（处理源代码）               | 处理源代码，比如说，过滤任意值。                             |
| generate-resources（生成资源文件）          | 生成将会包含在项目包中的资源文件。                           |
| process-resources （处理资源文件）          | 复制和处理资源到目标目录，为打包阶段最好准备。               |
| compile（编译）                             | 编译项目的源代码。                                           |
| process-classes（处理类文件）               | 处理编译生成的文件，比如说对Java class文件做字节码改善优化。 |
| generate-test-sources（生成测试源代码）     | 生成包含在编译阶段中的任何测试源代码。                       |
| process-test-sources（处理测试源代码）      | 处理测试源代码，比如说，过滤任意值。                         |
| generate-test-resources（生成测试资源文件） | 为测试创建资源文件。                                         |
| process-test-resources（处理测试资源文件）  | 复制和处理测试资源到目标目录。                               |
| test-compile（编译测试源码）                | 编译测试源代码到测试目标目录.                                |
| process-test-classes（处理测试类文件）      | 处理测试源码编译生成的文件。                                 |
| test（测试）                                | 使用合适的单元测试框架运行测试（Juint是其中之一）。          |
| prepare-package（准备打包）                 | 在实际打包之前，执行任何的必要的操作为打包做准备。           |
| package（打包）                             | 将编译后的代码打包成可分发格式的文件，比如JAR、WAR或者EAR文件。 |
| pre-integration-test（集成测试前）          | 在执行集成测试前进行必要的动作。比如说，搭建需要的环境。     |
| integration-test（集成测试）                | 处理和部署项目到可以运行集成测试环境中。                     |
| post-integration-test（集成测试后）         | 在执行集成测试完成后进行必要的动作。比如说，清理集成测试环境。 |
| verify （验证）                             | 运行任意的检查来验证项目包有效且达到质量标准。               |
| install（安装）                             | 安装项目包到本地仓库，这样项目包可以用作其他本地项目的依赖。 |
| deploy（部署）                              | 将最终的项目包复制到远程仓库中与其他开发者和项目共享。       |

有一些与 Maven 生命周期相关的重要概念需要说明：

当一个阶段通过 Maven 命令调用时，例如 mvn compile，只有该阶段之前以及包括该阶段在内的所有阶段会被执行。

不同的 maven 目标将根据打包的类型（JAR / WAR / EAR），被绑定到不同的 Maven 生命周期阶段。

在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 Build 生命周期的一部分阶段中。这样我们可以显示生命周期的文本信息。

我们已经更新了 C:\MVN\project 目录下的 pom.xml 文件。

<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-antrun-plugin</artifactId>
<version>1.1</version>
<executions>
   <execution>
      <id>id.validate</id>
      <phase>validate</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>validate phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.compile</id>
      <phase>compile</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>compile phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.test</id>
      <phase>test</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>test phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
         <id>id.package</id>
         <phase>package</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
         <tasks>
            <echo>package phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.deploy</id>
      <phase>deploy</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
      <tasks>
         <echo>deploy phase</echo>
      </tasks>
      </configuration>
   </execution>
</executions>
</plugin>
</plugins>
</build>
</project>

现在打开命令控制台，跳转到 pom.xml 所在目录，并执行以下 mvn 命令。

```
C:\MVN\project>mvn compile
```

Maven 将会开始处理并显示直到编译阶段的构建生命周期的各个阶段。

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [compile]
[INFO] ------------------------------------------------------------------
[INFO] [antrun:run {execution: id.validate}]
[INFO] Executing tasks
     [echo] validate phase
[INFO] Executed tasks
[INFO] [resources:resources {execution: default-resources}]
[WARNING] Using platform encoding (Cp1252 actually) to copy filtered resources,
i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory C:\MVN\project\src\main\resources
[INFO] [compiler:compile {execution: default-compile}]
[INFO] Nothing to compile - all classes are up to date
[INFO] [antrun:run {execution: id.compile}]
[INFO] Executing tasks
     [echo] compile phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: 2 seconds
[INFO] Finished at: Sat Jul 07 20:18:25 IST 2012
[INFO] Final Memory: 7M/64M
[INFO] ------------------------------------------------------------------
```

#### 命令行调用

在开发环境中，使用下面的命令去构建、安装工程到本地仓库

```
mvn install
```

这个命令在执行 install 阶段前，按顺序执行了 default 生命周期的阶段 （validate，compile，package，等等），我们只需要调用最后一个阶段，如这里是 install。

在构建环境中，使用下面的调用来纯净地构建和部署项目到共享仓库中

```
mvn clean deploy
```

这行命令也可以用于多模块的情况下，即包含多个子项目的项目，Maven 会在每一个子项目执行 clean 命令，然后再执行 deploy 命令。

------

### Site 生命周期

Maven Site 插件一般用来创建新的报告文档、部署站点等。

- pre-site：执行一些需要在生成站点文档之前完成的工作
- site：生成项目的站点文档
- post-site： 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
- site-deploy：将生成的站点文档部署到特定的服务器上

这里经常用到的是site阶段和site-deploy阶段，用以生成和发布Maven站点，这可是Maven相当强大的功能，Manager比较喜欢，文档及统计数据自动生成，很好看。 在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 Site 生命周期的所有阶段中。这样我们可以显示生命周期的所有文本信息。

我们已经更新了 C:\MVN\project 目录下的 pom.xml 文件。

<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-antrun-plugin</artifactId>
<version>1.1</version>
   <executions>
      <execution>
         <id>id.pre-site</id>
         <phase>pre-site</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>pre-site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.site</id>
         <phase>site</phase>
         <goals>
         <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.post-site</id>
         <phase>post-site</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>post-site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.site-deploy</id>
         <phase>site-deploy</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>site-deploy phase</echo>
            </tasks>
         </configuration>
      </execution>
   </executions>
</plugin>
</plugins>
</build>
</project>

现在打开命令控制台，跳转到 pom.xml 所在目录，并执行以下 mvn 命令。

```
C:\MVN\project>mvn site
```

Maven 将会开始处理并显示直到 site 阶段的 site 生命周期的各个阶段。

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [site]
[INFO] ------------------------------------------------------------------
[INFO] [antrun:run {execution: id.pre-site}]
[INFO] Executing tasks
     [echo] pre-site phase
[INFO] Executed tasks
[INFO] [site:site {execution: default-site}]
[INFO] Generating "About" report.
[INFO] Generating "Issue Tracking" report.
[INFO] Generating "Project Team" report.
[INFO] Generating "Dependencies" report.
[INFO] Generating "Project Plugins" report.
[INFO] Generating "Continuous Integration" report.
[INFO] Generating "Source Repository" report.
[INFO] Generating "Project License" report.
[INFO] Generating "Mailing Lists" report.
[INFO] Generating "Plugin Management" report.
[INFO] Generating "Project Summary" report.
[INFO] [antrun:run {execution: id.site}]
[INFO] Executing tasks
     [echo] site phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: 3 seconds
[INFO] Finished at: Sat Jul 07 15:25:10 IST 2012
[INFO] Final Memory: 24M/149M
[INFO] ------------------------------------------------------------------```
```





## Maven的核心概念

### 1.约定的目录结构

````
Hello

|---src

|--- |  --- main

|--- |  ---  |   ---  java

|--- |  ---  |    --- resources

|--- |  --- test

|--- |  ---  |   ---  java

|--- |  ---  |    --- resources

|---pom.xml
````



[1]根目录：工程名

[2]src目录：源码

[3]pom.xml文件：Maven工程的核心配置文件

[4]main目录：存放主程序

[5]test目录：存放测试目录

[6]java目录：存放Java源文件

[7]resources目录：存放框架或其他工具的配置文件



##### 为什么要遵守约定的目录结构？

maven要负责我们这个项目的自动化构建，以编译为例，maven要想自动进行编译，那么它必须知道java源文件保存在哪里。

如果我们自己自定义的东西想要框架或工具知道，

​	以配置的方式明确告诉框架

​	遵守框架内部已存在的规定



约定>配置>编码





### 2.POM

含义：Project Object Model 项目对象模型

pom.xml是Maven工程的核心配置文件，与构建过程相关的一切设置都在这个文件中进行配置



### 3.坐标

GAV 使用下面三个标签在仓库中唯一定位一个Maven工程

````xml
<groupId>cn.infocore</groupId>  #公司或组织域名倒序+项目名 
<artifactId>dreamer</artifactId>  #模块名
<version>1.0.0-SNAPSHOT</version> #版本
````



Maven工程坐标与仓库中路径的一致



### 4.依赖（重要）

Maven解析依赖信息时会到本地仓库中查找被依赖的jar包

​	对于我们自己开发的Maven工程，执行install命令安装后就可以进入仓库

​         

##### 依赖的范围

<scope>标签决定依赖的范围：

1. compile

   对主程序是否有效：是

   对测试程序是否有效：是

   是否参与打包：是

2. test

   对主程序是否有效：否

   对测试程序是否有效：是

   是否参与打包：否

3. provided

   开发时依赖，部署运行是不依赖 (比如servlet，如果运行环境有tomcat则不需要这个依赖了)

   对主程序是否有效：是

   对测试程序是否有效：是

   是否参与打包：否



##### 依赖的传递性

可以传递的依赖不必在每个模块工程中都重复声明，在“最底层”的工程中依赖一次即可

非compile范围（test provided）不可传递



##### 依赖的排除

````xml
<exclustions>
	<exclustion>
		<groupId>commons-logging</group>
		<artifactId>commons-logging</artifactId>
	</exclustion>
</exclustions>
````



##### 依赖的原则（解决jar包冲突）

作用：解决模块工程质检jar包冲突

情景设定1：验证路径最短者优先原则

![1610529313607](.\images\1610529313607.png)

情景设定2：验证路径相同时先声明者优先

![1610529524937](.\images\1610529524937.png)

先声明指的是dependency标签的声明顺序，如果先声明HelloFriend则使用1.2.14



##### 统一管理依赖的版本

如果一个项目中spring的各个jar包的依赖版本都是4.0.0

如果需要统一升级为4.1.1，怎么办？手动逐一修改不可靠

###### 建议配置方式：

i.使用properties标签内使用自定义标签统一声明版本号

````xml
<properties>
	<atguigu.spring.version>4.0.0.RELEASE</atguigu.spring.version>
</properties>

````

ii.在需要同一版本的位置，使用${自定义标签名}应用声明的版本号

````xml
<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-test</artifactId>
		<version>${atguigu.spring.version}</version>   
		<scope>test</scope>
</dependency>
````



其实properties标签配合自定义标签声明数据的配置不是只能用于声明依赖的版本号



### 5.仓库

##### 本地仓库

当前电脑上部署的仓库目录，为当前电脑上所有的Maven工程服务



##### 远程仓库

1. 私服：搭建在局域网环境中，为局域网范围内的所有Maven工程服务 Nexus
2. 中央仓库：架设在Internet上，为全世界所有Maven工程服务
3. 中央仓库镜像：为了分担中央仓库的流量，提升用户访问速度



##### 仓库中保存的内容：Maven工程

1. Maven自身所需要的插件
2. 第三方框架或工具的jar包 （第一方是jdk 第二方是我们）
3. 我们自己开发的maven工程



### 6.生命周期/插件/目标

Maven 有以下三个标准的生命周期：

- **clean**：项目清理的处理

- **default(或 build)**：项目部署的处理

- **site**：项目站点文档创建的处理

  

##### 各个构建环境执行的顺序

不能打乱顺序，必须按照既定的正确顺序来执行

maven的核心程序定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的

Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期的各个阶段：**不论现在要执行生命周期的哪一个阶段，都是从这个生命周期最初的位置开始执行**  Build生命周期都从compile开始



#### Clean 生命周期

当我们执行 mvn post-clean 命令时，Maven 调用 clean 生命周期，它包含以下阶段：

- pre-clean：执行一些需要在clean之前完成的工作
- clean：移除所有上一次构建生成的文件
- post-clean：执行一些需要在clean之后立刻完成的工作



#### Default (Build) 生命周期

这是 Maven 的主要生命周期，被用于构建应用，包括下面的 23 个阶段：

| 生命周期阶段                                | 描述                                                         |
| :------------------------------------------ | :----------------------------------------------------------- |
| **validate（校验）**                        | 校验项目是否正确并且所有必要的信息可以完成项目的构建过程。   |
| initialize（初始化）                        | 初始化构建状态，比如设置属性值。                             |
| generate-sources（生成源代码）              | 生成包含在编译阶段中的任何源代码。                           |
| process-sources（处理源代码）               | 处理源代码，比如说，过滤任意值。                             |
| generate-resources（生成资源文件）          | 生成将会包含在项目包中的资源文件。                           |
| process-resources （处理资源文件）          | 复制和处理资源到目标目录，为打包阶段最好准备。               |
| **compile（编译）**                         | 编译项目的源代码。                                           |
| process-classes（处理类文件）               | 处理编译生成的文件，比如说对Java class文件做字节码改善优化。 |
| generate-test-sources（生成测试源代码）     | 生成包含在编译阶段中的任何测试源代码。                       |
| process-test-sources（处理测试源代码）      | 处理测试源代码，比如说，过滤任意值。                         |
| generate-test-resources（生成测试资源文件） | 为测试创建资源文件。                                         |
| process-test-resources（处理测试资源文件）  | 复制和处理测试资源到目标目录。                               |
| **test-compile（编译测试源码）**            | 编译测试源代码到测试目标目录.                                |
| process-test-classes（处理测试类文件）      | 处理测试源码编译生成的文件。                                 |
| **test（测试）**                            | 使用合适的单元测试框架运行测试（Juint是其中之 一）。         |
| prepare-package（准备打包）                 | 在实际打包之前，执行任何的必要的操作为打包做准备。           |
| **package（打包）**                         | 将编译后的代码打包成可分发格式的文件，比如JAR、WAR或者EAR文件。 |
| pre-integration-test（集成测试前）          | 在执行集成测试前进行必要的动作。比如说，搭建需要的环境。     |
| integration-test（集成测试）                | 处理和部署项目到可以运行集成测试环境中。                     |
| post-integration-test（集成测试后）         | 在执行集成测试完成后进行必要的动作。比如说，清理集成测试环境。 |
| **verify （验证）**                         | 运行任意的检查来验证项目包有效且达到质量标准。               |
| **install（安装）**                         | 安装项目包到本地仓库，这样项目包可以用作其他本地项目的依赖。 |
| **deploy（部署）**                          | 将最终的项目包复制到远程仓库中与其他开发者和项目共享。       |





### 7.继承

现状：

Hello依赖的junit 4.0

HelloFriend依赖的junit 4.0

MakeFriends依赖的junit 4.9

由于test范围的依赖不能传递，所以必然会分散在各个模块工程中，很容易造成版本不一致



需求：

统一管理各个模块工程对junit依赖的版本



解决思路：

将junit依赖统一提取到父工程中，在子工程中声明依赖时，不指定版本（子工程会以父工程中统一设定为准），同时也便于修改



操作步骤：

1. 创建一个Maven工程作为父工程，注意：打包的方式是**pom**

   ````xml
    <groupId>cn.infocore</groupId>
    <artifactId>dreamer</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
   ````

   

2. 在子工程中声明对父工程的引用

   ````xml
   <parent>
   	<groupId>cn.infocore</groupId>
   	<artifactId>dreamer</artifactId>
   	<version>1.0.0-SNAPSHOT</version>
       
       <!-- 以当前文件为基准的父工程pom.xml文件的相对路径 可以省略-->
       <relativePath>../Parent/pom.xml</relativePath>
   </parent>
   ````

   

3. 将子工程的坐标与父工程坐标中重复的内容删除

4. 在父工程统一声明junit依赖 **dependencyManagement**

   ````xml
   	<dependencyManagement>
   		<dependencies>
   			<dependency>
   				<groupId>junit</groupId>
   				<artifactId>junit</artifactId>
   				<version>4.13</version>
   				<scope>test</scope>
   			</dependency>
   		</dependencies>
   	</dependencyManagement>
   ````

   

5. 在子工程中删除junit依赖的版本号部分



小结： 

**dependencies**即使在子项目中不写该依赖项，那么子项目仍然会从父项目中继承该依赖项（全部继承）

**dependencyManagement**里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。



**注意：配置继承后，执行安装命令时要先安装父工程**



### 8.聚合

作用：一键安装各个模块工程

配置方式：

在一个“总的聚合工程“中配置各个参与聚合的模块

````xml
  <modules>
  	<module>dreamer-model</module>
  	<module>dreamer-controller</module>
  	<module>dreamer-service-api</module>
  	<module>dreamer-service-impl</module>
  	<module>dreamer-web</module>
  	<module>dreamer-dao</module>
  	<module>dreamer-deploy</module>
  </modules>
````





## 常用的Maven命令

注意：执行与<u>构建过程相关</u>的Maven命令，必须进入pom.xml所在的目录

【1】mvn clean：清理

【2】mvn compile：编译主程序，生成的字节码文件保存在target文件夹中

【3】mvn test-compile：编译测试程序

【4】mvn test：执行测试

【5】mvn package：打包



## Maven核心程序

1. Maven的核心程序中仅仅定义了抽象的生命周期，但是具体的工作必须由特定的插件来完成。而插件本身并不包含在Maven的核心程序中

2. 当我们执行的maven命令需要用到某些插件时，maven核心程序会首先到本地仓库中查找

3. 本地仓库的默认位置：[系统中当前用户的家目录] \.m2\repository

   ````
   C:\Users\[登录当前系统的用户名]\.m2\.repository
   ````

4. Maven核心程序如果在本地仓库中找不到需要的插件，那么他会自动连接外网，到中央仓库下载

