# EasyFilter Manual

---------------

EasyFilter is a Jave Web content filtering request replacement component, the user can request the following information to filter replacement:

1. Replace special characters (eg: `& lt;`, `& gt;` special mark, scripts, etc.)
2. Illegal keyword substitution (eg: network system does not allow the special keyword)
3. SQL-injection filter (eg: `%`, `*`, `or`,` delete`, `and` SQL special keyword)

Supports use properties or xml file custom filtering configuration profiles.


[Official Homepage](http://www.easyproject.cn/easyfilter/en/index.jsp 'Official Homepage')


##  Use step

1. Add jar：`EasyFilter-X.X.X.jar`
 - Maven:
 ```XML
 <dependency>
 	<groupId>cn.easyproject</groupId>
 	<artifactId>easyfilter</artifactId>
 	<version>2.0.2-RELEASE</version>
 </dependency>
 ```

2. Configure the filter in `web.xml`

 If there are other filter, check the filter execution sequence (`filter-mapping` defined order) ensure EasyFilter priority filter data. For example, there struts2 project, `EasyFilter`  must be executed before the Struts2` StrutsPrepareAndExecuteFilter`.
 
 ```XML
   <filter>
       <filter-name>contentFilter</filter-name>
       <filter-class>cn.easyproject.easyfilter.filter.EasyFilter</filter-class>
       <!-- request characterEncoding, default is utf-8 -->
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

3. Custom Configuration
    Create a `easyFilter.properties` or` easyFilter.xml` in the root directory of the classpath  (If not, EasyFilter uses internal default configuration file `easyFilter-failsafe.properties`). Configurations and parameters are as follows:

 - **Configuration way one: easyFilter.xml**
 ```XML
<?xml version="1.0" encoding="UTF-8"?>
<easyFilter xmlns="http://www.easyproject.cn/schema/easyFilter"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.easyproject.cn/schema/easyFilter http://www.easyproject.cn/schema/easyFilter/easyfilter-2.0.xsd">

	<!--######################### Request keyword filter ######################### -->
	<keywordFilter>
		<!-- Whether to filter keyword from request parameters 
			- on: Filter keyword parameter on 
			- off: Filter keyword parameter off
		-->
		<filter>on</filter>
		<!-- 
			if find keyword, use defaultReplace to replace 
		-->
		<defaultReplace></defaultReplace>
		<!-- 
			- keyword and replace list 
			- Format: keyword replace 
			- Notice: In XML document, special characters must use character entities in place
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
			- exclude keyword filter parameterName list 
			- Format: regexParameterName##regexParameterName2##...
			- Supports regex expressions 
		-->
		<excludeParameters>
			<keyword>password</keyword>
			<keyword>.*.password</keyword>
			<keyword>confirmPwd</keyword>
		</excludeParameters>
		<!-- 
			- exculde keyword filter uri list, request.getRequestURI() 
			- Format: regexURI##regexURI2##... 
			- Supports regex expressions 
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
			- Whether to filter SQL inject keyword parameters 
			- on: Filter SQL inject keyword parameter on 
			- off: Filter SQL inject keyword parameter off 
		-->
		<filter>off</filter>
		<!-- 
			- SQL inject keyword, when sqlInjectFilter is true, these words will 
			replace to empty by request 
			-  Format: word##word2##... 
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
			- inlude sql inject filter parameterName list 
			- Format: regexParameterName##regexParameterName##... 
			- Supports regex expressions 
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

 - **Configuration way two: easyFilter.properties**
 ```properties
    # EasyFilter
    # @author easyproject.cn 
    # @author inthinkcolor@gmail.com
    
    #  The easyFilter-failsafe.properties is a default configuration for EasyFilter, if an easyFilter.properties is not configured.
    
    ######################### Request keyword filter #########################
    
    # Whether to filter keyword from request parameters
    # on: Filter keyword parameter on
    # off: Filter keyword parameter off
    # if not on or off, use off
    keywordFilter=on
    
    # if find keyword, use defaultReplace to replace
    defaultReplace=
    
    # keyword and replace list
    # Format: keyword,replace##keyword2,replace2##...
    keywordAndReplaceList= <,&lt;  \#\#  >,&gt;  
    
    # User defined keyword filter item
    # If item's keyword same to keywordAndReplaceList config, these priority
    # Format: keyword=replace
    # eg. bitch=love
    # eg. \u6BD2\u54C1=*
    fuck=f***
    
    # exclude keyword filter parameterName list
    # Format: regexParameterName##regexParameterName##...
    # Supports regex expressions
    excludeKeywordFilterParameters=password\#\#.*.password\#\#confirmPwd
    
    # exculde keyword filter uri list, request.getRequestURI()
    # Format: regexURI##regexURI2##...
    # Supports regex expressions
    # eg. excludeKeywordFilterURI=/test\.jsp##add\.action
    excludeKeywordFilterURI=
    
    ######################### SQL inject keyword filter #########################
    
    # Whether to filter SQL inject keyword parameters
    # on: Filter SQL inject keyword parameter on
    # off: Filter SQL inject keyword parameter off
    # if not on or off, use off
    sqlInjectFilter=off
    
    # SQL inject keyword, when sqlInjectFilter is true, these words will replace to empty by request
    # Format: word##word2##...
    sqlInjectFilterList=and#exec#insert#select#delete# #update#count#*#%#chr#mid#master#truncate#char#declare#;#or#-#+#,
    
    # inlude sql inject filter parameterName list
    # Format: regexParameterName##regexParameterName##...
    # Supports regex expressions
    # eg. includeSqlInjectFilterParameters=sysUser.name
    includeSqlInjectFilterParameters=
```




## End

[Comments](http://www.easyproject.cn/easycommons/en/index.jsp#about 'Comments')

If you have more comments, suggestions or ideas, please contact me.

Email:<inthinkcolor@gmail.com>

[http://www.easyproject.cn](http://www.easyproject.cn "EasyProject Home")


We believe that the contribution of each bit by bit, will be driven to produce more and better free and open source products a big step.

**Thank you donation to support the server running and encourage more community members.**

[![PayPal](http://www.easyproject.cn/images/paypaldonation5.jpg)](https://www.paypal.me/easyproject/10 "Make payments with PayPal - it's fast, free and secure!")
