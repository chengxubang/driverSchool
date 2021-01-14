基于SSH框架实现的驾校管理系统
=

程序有问题联系[程序帮](http://suo.nz/530ijn)：QQ1022287044

项目介绍
----
>本系统使用Struts2+Spring+Hibernate架构，数据库使用MySQL,连接池使用c3p0。广泛用于驾校管理，包含学员管理、车辆管理、教练员工管理、用车管理、考试管理、成绩缴费管理等功能，除了管理员能控制修改这些信息以外，教练也可对其下学员的约车信息和考试管理信息进行增删改查。


项目适用人群
----
正在做毕设的学生，或者需要项目实战练习的Java学习者

开发环境
-----
1. jdk 8
2. intellij idea
3. tomcat 8.5.40
4. mysql 5.7

所用技术
-----
1. Struts2+Spring+Hibernate
2. js+ajax
3. jsp

 项目架构
----
![](/src/image/项目架构.png)
 
项目截图
----

- 登录

![](/src/image/登录.png)

- 学员列表

![](/src/image/学员列表.png)

- 教练列表

![](/src/image/教练列表.png)

- 新增缴费信息

![](/src/image/新增缴费信息.png)

- 教练后台-考试管理

![](/src/image/教练后台-考试管理.png)

- 教练后台-约车管理

![](/src/image/教练后台-约车管理.png)







数据库配置
----
```
<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
    <property name="hibernateProperties">
        <props>
            <prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
            <prop key="hibernate.show_sql">false</prop>
            <prop key="hibernate.format_sql">false</prop>
            <prop key="hibernate.connection.provider_class">com.jolbox.bonecp.provider.BoneCPConnectionProvider</prop>
            <prop key="hibernate.connection.driver_class">com.mysql.jdbc.Driver</prop>
            <prop key="hibernate.connection.url"> <![CDATA[jdbc:mysql://localhost:3306/driverschool?useUnicode=true&characterEncoding=utf8]]></prop>
            <prop key="hibernate.connection.username">root</prop>
            <prop key="hibernate.connection.password">root123</prop>
            <prop key="bonecp.partitionCount">1</prop>
            <prop key="bonecp.maxConnectionsPerPartition">10</prop>
            <prop key="bonecp.minConnectionsPerPartition">5</prop>
            <prop key="bonecp.acquireIncrement">1</prop><!-- #每次新增连接的数量 -->
            <prop key="bonecp.connectionTimeout">3000</prop><!-- #连接超时时间阀值，获取连接时，超出阀值时间，则获取失败，毫秒为单位 -->
            <prop key="bonecp.poolAvailabilityThreshold">20</prop><!-- #连接池阀值，当 可用连接/最大连接 < 连接阀值 时，创建新的连接 -->
            <prop key="bonecp.idleConnectionTestPeriod">30</prop><!-- 测试连接有效性的间隔时间，单位分钟 -->
            <prop key="bonecp.idleMaxAge">240</prop><!-- 连接的空闲存活时间，当连接空闲时间大于该阀值时，清除该连接 -->
            <prop key="bonecp.statementsCacheSize">5</prop><!-- 语句缓存个数 -->
            <prop key="hibernate.query.factory_class">org.hibernate.hql.classic.ClassicQueryTranslatorFactory</prop>
            <prop key="hibernate.connection.release_mode">auto</prop>
            <prop key="hibernate.autoReconnect">true</prop>
            <prop key="hibernate.connection.autocommit">true</prop>
            <prop key="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</prop>
            <prop key="hibernate.cache.use_query_cache">true</prop>
            <prop key="hibernate.cache.use_second_level_cache">true</prop>
        </props>
    </property>
    <property name="mappingResources">
        <list>
            <value>com/driverSchool/entity/Bookcar.hbm.xml</value>
            <value>com/driverSchool/entity/Car.hbm.xml</value>
            <value>com/driverSchool/entity/Pay.hbm.xml</value>
            <value>com/driverSchool/entity/Student.hbm.xml</value>
            <value>com/driverSchool/entity/Test.hbm.xml</value>
            <value>com/driverSchool/entity/User.hbm.xml</value>
        </list>
    </property>
</bean>
```


struts.xml 配置
-----
```diff
<struts>
	<constant name="struts.i18n.encoding" value="UTF-8" />
	<constant name="struts.locale" value="zh_CN" />
	<constant name="struts.objectFactory" value="spring" />
	<package name="default" extends="struts-default">

		<interceptors>
			<!-- 安全信息过滤，用户session为空则强制退出 -->
			<interceptor name="sessionCheck" class="com.driverSchool.util.SessionCheck">
				<!-- 不需要拦截的方法 -->
				<param name="excludeMethods">
					login
				</param>
			</interceptor>
			<!-- 拦截器栈，包含SESSION拦截和默认拦截器 -->
			<interceptor-stack name="authInterceptor1">
				<interceptor-ref name="sessionCheck" />
				<interceptor-ref name="defaultStack" />
			</interceptor-stack>
		</interceptors>
		<default-interceptor-ref name="authInterceptor1"></default-interceptor-ref>

		<!-- 注意global-results必须在global-exception-mappings前面 -->
		<global-results>
			<result name="reLogin">/login.jsp</result>
			<result name="to_index">/index.jsp</result>
		</global-results>
		<global-exception-mappings> <!-- 此处定义的result对应于上面的global-results内 捕获所有异常  -->
			<exception-mapping result="error" exception="java.lang.Exception"> </exception-mapping> 
			<exception-mapping result="errorSql" exception="java.sql.SQLException"> </exception-mapping> 
		</global-exception-mappings>
    	
		<action name="userAction_*" class="userAction" method="{1}">
			<result name="to_info">/info.jsp</result>
			<result name="to_user_list">/user_list.jsp</result>
			<result name="to_addOrEditUser">/user_addAndEdit.jsp</result>
			<result name="to_user_listAction" type="redirectAction">userAction_findUserAll</result>
		</action>
		
		<action name="bookcarAction_*" class="bookcarAction" method="{1}">
			<result name="to_bookcar_list">/bookcar_list.jsp</result>
			<result name="to_addOrEditBookcar">/bookcar_addAndEdit.jsp</result>
			<result name="to_bookcar_listAction" type="redirectAction">bookcarAction_findBookcarAll</result>
		</action>
		
		<action name="carAction_*" class="carAction" method="{1}">
			<result name="to_car_list">/car_list.jsp</result>
			<result name="to_addOrEditCar">/car_addAndEdit.jsp</result>
			<result name="to_car_listAction" type="redirectAction">carAction_findCarAll</result>
		</action>
		
		<action name="payAction_*" class="payAction" method="{1}">
			<result name="to_pay_list">/pay_list.jsp</result>
			<result name="to_addOrEditPay">/pay_addAndEdit.jsp</result>
			<result name="to_pay_listAction" type="redirectAction">payAction_findPayAll</result>
		</action>
		
		<action name="studentAction_*" class="studentAction" method="{1}">
			<result name="to_student_list">/student_list.jsp</result>
			<result name="to_addOrEditStudent">/student_addAndEdit.jsp</result>
			<result name="to_student_listAction" type="redirectAction">studentAction_findStudentAll</result>
		</action>
		
		<action name="testAction_*" class="testAction" method="{1}">
			<result name="to_test_list">/test_list.jsp</result>
			<result name="to_addOrEditTest">/test_addAndEdit.jsp</result>
			<result name="to_test_listAction" type="redirectAction">testAction_findTestAll</result>
		</action>
	</package>
</struts>   
```

2. applicationContext.xml
```diff
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	<import resource="datebase.xml" />

	<bean id="userDao" class="com.driverSchool.dao.UserDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="userService" class="com.driverSchool.service.impl.UserServiceImpl">
		<property name="dao" ref="userDao"></property>
	</bean>
	<bean id="userAction" class="com.driverSchool.action.UserAction">
		<property name="biz" ref="userService"></property>
	</bean>
	
	<bean id="bookcarDao" class="com.driverSchool.dao.BookcarDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="bookcarService" class="com.driverSchool.service.impl.BookcarServiceImpl">
		<property name="dao" ref="bookcarDao"></property>
	</bean>
	<bean id="bookcarAction" class="com.driverSchool.action.BookcarAction">
		<property name="biz" ref="bookcarService"></property>
		<property name="sbiz" ref="studentService"></property>
		<property name="cbiz" ref="carService"></property>
	</bean>
	
	<bean id="carDao" class="com.driverSchool.dao.CarDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="carService" class="com.driverSchool.service.impl.CarServiceImpl">
		<property name="dao" ref="carDao"></property>
	</bean>
	<bean id="carAction" class="com.driverSchool.action.CarAction">
		<property name="biz" ref="carService"></property>
	</bean>
	
	<bean id="payDao" class="com.driverSchool.dao.PayDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="payService" class="com.driverSchool.service.impl.PayServiceImpl">
		<property name="dao" ref="payDao"></property>
	</bean>
	<bean id="payAction" class="com.driverSchool.action.PayAction">
		<property name="biz" ref="payService"></property>
		<property name="sbiz" ref="studentService"></property>
	</bean>
	
	<bean id="studentDao" class="com.driverSchool.dao.StudentDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="studentService" class="com.driverSchool.service.impl.StudentServiceImpl">
		<property name="dao" ref="studentDao"></property>
	</bean>
	<bean id="studentAction" class="com.driverSchool.action.StudentAction">
		<property name="biz" ref="studentService"></property>
	</bean>
	
	<bean id="testDao" class="com.driverSchool.dao.TestDao">
		<property name="ht" ref="hibernatetemplate"></property>
	</bean>
	<bean id="testService" class="com.driverSchool.service.impl.TestServiceImpl">
		<property name="dao" ref="testDao"></property>
	</bean>
	<bean id="testAction" class="com.driverSchool.action.TestAction">
		<property name="biz" ref="testService"></property>
		<property name="sbiz" ref="studentService"></property>
	</bean>
</beans>
```

登录
----
```
//controller
public String login() {
    User user = biz.findUserByUsernameAndPwd(username, pwd);
    if (user == null) {
        ActionContext.getContext().put("msg", "该用户不存在，请重新登录！");
        return "reLogin";
    } else {
        ActionContext.getContext().getSession().put("user", user);
        ActionContext.getContext().getSession().put("uname", username);
        ActionContext.getContext().getSession().put("uid", user.getId());
        ActionContext.getContext().getSession().put("role", user.getRole());
        ActionContext.getContext().put("msg", "");
        return "to_index";
    }
}
//jsp
<div class="container">
    <div class="line bouncein">
        <div class="xs6 xm4 xs3-move xm4-move">
            <div style="height:150px;"></div>
            <div class="media media-y margin-big-bottom"></div>
            <form action="<%=basePath%>userAction_login.action" method="post">
                <div class="panel loginbox">
                    <div class="text-center margin-big padding-big-top">
                        <h1>驾校信息管理平台</h1>
                    </div>
                    <div class="panel-body" style="padding:30px; padding-bottom:10px; padding-top:10px;">
                        <div class="form-group">
                            <div class="field field-icon-right">
                                <input type="text" class="input input-big" name="username" placeholder="登录账号" data-validate="required:请填写账号" />
                                <span class="icon icon-user margin-small"></span>
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="field field-icon-right">
                                <input type="password" class="input input-big" name="pwd" placeholder="登录密码" data-validate="required:请填写密码" />
                                <span class="icon icon-key margin-small"></span>
                            </div>
                        </div>
                    </div>
                    <div style="padding:30px;">
                        <input type="submit" class="button button-block bg-main text-big input-big"	value="登录">
                    </div>
                </div>
            </form>
        </div>
    </div>
</div>

```


项目后续
----
其他ssm，springboot版本后续迭代更新，持续关注
