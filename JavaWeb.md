## HTTP协议
	1. 请求消息
		* 数据格式：
			1. 请求行
			2. 请求头
			3. 请求空行
			4. 请求体
	2. 响应消息
		* 数据格式：
			1. 响应行
				1. 组成： 协议/版本 响应状态码 状态码描述
				2. 响应状态码：
					1. 状态码都是3位数字
					2. 分类：
						1. 1xx：服务器接受客户端消息 但没有接收完成，等待一段时间后 发送 1xx状态码
						2. 2xx：成功。代表：200
						3. 3xx：重定向。代表：302（重定向，资源跳转），304（访问缓存）
						4. 4xx：客户端错误。代表：404（请求路径没有对应的资源）405（请求方式 没有对应的doxxx方法）
						5. 5xx：服务器端错误。代表：500（服务器内部出现异常）
			2. 响应头
				1. 格式：头名称：值
				2. 常见的响应头：
					1. Content-Type：本次响应体数据格式以及编码格式
					2. Content-disposition:服务器告诉客户端以什么格式打开响应体数据
						* in-line: 默认值，在当前页面内打开
						* attachment;filename=xxx: 以附件形式打开响应体。文件下载
			3. 响应空行
			4. 响应体：真实传输的数据

## Response对象
	*功能：设置响应消息
		1. 设置响应行
			1. 格式 HTTP/1.1 200 ok
			2. 设置状态码 setStatus(int sc)
		2. 设置响应头 : setHeader(String name,String value)
		3. 设置响应体:
			* 使用步骤：
				1. 获取输出流
					* 字符输出流:  PrintWriter getWriter()
					* 字节输出流:  ServletOutputStream getOutputStream()
				2. 使用输出流，将数据输出到客户端浏览器


## 重定向
	* 重定向代码实现：
		 //方法1
	    //设置状态码
	    resp.setStatus(302);
	    //设置响应头
	    resp.setHeader("location","/demo/responseDemo2");


        //方法2 简单的重定向方法
        resp.sendRedirect("/demo/responseDemo2");
    * 重定向特点
    	1. 地址栏发生变化
    	2. 可以访问其他服务器的资源
    	3. 是两次请求，不能使用request对象共享数据
    * 转发的特点：
    	1. 转发地址栏路径不变
    	2. 转发只能访问当前服务器下的资源
    	3. 转发只是一次请求，可以通过request对象共享数据
    * 路径写法：
    	1. 路径分类
    		1. 相对路径 不可以确定唯一资源
    			* 以.开头的路径
    		2. 绝对路径 可以确定唯一资源
    			* 以/开头的路径
    	* 建议虚拟目录动态获取：  request.getContextPat()

## 服务器输出字符流数据到浏览器
	1. 注意乱码
		* 简单的形式 设置编码 response.setContentType("text/html;charset=utf-8");
	    //获取字符输出流
	    resp.getWriter().write("你好 response");

## 服务器输出字节流数据到浏览器
		 //告诉浏览器 服务器发送的消息体数据的编码，建议浏览器使用该编码解码
	    resp.setContentType("text/html;charset=utf-8");
	    //获取字节输出流
	    resp.getOutputStream().write("<h1>你好 response</h1>".getBytes("utf-8"));
	1. 验证码
		1. 本质：图片
		2. 目的：防止恶意表单注册

## ServletContext对象
	1. 概念：代表整个web应用，可以和程序的容器来通信
	2. 获取：两种方式获取的是同一对象
		1. 通过request对象获取
			request.getServletContext();
		2. 通过HttpServlet方法获取	
			this.getServletContext();
	3. 功能：
		1. 获取MIME类型：
			* MIME类型：在互联网通信过程中定义的一种文件数据类型
				* 格式： 大类型/小类型  test/html image/jpeg
			* 获取： String getMimeType(String file)
		2. 域对象：共享数据
			* ServletContext() 对象范围：所有用户所有请求的数据
		3. 获取文件的真实路径（服务器路径）
			1. 方法： getRealPath(String path)

* 使用响应头设置资源的打开方式： 
	* content-disposition: attachment;filename=xxx
* 中文文件名
	* 解决思路：
		1. 根据客户端使用的浏览器版本信息
		2. 根据不同的版本信息，设计filename的编码方式不同


## 会话技术
	1. 会话：一次会话中包含多次请求和响应
		* 一次会话： 浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
	2. 功能： 在一次会话的范围内共享数据
	3. 方式：
		1. 客户端会话技术 Cookie
		2. 服务器端会话技术 Session


