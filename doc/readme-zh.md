# EasyFilter 使用手册

---------------


EasyFilter是一个Jave Web请求内容过滤替换组件，能够对用户请求中的以下信息进行过滤替换：

1. 特殊字符替换（如：`&lt;`，`&gt;`特殊标记，脚本等）
2. 非法关键字替换（如：网络系统中国情不允许的特殊关键词）
3. SQL防注入过滤（如：`%`,`*`,`or`,`delete`,`and`等等SQL特殊关键字）

支持使用配置文件自定义过滤配置。


[官方主页](http://www.easyproject.cn/easyfilter/zh-cn/index.jsp '官方主页')

##  使用步骤

1. 加入jar包：`EasyFilter-X.X.X.jar`

2. 在`web.xml`配置过滤器

 如果有其他过滤器，请检查过滤器执行顺序（`filter-mapping`定义顺序）保证EasyFilter优先过滤数据。例如，有struts2的项目，`EasyFilter`应该早于Struts2的`StrutsPrepareAndExecuteFilter`执行。

 ```XML
   <filter>
       <filter-name>contentFilter</filter-name>
       <filter-class>cn.easyproject.easyfilter.filter.EasyFilter</filter-class>
       <!-- request 请求字符集 -->
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

3. 在类路径根目录下创建`easyFilter.properties`配置文件，配置参数如下

 ```properties
  # EasyFilter
  # @author easyproject.cn 
  # @author inthinkcolor@gmail.com
  
  # 如果没有配置easyFilter.properties，EasyFilter会使用内部默认配置文件easyFilter-failsafe.properties
  
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
  # 格式: 参数名称正则表达式#参数名称正则表达式2#...
  # 支持正则表达式
  excludeKeywordFilterParameters=password\#.*.password\#confirmPwd
  
  # 排除关键字过滤的uri列表，uri为request.getRequestURI()获得的值
  # 格式: URI正则表达式#URI正则表达式2#...
  # Supports regex expressions
  # eg. excludeKeywordFilterURI=/test\.jsp#add\.action
  excludeKeywordFilterURI=
  
  ######################### SQL注入关键字过滤 #########################
  
  # 是否开启SQL注入关键字过滤
  # on: 开启
  # off: 关闭
  # 如果不是on，则按off处理
  sqlInjectFilter=off
  
  # SQL注入关键字列表，这些字符将被替换为空字符
  # 格式: 关键字1#关键字2#...
  sqlInjectFilterList=and#exec#insert#select#delete# #update#count#*#%#chr#mid#master#truncate#char#declare#;#or#-#+#,
  
  # 包含SQL注入关键字过滤的请求参数名称列表
  # 格式: 参数名称正则表达式#参数名称正则表达式2#...
  # 支持正则表达式
  # eg. includeSqlInjectFilterParameters=sysUser.name
  includeSqlInjectFilterParameters=
```



## 结束

[留言评论](http://www.easyproject.cn/easyfilter/zh-cn/index.jsp#about '留言评论')

如果您有更好意见，建议或想法，请联系我。


联系、反馈、定制、培训 Email：<inthinkcolor@gmail.com>

<p>
<strong>支付宝钱包扫一扫捐助：</strong>
</p>
<p>

<img alt="支付宝钱包扫一扫捐助" src="http://www.easyproject.cn/images/s.png"  title="支付宝钱包扫一扫捐助"  height="256" width="256"></img>


[http://www.easyproject.cn](http://www.easyproject.cn "EasyProject Home")