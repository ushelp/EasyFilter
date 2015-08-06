# EasyFilter Manual

---------------

EasyFilter is a Jave Web content filtering request replacement component, the user can request the following information to filter replacement:

1. Replace special characters (eg: `& lt;`, `& gt;` special mark, scripts, etc.)
2. Illegal keyword substitution (eg: network system does not allow the special keyword)
3. SQL-injection filter (eg: `%`, `*`, `or`,` delete`, `and` SQL special keyword)

Supports custom filtering configuration profiles.


[Official Homepage](http://www.easyproject.cn/easyfilter/en/index.jsp 'Official Homepage')


##  Use step

1. Add jar：`EasyFilter-X.X.X.jar`

2. Configure the filter in `web.xml`

 If there are other filter, check the filter execution sequence (`filter-mapping` defined order) ensure EasyFilter priority filter data. For example, there struts2 project, `EasyFilter`  must be executed before the Struts2` StrutsPrepareAndExecuteFilter`.
 
 ```XML
   <filter>
       <filter-name>contentFilter</filter-name>
       <filter-class>cn.easyproject.easyfilter.filter.EasyFilter</filter-class>
       <!-- request characterEncoding -->
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

3. Create a `easyFilter.properties` profile classpath root directory, configuration parameters are as follows

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
   # Format: keyword,replace##keyword2,replace2#...
   keywordAndReplaceList= <,&lt;  \#\#  >,&gt;  
   
   # User defined keyword filter item
   # If item's keyword same to keywordAndReplaceList config, these priority
   # Format: keyword=replace
   # eg. bitch=love
   # eg. \u6BD2\u54C1=*
   fuck=f***
   
   # exclude keyword filter parameterName list
   # Format: regexParameterName#regexParameterName#...
   # Supports regex expressions
   excludeKeywordFilterParameters=password\#.*.password\#confirmPwd
   
   # exculde keyword filter uri list, request.getRequestURI()
   # Format: regexURI#regexURI2#...
   # Supports regex expressions
   # eg. excludeKeywordFilterURI=/test\.jsp#add\.action
   excludeKeywordFilterURI=
   
   ######################### SQL inject keyword filter #########################
   
   # Whether to filter SQL inject keyword parameters
   # on: Filter SQL inject keyword parameter on
   # off: Filter SQL inject keyword parameter off
   # if not on or off, use off
   sqlInjectFilter=off
   
   # SQL inject keyword, when sqlInjectFilter is true, these words will replace to empty by request
   # Format: word#word2#...
   sqlInjectFilterList=and#exec#insert#select#delete# #update#count#*#%#chr#mid#master#truncate#char#declare#;#or#-#+#,
   
   # inlude sql inject filter parameterName list
   # Format: regexParameterName#regexParameterName#...
   # Supports regex expressions
   # eg. includeSqlInjectFilterParameters=sysUser.name
   includeSqlInjectFilterParameters=
```




## End

[Comments](http://www.easyproject.cn/easycommons/en/index.jsp#about 'Comments')

If you have more comments, suggestions or ideas, please contact me.

Email:<inthinkcolor@gmail.com>

[http://www.easyproject.cn](http://www.easyproject.cn "EasyProject Home")