## Cookie
	1. 概念：客户端会话技术，将数据保存到客户端
	2. 快速入门：
		* 使用步骤：
			1. 创建Cookie对象，绑定数据
				* new Cookie(String name, String value)
			2. 发送Cookie对象
				* response.addCookie(Cookie cookie)
			3. 获取Cookie，拿到数据
				* Cookie[] request.getCookies
	
	3. 实现原理
		* 基于响应头set-cooke和请求头cookie实现
		
	4. cookie的细节
		1. 一次可以发送多个cookie吗
			* 可以创建多个cookie对象  多次add
		2. cookie在浏览器中保存多长时间
			1. 默认情况下，当浏览器关闭后，cookie数据会被销毁（cookie存在缓存中）
			2. 持久化存储：
				* setMaxAge(int seconds)
					1. 正数：持久化将cookie数据写到硬盘的文件中。持久化存储。cookie的存活时间
					2. 负数：默认值
					3. 零： 删除cookie信息 
		3. cookie能不能存中文
			* 在tomcat8之前 cookie不能直接存储中文
			* 在tomcat8之后 可以 特殊字符还是不支持，如空格 建议使用URL编解码
		4. cookie共享问题？
			1. 假设在一个tomcat服务器中，部署了多个web项目（不同虚拟目录），默认情况下cookie不能共享
				* setPath(String path): 设置cookie的获取范围，默认情况下设置当前的虚拟目录 path设为"/" 则可以共享
			
			2. 不同的tomcat服务器间cookie共享问题？
				* setDomain(String path):如果设置一级域名相同，那么可以共享
					* setDomain(".baidu.com") 那么tieba.baidu.com和news.baidu.com可以共享
		5. cookie的特点
			1. cookie存储数据在客户端浏览器
			2. 浏览器对于单个cookie的大小有限制（4kb） 以及 对同一个域名下的总cookie数量也有限制（20个）
			
			* 作用：
				1. 用户存储少量的不太敏感的数据
				2. 在不登录的情况下，完成服务器对客户端的身份识别

## JSP：入门学习
	1. 概念：
		* Java Server Pages: java服务器端页面
			* 可以理解为：一个特殊的页面，其中既可以制定定义html标签，又可以定义java代码
			* 用于简化书写
	
	2. 原理
		* JSP本质上就是一个Servlet
	
	3. JSP的脚本：定义java代码的方式
		1. <%  代码 %> ：定义的java代码，在service方法中。service方法中可以定义i什么，该脚本就可以定义什么
		2. <%!     %> ：定义的java代码，在jsp转换后的java类的成员位置。 
		3. <%=     %> ：定义的java代码会输出到页面上。输出语句中可以定义什么，该脚本就可以定义什么
	
	4. 内置对象
		* 在jsp页面中不需要获取和创建，可以直接使用的对象
		* jsp一共有9个内置对象。
			* request
			* response
			* out： 可以将数据输出到页面上和response.getWriter()类似
				* response输出永远在out之前


## Session：
	1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据爆粗你在服务器端的对象中。HttpSession对象
	2. 快速入门：
		1. 获取HttpSession对象
			request.getSession();
		2. 使用HttpSession对象
			HttpSession对象：
				Object getAttribute(String name)
				void setAttribute(String name, Object value)
				void removeAttribute(String name)
		3. 原理
			* Session的实现是依赖于Cookie的
				* 在没有cookie时才会创建Session
	
		4. 细节：
			1. 当客户端关闭后 重开，两次获取的session是同一个吗
				* 默认情况下不是
				* 如果需要相同。则可以创建cookie，键为JSESSIONID，设置最大存活时间，让cookie持久化保存。
					Cookie c = new Cookie("JSESSIONID",session.getID());
	                c.setMaxAge(60*60);
			2. 客户端你不关闭，服务器关闭后，两次获取的session是同一个吗
				* 不是同一个 但是要确保数据不丢失 tomcat做好了 不用担心
					* session的钝化
						* 在服务器正常关闭之前，将session对象序列哈到硬盘上
					* session的活化
						* 在服务器启动后，将session文件转化为内存中的session对象即可
			3. session什么时候被销毁？
				1. 服务器关闭
				2. session对象调用invalidate()
				3. session默认失效时间 30分钟
	
		5. session的特点
			1. session 用于存储一次会话的多次请求的数据，存在服务器端
			2. session可以存储任意类型，任意大小的数据
			
			* session和cookie的区别：
				1. session存储数据在服务器端，cookie在客户端
				2. session没有数据大小限制，cookie有
				3. session数据安全，cookie相对不安全


