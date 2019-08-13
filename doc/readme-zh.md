# EasyFilter 使用手册

---------------


EasyFilter是一个Jave Web请求内容过滤替换组件，能够对用户请求中的以下信息进行过滤替换：

1. 特殊字符替换（如：`&lt;`，`&gt;`特殊标记，脚本等）
2. 非法关键字替换（如：网络系统中国情不允许的特殊关键词）
3. SQL防注入过滤（如：`%`,`*`,`or`,`delete`,`and`等等SQL特殊关键字）

支持使用properties或xml配置文件自定义过滤配置。


[官方主页](http://www.easyproject.cn/easyfilter/zh-cn/index.jsp '官方主页')

##  使用步骤

1. 加入jar包：`EasyFilter-X.X.X.jar`
 - Maven:
 ```XML
 <dependency>
 	<groupId>cn.easyproject</groupId>
 	<artifactId>easyfilter</artifactId>
 	<version>2.0.2-RELEASE</version>
 </dependency>
 ```

2. 在`web.xml`配置过滤器

 如果有其他过滤器，请检查过滤器执行顺序（`filter-mapping`定义顺序）保证EasyFilter优先过滤数据。例如，有struts2的项目，`EasyFilter`应该早于Struts2的`StrutsPrepareAndExecuteFilter`执行。
```XML
  <filter>
      <filter-name>contentFilter</filter-name>
      <filter-class>cn.easyproject.easyfilter.filter.EasyFilter</filter-class>
      <!-- request 请求字符集, 默认为 utf-8  -->
      <init-param>
      	<param-name>charset</param-name>
      	<param-value>utf-8</param-value>
      </init-param>
 </filter>
 
 <filter-mapping>
      <filter-name>contentFilter</filter-name>
      <url-pattern>/*</url-pattern>
 </filter-mapping>
```

3. 自定义配置
   在类路径根目录下创建`easyFilter.properties`或`easyFilter.xml`（如果没有配置，EasyFilter会使用内部默认配置文件`easyFilter-failsafe.properties`）。配置方式和参数如下：

 - **配置方式一: easyFilter.xml**
 ```XML
<?xml version="1.0" encoding="UTF-8"?>
<easyFilter xmlns="http://www.easyproject.cn/schema/easyFilter"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.easyproject.cn/schema/easyFilter http://www.easyproject.cn/schema/easyFilter/easyfilter-2.0.xsd">

	<!--######################### Request keyword filter ######################### -->
	<keywordFilter>
		<!-- 
			- 是否开启请求关键字过滤
			- on: 开启 
			- off: 关闭
		-->
		<filter>on</filter>
		<!-- 
			发现关键字后，如果没有为关键字指定替换字符，则使用该默认值替换
		-->
		<defaultReplace></defaultReplace>
		<!-- 
			- 关键字替换列表 
			- 格式: keyword replace 
			- 注意: 在XML文档中特殊字符必须使用字符实体表示
		-->
		<replaceList>
			<replace keyword="&lt;" replace="&amp;lt;"></replace>
			<replace keyword="&gt;" replace="&amp;gt;"></replace>
			<replace keyword="fuck" replace="f***"></replace>
			<!-- 
			<replace keyword="bitch" replace="love"></replace>
			<replace keyword="毒品" replace="XX"></replace> 
			-->
		</replaceList>
		<!-- 
			- 排除关键字过滤的请求参数名称列表 
			- 格式: 参数名称正则表达式##参数名称正则表达式2##...
			- 支持正则表达式 
		-->
		<excludeParameters>
			<keyword>password</keyword>
			<keyword>.*.password</keyword>
			<keyword>confirmPwd</keyword>
		</excludeParameters>
		<!-- 
			- 排除关键字过滤的uri列表，uri为request.getRequestURI()获得的值 
			- 格式: URI正则表达式##URI正则表达式2##... 
			- 支持正则表达式 
		-->
		<excludeURI>
		<!-- 	 
				<uri>/test\.jsp</uri> 
				<uri>add\.action</uri> 
		-->	
		</excludeURI>
	</keywordFilter>

	<!-- ######################### SQL inject keyword filter ######################### -->
	<sqlFilter>
		<!-- 
			- 是否开启SQL注入关键字过滤
			- on: 开启 
			- off: 关闭 
		-->
		<filter>off</filter>
		<!-- 
			- SQL注入关键字列表，这些字符将被替换为空字符 
			-  Format: 关键字1##关键字2##... 
		-->
		<injectFilterList>
			<value>
				and##exec##insert##select##delete##update##count##chr##mid##master##truncate##char##declare##or
			</value>
			<value>
				;##-##+##,##*##% 
			</value>
		</injectFilterList>
		<!-- 
			- 包含SQL注入关键字过滤的请求参数名称列表 
			- 格式: 参数名称正则表达式##参数名称正则表达式2##... 
			- 支持正则表达式 
			- eg. sysUser.name 
		-->
		<includeParameters>
			<!-- 
				<value>sysUser.name</value> 
			-->
		</includeParameters>
	</sqlFilter>
</easyFilter>
```

 - **配置方式二：easyFilter.properties**
 ```properties
    # EasyFilter
    # @author easyproject.cn 
    # @author inthinkcolor@gmail.com
    
    ######################### 请求关键字过滤部分 #########################
    
    # 是否开启请求关键字过滤
    # on: 开启
    # off: 关闭
    # 如果不是on，则按off处理
    keywordFilter=on
    
    # 发现关键字后，如果没有为关键字指定替换字符，则使用该默认值替换
    defaultReplace=
    
    # 关键字替换列表
    # 格式: 关键字,替换值##关键字2,替换值2##...
    keywordAndReplaceList= <,&lt;  \#\#  >,&gt;  
    
    # 自定义关键字替换条目
    # 如果keywordAndReplaceList已经存在相同的关键字，自定义条目优先
    # 格式: 关键字=替换值
    # eg. bitch=love
    # eg. \u6BD2\u54C1=*
    fuck=f***
    
    # 排除关键字过滤的请求参数名称列表
    # 格式: 参数名称正则表达式##参数名称正则表达式2##...
    # 支持正则表达式
    excludeKeywordFilterParameters=password\#\#.*.password\\##confirmPwd
    
    # 排除关键字过滤的uri列表，uri为request.getRequestURI()获得的值
    # 格式: URI正则表达式##URI正则表达式2##...
    # Supports regex expressions
    # eg. excludeKeywordFilterURI=/test\.jsp##add\.action
    excludeKeywordFilterURI=
    
    ######################### SQL注入关键字过滤 #########################
    
    # 是否开启SQL注入关键字过滤
    # on: 开启
    # off: 关闭
    # 如果不是on，则按off处理
    sqlInjectFilter=off
    
    # SQL注入关键字列表，这些字符将被替换为空字符
    # 格式: 关键字1##关键字2##...
    sqlInjectFilterList=and##exec##insert##select##delete##update##count##*##%##chr##mid##master##truncate##char##declare##;##or##-##+##,
    
    # 包含SQL注入关键字过滤的请求参数名称列表
    # 格式: 参数名称正则表达式##参数名称正则表达式2##...
    # 支持正则表达式
    # eg. includeSqlInjectFilterParameters=sysUser.name
    includeSqlInjectFilterParameters=
```


## End

Email：<inthinkcolor@gmail.com>

[http://www.easyproject.cn](http://www.easyproject.cn "EasyProject Home")


**Donation/捐助:**

<a href="http://www.easyproject.cn/donation">
<img alt="
支付宝/微信/QQ/云闪付/PayPal 扫码支付" src="http://www.easyproject.cn/thanks/donation.png"  title="支付宝/微信/QQ/云闪付/PayPal 扫码支付"  height="320" width="320"></img></a>
<div>支付宝/微信/QQ/云闪付/PayPal</div>

<br/>

我们相信，每个人的点滴贡献，都将是推动产生更多、更好免费开源产品的一大步。

**感谢慷慨捐助，以支持服务器运行和鼓励更多社区成员。**

We believe that the contribution of each bit by bit, will be driven to produce more and better free and open source products a big step.

**Thank you donation to support the server running and encourage more community members.**