## JSP
	1. 指令
		* 作用：用户配置JSP页面，导入资源文件
		* 格式： <%@ 指令名称 属性名1=属性值1 属性名2=属性值2 %>
		* 分类：
			1. page   ： 配置jsp页面的
				* contentType: 等同于 response.setContentType()
					1. 设置响应体的Mime类型以及字符集
					2. 设置jsp当前页面的编码（只能是高级的IDE才能生效）
				* buffer: 缓冲区大小
				* import：导包
				* errorPage：错误页面，当前页面发生异常后 会跳转到指定的错误页面
				* isErrorPage：判断当前页面是否是错误页面
			2. include ： 页面包含的，导入页面的资源文件
				* <%@include file="top.jsp"%>
			3. taglib  ： 导入资源
	2. 注释（推荐使用jsp注释）
		1. HTML注释：只能注释html标签片段
			<!-- -->
		2. jsp注释：可以注释所有
			* <%-- --%>
	3. 内置对象
		* 在jsp页面中不需要创建，直接使用的对象
		* 一共有9个
				变量名                作用
			* pageContext			在当前页面共享数据，还可以获取其他八个内置对象
			* request				一次请求访问多个资源（转发）
			* session				一次回话的多个请求间
			* application			所有用户间共享数据
			* response				相应对象
			* page					当前页面（Servlet）的对象 this
			* out					输出对象，数据输出到页面上
			* config				Servlet的配置对象
			* exception				异常对象
				* (声名了一个isErrorPage后才有）


## MVC：开发模式
	1. jsp
	2. MVC：
		1. M：Model，模型 Java
		2. Bean
			* 完成具体的业务操作，如：查询数据库，封装对象
		2. V：View，视图  JSP
			* 展示数据
		3. C：Controller，控制器 Servlet
			* 获取用户的输入
			* 调用模型
			* 将数据交给视图进行展示
		
		* 优缺点
			1.优点：
				1. 耦合性低，方便维护，利于分工协作
				2. 重用性高。
			2. 缺点：
				1.	使得项目架构变得复杂，对开发人员要求高


## EL表达式
	1. 概念： Expression Language 表达式语言
	2. 作用： 替换和监护jsp页面中java代码的编写
	3. 语法：${表达式}
	4. 注意：
		* jsp默认支持EL表达式。如果要忽略el表达式 可以写\${表达式} 忽略当前这条el表达式
	5. 使用
		1. 运算
			* 运算符
				1. 算数运算符： + - * /(div) %(mod)
				2. 比较运算符
				3. 逻辑运算符 &&(and) ||(or) ！(not)
				4. 空运算符 empty
					* ${empty list} 用于判断字符串、集合、数组对象是否为null并且长度是否为0
					* ${not empty str} 用于判断字符串、集合、数组对象是否为不为null并且长度是否大于0
					
		2. 获取值
			1. el表达式只能从域对象中获取值
			2. 语法：
				1. ${域名称.键名称}：从指定域中获取指定键的值
					* 域名称
						1. pageScope --> pageContext
						2. requestScope --> request
						3. sessionScope --> session
						4. applicationScope --> application(ServletContext)
					* 举例 ${requestScope.name}
				2. ${键名} 依次从最小的域中查找，直到找到为止
				3. 获取对象、list结合、map集合的值
					1. 对象：${域名称.键名.属性名}
						* 本质上回去调用对象的getter方法
					
					2. List集合： ${域名称.键名[索引]}
					3. Map集合： ${域名称.键名.key名称}   ${域名称.键名["key名称"]}
	
		3. 隐式对象：
			* el表达式中有11个隐式对象
			* pageContext：
				* 获取jsp其他八个内置对象
					* ${pageContext.request.contextPath}:动态地获取虚拟目录


## JSTL
	1. 概念：JavaServer Pages Tag Library JSP标准标签库
		* 是由apache组织提供的开源的免费的jsp标签 <标签>
	
	2. 作用：用于简化和替换jsp页面上的java代码
	3. 使用步骤：
		1. 导入jstl相关jar包
		2. 引入标签库：taglib指令 <%@ taglib %>
		3. 使用标签
	
	4. 常用的JSTL标签
		1. if :相当于if语句
			* c:if标签
				1. 属性：
					* test 必须属性，接收boolean表达式，如果表达式为true，则显示if标签体内容，如果为false则不显示
					* 一般情况下，test属性值会结合el表达式一起使用
				2. 注意： c:if标签没有else情况，想要else情况，可以再定义一个c:if标签
		2. choose：相当于switch语句
		3. foreach：相当于for语句
			1. 完成重复的工作
				* 属性
					begin：开始值
					end 结束值
					var 临时变量
					step 步长
					varStatus 状态
						index:元素索引（从0开始）
						count:循环次数
			2. 遍历容器
				* 属性：
					* item: 容器对象
					* var: 临时变量
					* varStatus 状态
						index:元素索引（从0开始）
						count:循环次数	

## 三层架构：软件设计架构
	1. 界面层（表示层）：用户看到的界面，用户可以通过界面上的组件和服务器进行交互  cn.itcast.项目名.web
		* 接收用户参数，封装数据，调用业务逻辑层完成处理，转发jsp页面完成显示
		1. 控制器：Servlet
		2. 视图：jsp
	2. 业务逻辑层：处理业务逻辑的。 cn.itcast.项目名.service
		1. 组合dao层的简单方法，形成复杂的功能
	3. 数据访问层（dao层）：操作数据存储文件。  cn.itcast.项目名.dao
		1. 定义了对于数据库最基本的CRUD操作



## Filter 过滤器	
	1. 概念
		* web中的过滤器：当访问服务器的资源时，过滤器可将请求拦截下来，完成一些特殊的功能。
		* 过滤器的作用：
			* 一般用于完成通过的操作。如：登录验证、统一编码处理、敏感字符过滤...
			
	2. 快速入门：
		1. 步骤：
			1. 定义一个类，实现接口Filter
			2. 复写方法
			3. 配置拦截路径
				1. web.xml
				2. 注解配置   @WebFilter("拦截路径")
			* 放行 filterChain.doFilter(servletRequest, servletResponse);
	3. 过滤器细节
		1. web.xml配置
		2. 过滤器执行流程
		3. 过滤器生命周期方法
		4. 过滤器配置详解
			1. 拦截路径配置：
				1. 具体资源路径： /index.jsp  只有访问index.jsp资源时，过滤器才会被执行
				2. 目录拦截：   /usr/*   访问/user下的所有资源时，过滤器都会被执行
				3. 后缀名拦截：  *.jsp  访问所有后缀名为.jsp资源时，过滤器都会被执行
				4. 拦截所有资源： /*  访问所有资源时，过滤器都会被执行
			2. 拦截方式配置： 资源被访问的方式
				1. 注解配置
					* 设置dispatcherTypes属性  可以配置多个属性
						1. REQUEST: 默认值。浏览器直接请求资源  @WebFilter(value = "/*", dispatcherTypes = DispatcherType.REQUEST)
						2. FORWARD: 转发访问资源
						3. INCLUDE: 包含访问资源
						4. ERROR: 错误跳转资源
						5. ASYNC: 异步访问资源
				2. web.xml配置
		5. 过滤器链（配置多个过滤器）
			* 执行顺序：如果有两个过滤器：过滤器1  过滤器2  
				1. 过滤器1
				2. 过滤器2
				3. 资源执行
				4. 过滤器2
				5. 过滤器1
			* 过滤器先后顺序问题：
				1. 注解配置：按照类名的字符串比较规则比较，值晓得先执行
					* 如Afilter 和Bfilter，Afilter先执行
				2. web.xml配置：谁定义在上边，谁先执行


## Listener 监听器：
	* 概念： web的三大组件之一
		* 事件监听机制


			* 事件 ： 一件事情
			* 事件源 ：事件发生的地方
			* 监听器 ： 一个对象
			* 注册监听 ： 将事件、事件源、监听器绑定在一起。当事件源上发生某个时间后，执行监听器代码
	
	* ServletContextListener：监听ServletContext对象的创建和销毁
		* 方法
			* void contextDestroyed(ServletContextEvent sce):ServletContext对象被销毁之前会调用该方法
			* void contextInitialized(ServletContextEvent sce):ServletContext对象创建后会调用该方法
		* 步骤：
			1. 定义一个类，实现ServletcontextListener接口
			2. 复写方法
			3. 配置
				1. web.xml
					 <listener>
				        <listener-class>cn.infocore.web.listener.ContextLoaderListener</listener-class>
				     </listener>
				2. 注解
					@WebListener



## JQuery 基础：
	1. 概念：一个JavaScript的框架。可以简化JS开发
	
		* JavaScript框架：本质上就是一些js文件，封装了js的原生代码而已
		
	2. 快速入门
		1. 基本步骤：
			1. 下载JQuery
				* 一般企业使用1.x 2.0很少用 3.x只支持最新的浏览器
				* jquery-xxx.js 开发版本。给程序员看的，有良好的缩进和注释
				* jquery-xxx.min.js 生产版本。程序中使用，没有缩进。体积小一些，程序加载更快。
			2. 导入JQuery的js文件 导入min.js
			3. 使用
			
	3. JQuery对象和JS对象区别与转换
		1. JQuery对象在操作时，更加方便
		2. JQuery对象和js对象方法不通用
		3. 两者相互转换
			jq-->js  ： jq对象[索引]  或者 jq对象.get()
			js-->jq  ： ${js对象}
	
	4. 选择器：筛选具有相似特征的元素（标签）
		1. 分类
			1. 基本选择器
				1. 标签选择器（元素选择器）
					* 语法： $("html标签名") 获得所有匹配标签名称的元素
				2. id选择器
					* $(#id的属性值) 获得与制定id属性值匹配的元素
				3. 类选择器
					* $(".class的属性值") 获得与制定的class属性值匹配的元素
			2. 层级选择器
			3. 属性选择器
			4. 过滤选择器
	5. DOM操作（文档操作）
	6. 案例

