### 目录  
 - [Spring](#Spring)  
 - [Hibernate](#Hibernate)  
 - [Struts2](#Struts2)  
 - [EL及JSTL](#ELJSTL)  
 - [AJAX](#AJAX)
 - [栅格与响应式](#BootStrap)
 - [websocket](#websocket)
## <span id="Spring">Spring 框架</span>  
 - [Spring](#Spring)  
    - [Spring配置](#1)
    - [bean实例化](#2)
    - [属性注入](#3)
    - [注解操作](#4)
    - [AOP](#5)
    - [jdbcTemplate](#6)
    - [监听器](#7)
    - [SSH整合](#8)
>   开源轻量级框架  
    核心：AOP、IOC  
    一站式框架  
    Spring5.0.3  

#### <span id="1">Spring配置:</span>
```  
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd"> <!-- bean definitions here -->
	<context:component-scan base-package="扫描的包名"></context:component-scan>
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
web.xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listen>  
	<listen-class>org.springframework.web.context.ContextLoaderListener</listen-class>
</listen>
```
#### <span id="2">bean实例化: </span>
- 无参构造√ :  
```  
<bean id="名称" class="全路径"></bean>   //不含特殊符号 name属性可以加特殊符号
//scope singletou 默认,单实例 prototype多实例
```
- 静态工厂 :  
```  
静态工厂类里  
public static Bean getBean() {  
	return new Bean();  
}  
<bean id="名称" class="全路径" factory-method="getBean"></bean>  
```
- 实例工厂：
```
实例工厂类里  
public Bean getBean() {  
	return new Bean();  
}  
<bean id="工厂类名" class="全路径"></bean>  
<bean id="名称" factory-bean="工厂类名" factory-method="getBean"></bean>  
```
#### <span id="3">属性注入 : </span> 
-  有参构造注入  
```
<bean id="名称" class="全路径">  
	<constructor-arg name="属性名" value="值"></constructor-arg>  
</bean>  
```
- set注入√  
```
<bean id="名称" class="全路径">  
	<property name="属性名" value="值"></property>  
</bean>  
```
- 注入对象属性  
```  
service类里加入set方法
<bean id="名称" class="DAO类全路径"></bean>  
<bean id="名称" class="service类全路径">  
	<property name="set的属性名" ref="名称"></proterty>  
```
- p名称空间注入  
```  
xmlns:p="http://www.springframework.org/schema/p"   
<bean id="名称" class="DAO全路径" p:属性名="属性值"></p>
```
- 数组/Map/List注入  
```  
<bean id="名称" class="全路径">  
	<property name="属性名">
	<list/map>
		<value>...</value>  
		//<entry key="" value=""></entry>  
	<list/map>  
	</property>  
</bean>
```
#### <span id="4">注解操作:</span>
- 注解创建对象
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
	<context:component-scan base-package=""></context:component-scan>
</beans>  
@Component(value="名称")  
类 
@Controller 表述层  @Service 业务逻辑层  @Respository  数据访问层  
```
- 注解注入属性
```
@Autowired  
private 类 对象;  
或  
@Resource(name="要引用的对象的Component里的value值");  
```
Bean用来创建对象 注解用来注入属性  
```
<bean id="名称" class="被作为属性的对象的类的全路径"></bean>  
@Resource(name="名称")  
private 类 对象名;  
```
#### <span id="5">AOP:</span>  
- xml配置
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
<bean id="名称" class="增强的类"></bean>  
<bean id="名称" class="被增强的类"></bean>  
<aop:config>  
	<aop:pointcut expression="execution(* 包.被增强方法(..))" id="名称"/>  
	<aop:aspect ref="增强的类名称">  
		<aop:类型(before/after-returning/around) method="增强方法名"/ pointcut-ref="被增强方法(切入点)名称">  
	</aop:aspect>  
</aop:config>  
</beans>
```
- 注解
```
<bean ...>
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
@Aspect
public class 增强类{
    @类型(value="execution(* 被增强类.切入点())")    
    public void 增强方法(){
        ...
    }
}
```
//环绕 proceedingJoinPoint.proceed();  
#### <span id="6">jdbcTemplate</span>
- 增删改
```
DriverManagerDataSource dataSource = new DriverManagerDataSource();
dataSource.setDriverClassName("com.mysql.jdbc.Driver");
dataSource.setUrl("jdbc:mysql:///库");
dataSource.setUsername("");
dataSource.setPassword("");
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSouce);
String sql = "xxx ? xxx"
int rows = jdbcTemplate.update(sql,"value");
```
- 查询
    - 返回一个值
    ```
    int num = jdbcTemplate.queryForObject(sql,Integer.class);
    
    ```
    - 返回一个对象
    ```
    Entity Entity_name = jdbcTemplate.queryForObject(sql,new MyRowMapper(),"value");
    class MyRowMapper implements RowMapper<Entity>{
    重写mapRow{
        re.getString......;
        Entity Entity_name = new Entity();
        set........;
        return Entity;
        }
     }
    ```
    - 返回一个List
    ```
    List<Entity> list = jdbcTemplate.query(sql,new MyRowMapper());
    ```
- 连接池
```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql:///库"></property>
    <property name="user" value=""></property>
    <property name="password" value=""></property>
</bean>
//Service注入DAO
//DAO注入jdbcTemplate
//jdbcTemplate注入连接池
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
<property name="dataSource" ref="dataSource"></property>
</beans>
```
- 事务
    - 配置
    ```
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:advice id="txadvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="xxx*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:adbice>
    <aop:config>
        <aop:pointcut expression:"execution(* 类.切入点(..))" id="pointcut">
        <aop:advisor advice-ref="txadvice" pointcut-ref="pointcut"/>
    </aop:config>
    
    ```
    - 注解
    ```
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>
    @Transactional
    public class Service类{
    }
    ```
#### <span id="7">监听器 :</span>
```
<context-param>  
	<param-name>contextConfigLocation</param-name>  
	<param-value>classpath:配置文件路径</param-value>  
</context-param>  
<listen>  
	<listen-class>org.springframework.web.context.ContextLoaderListener</listen-class>
</listen>
```
#### <span id="8">SSH框架整合</span>
- Struts2 整合 Spring
```
spring中
<bean id="" class="类全路径" scope="prototype">
Struts2中
<action name="" class="beanid">
```
- Spring 整合 Hibernate
```
Hibernate内连接配置可以去掉
<bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
    <property name="configLocations" value="classpath:hibernate.cfg.xml"></property>
</bean>
```
- Hibernate Template
```
<bean id="Dao" class="Dao类">
    <property name="hibernateTemplate" ref="hibernateTemplate">
</bean>
<bean id="hibernateTemplate" class="org.springframework.orm.hibernate5.HibernateTemplate">
    <property name="sessionFactory" ref="sessionFactory"></property>
</bean>
<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
    <property name="sessionFactory" ref="sessionFactory"></property>
</bean>
<tx:annotation-driven  transaction-manager="transactionManager">
@Transactional
Service类{
}
```
```
get方法(根据id查询)
Entity Entity_name = hibernateTemplate.get(Entity.class,id);
find方法(条件查询)
List<Entity> list = (List<Entity>) hibernateTemplate.find(HQL,value);
分页查询
hibernateTemplate.findByCriteria(DetachedCriteria criteria,firstResult,maxResults)
```
- 分模块
```
<import resource="classpath:xxx.xml"/>
```
## <span id="Hibernate">Hibernate 框架</span>  
- [Hibernate](#Hibernate)  
    - [实体类](#11)
    - [配置](#12)
    - [实现](#13)
    - [CRUD](#14)
    - [事务](#15)
    - [绑定session](#16)
    - [查询](#17)
    - [一对多](#18)
    - [多对多](#19)
    - [查询(深入)](#20)
> 数据访问层框架  
  Hibernate 5.2.12
  
#### <span id="11">实体类:</span>  
```
  Public class 表名{  
    private 类型 属性名;  
    ....  
    get函数;  
    ....  
    set函数;  
    ....  
  }  
```
#### <span id="12">映射配置: </span>  
```
  <?xml version="1.0" encoding="UTF-8"?>  
  <!DOCTYPE hibernate-mapping PUBLIC  
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">  
<hibernate-mapping>  
	<class name="实体类全路径" table="表名">  
		<id name="主键属性名" column="主键字段名">  
			<generator class=""></generator>  
		</id>  
		<property name="属性名" column="字段名"></property>  
	</class>  
</hibernate-mapping>  
<xml-body>  
</xml-body>  
 
generator: increment / identity / sequence / native(√) / uuid(String) / assigned
```
#### 核心配置:  
```
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
  <hibernate-configuration>
	<session-factory>
		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/</property>
		<property name="hibernate.connection.username">root</property>
		<property name="hibernate.connection.password"></property>
		<property name="hibernate.show_sql">true</property>
		<property name="hibernate.format_sql">true</property>
		<property name="hibernate.hbm2ddl.auto">update</property>
		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
		<mapping resource="entity\userdata.hbm.xml"/>
	</session-factory>
  </hibernate-configuration>
  <xml-body>

  </xml-body>
```
#### <span id="13">实现：</span>
```  
  Configuration cfg = new Configuration();
  cfg.configure();
  SessionFactory sessionFactory = cfg.buildSessionFactory();
  Session session = sessionFactory.openSession();
  Transaction tx = session.beginTransaction();
  //功能
  userdata userdata = new userdata();
  session.save(userdata);
  //
  tx.commit();
  session.close();
  sessionFactory.close();
```
#### <span id="14">查找操作:</span>
```
//Configuration cfg = new Configuration();
//cfg.configure();
//SessionFactory sessionFactory = cfg.buildSessionFactory();
SessionFactory sessionFactory = HibernateUtils.getSessionFactory();
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();
//get语句
userdata userdata = session.get(userdata.class,记录主键值);
//输出 System.out.println.........
tx.commit();
session.close();
//sessionFactory.close();
```  
 
#### 修改操作:

```
userdata userdata = session.get(userdata.class,记录主键值);
userdata.set属性名(记录值);
session.update(userdata);
```  
 
#### 删除操作:
```
userdata userdata = session.get(userdata.class,记录主键值);
session.delete(userdata);
```  
#### <span id="15">事务:</span>
```
SessionFactory  sessionfactory = null;  
Session session = null;  
Transaction tx = null;  
try{  
	sessionfactory = HibernateUtils.getSessionFactory();  
	session = sessionfactory.openSession();  
	tx = session.beginTransaction();  
	//    	  
	tx.commit();  
}catch(Exception e){  
	tx.rollback();  
}finally{  
	 session.close();  
}  
```
#### <span id="16">绑定session:</span>  
```  
<property name="hibernate.current_session_context_class">thread</property>  
public static Session getSessionobject() {  
		return sessionFactory.getCurrentSession();  
	}  
Session session = HibernateUtils.getSessionobject();  
```
#### <span id="17">查询</span> 
- Query:  
```  
Query query = session.createQuery(HQL(from userdata));
List<userdata> list = query.list();
遍历
```
- Criteria:  
```  
Criteria criteria = session.createCriteria(userdata.class);
List<userdata> list = criteria.list();
遍历
```
- SQLQuery:  
```  
SQLQuery sqlQuery = session.createSQLQuery(SQL(select * from userdata));
//List<Object[]> list = sqlQuery.list();
//遍历(数组)
sqlQuery.addEntity(userdata.class);
List<userdata> list = sqlQuery.list();
遍历
```
#### <span id="18">一对多:</span>
- 映射:  
```
Entity1一对多Entity2:  
Entity1中  
private set<Entity2> Entity2_name = new HashSet<Entity2>();  
get()....set().....  
Entity2中  
private Entity1 Entity1_name;  
get()...set...  
Entity1.hbm.xml中  
<set name="Entity2_name">  
<key column="外键名"></key>
<one-to-many class="Entity2全路径"/>
</set>
Entity2.hbm.xml中  
<many-to-one name="Entity1_name" class="Entity1全路径" column="外键名"></many-to-one>
```
- 级联保存:  
```  
Entity1_name.getSetEntity2().add(Entity2_name1);  
Entity1_name.getSetEntity2().add(Entity2_name2);  
Entity2_name1.setEntity1(Entity1_name);  
Entity2_name2.setEntity1(Entity1_name);  
session.save(Entity1_name);  
session.save(Entity2_name1);  
session.save(Entity2_name2);  
或者
Entity1.hbm.xml中  
set标签内添加 cascade="save-update"  
Entity1_name.getSetEntity2().add(Entity2_name1);  
Entity1_name.getSetEntity2().add(Entity2_name2);  
session.save(Entity1_name);
```
- 级联删除:  
```
cascade加入delete  
session.delete(Entity1_name);
```
- 修改:  
```
Entity1.hbm.xml中  
set标签内添加 inverse="false"  
Entity1_name.getsetEntity2().add(Entity2_name);  
Entity2_name.setEntity1(Entity1_name);  
```
#### <span id="19">多对多:</span>
- 映射:  
```
Entity1中  
private set<Entity2> Entity2_name = new HashSet<Entity2>();  
get()....set().....  
Entity2中  
private set<Entity1> Entity1_name = new HashSet<Entity1>();  
get()....set()..... 
Entity1.hbm.xml中  
<set name="Entity2_name" table="维护的表名">  
<key column="Entity1外键名"></key>
<many-to-many class="Entity2全路径" column="Entity2外键名"/>
</set>
Entity2.hbm.xml中  
<set name="Entity1_name" table="维护的表名">  
<key column="Entity2外键名"></key>
<many-to-many class="Entity1全路径" column="Entity1外键名"/>
</set>
```
- 级联保存:  
```
若要用Entity1保存Entity2
Entity1.hbm.xml中  
set标签内添加 cascade="save-update"  
Entity1_name1.getSetEntity2().add(Entity2_name1);  
Entity1_name1.getSetEntity2().add(Entity2_name2);  
Entity1_name2.getSetEntity2().add(Entity2_name2);  
Entity1_name2.getSetEntity2().add(Entity2_name3); 
session.save(Entity1_name1);
session.save(Entity1_name2);
```
- 级联删除:  
```
//一般不用,也用不到
cascade加入delete  
session.delete(Entity1_name1);
```
- 维护第三张表:  
```
Entity1和Entity2相关联  
Entity1_name.getsetEntity2().add(Entity2_name);  
Entity1和Entity2取消关联  
Entity1_name.getsetEntity2().remove(Entity2_name);  
```
#### <span id="20">查询:</span> 
- 对象导航查询:  
```
Set<Entity2> Entity2_name = Entity1_name.getSetEntity2();
```
- OID查询:  
```
Entity Entity_name = session.get(Entity.class,记录主键值);
```
- HQL查询:  
```
Query query = session.createQuery(HQL);
query.setParameter(位置,值);
List<userdata> list = query.list();
遍历  
条件查询 from 实体类名 where 实体类属性 = ?  
条件查询 from 实体类名 where 实体类属性 like ?  
排序查询 freom 实体类名 order by 实体类属性 asc/desc  
分页查询 query.setFirstResult(第一条记录);query.setMaxResults(记录数);  
投影查询 select 实体类属性 from  
聚集函数 select 聚集函数 from...hving.. ...  Object obj = query.uniqueResult(); 遍历obj  
连接查询 from Entity1 e inner join e.setEntity2 ... List list = query.list();
迫切连接 from Entity1 e inner join fetch e.setEntity2 ... List list = query.list();
```
- QBC查询:  
```
Criteria criteria = session.createCriteria(Entity.class);
List<Entity> list = criteria.list();
遍历  
条件查询 criteria.add(Restrictions.方法(属性名,值));  
排序查询 criteria.addOrder(Order.方法(属性名));  
分页查询 criteria.setFirstResult(第一条记录);criteria.setMaxResults(记录数);  
统计查询 criteria.setProjection(Projection.rowCount());Object obj = criteria.uniqueResult();  
离线查询  
DetachedCriteria detachedcriteria = DetachedCriteria.forClass(Entity.class);  
Criteria criteria = detachedcriteria.getExecutableCriteria(session);  
批量抓取 set加入batch-size="10"标签
```
- 本地SQL查询:  
```
...
```
## <span id="Struts2">Struts2 框架</span> 
 - [Struts2](#Struts2)  
    - [配置](#21)
    - [创建action](#22)
    - [方法访问](#23)
    - [全局结果页面配置](#24)
    - [获取表单](#25)
    - [操作域对象](#26)
    - [封装到实体类](#27)
    - [值栈](#28)
    - [拦截器](#29)
> 表述层框架  
  struts-2.5.14  
  
#### <span id="21">配置 :</span>  
```
配置过滤器  
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
struts.xml  
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">
 <struts>
 		<package name="用于区分的名称" extends="struts-default" namespace="/"><!-- namespace+actionname 构成访问路径 -->
 				<action name="访问名称" class="类全路径" method="默认为execute方法">
 						<result name="返回值" type="默认为转发(dispatcher)chain/redirect/redirectAction">/返回页面</result>
 				</action>
 		</package>
 </struts>
 编码: <constant name="struts.i18n.encoding" value="UTF-8"></constant>  
 分模块 <include file="全路径"></include>
```
#### <span id="22">创建action:</span>  
- 1.  
```
public class 名称 {  
public String execute() {  
		return "返回值";  
	}  
}  
```
- 2
```
import com.opensymphony.xwork2.Action;  
public class 名称 implements Action {   
	@Override  
	public String execute() throws Exception {  
		// TODO Auto-generated method stub  
		return null;  
	}  
}  
```
- 3√
```
import com.opensymphony.xwork2.ActionSupport;  
public class 名称 extends ActionSupport {  
	@Override  
	public String execute() throws Exception {  
		// TODO Auto-generated method stub  
		return "";  
	}  
}  
```
#### <span id="23">方法访问:</span>  
```  
<package name="名称" extends="struts-default" namespace="/">  
 	<action name="index_*" class="全路径" method="{1}">  
 			<result name="返回值">/网页</result>  
 	</action>  
 </package>  
```
#### <span id="24">全局结果页面配置:</span>  
```  
<package>
<global-results><result name="....">/....</result></global-results>
```
#### <span id="25">获取表单:</span>  
```
//耦合低  
ActionContext context = ActionContext.getContext();  
Map<String,Object> map = context.getParameters();  
Set<String> keys = map.keySet();  
for(String key:keys)  
{  
	Object[] obj = (Object[])map.get(key);  
	...  
}  
```
或  
```
//常用  
HttpServletRequest request = ServletActionContext.getRequest();  
String xxx = request.getParameter("xxx");  
```
或  
```  
implements ServletRequestAware  
private HttpServletRequest request;  
public void setServletRequest(HttpServletRequest request) {  
	this.request = request;  
}  
```
#### <span id="26">操作域对象:</span>  
```
HttpServletRequest request = ServletActionContext.getRequest();  
request.setAttribute("名","值");  
HttpSession session = request.getSession();  
session.setAttribute("名","值");  
ServletContext context = ServletActionContext.getServletContext();  
context.setAttribute("名","值");  
```
#### <span id="27">封装到实体类:</span>  
```
public class index_action extends ActionSupport {  
	定义变量;  
	get()....set()....;  
	@Override  
	public String execute() throws Exception {  
		// TODO Auto-generated method stub  
		return "";  
	}  
}  
```
或  √
```
public class index_action extends ActionSupport implements ModelDriven<Entity> {  
	private Entity Entity_name = new Entity();  
	public Entity getModel(){  
		return Entity;  
	}  
	@Override  
	public String execute() throws Exception {  
		// TODO Auto-generated method stub  
		return "";  
	}  
}  
```
或  
```
<input name="Entity_name.name">  
private Entity Entity_name;  
get()...set()...  
```
#### <span id="28">值栈:</span>    
- ognl:  
```  
<% taglib uri="/struts-tags" prefix="s"%>  
<s:property value="OGNL">  
```
- 获取值栈对象  
```  
ActionContext context = ActionContext.getContext();  
ValueStack stack = context.getValueStack();  
```
- 值栈内放数据  
```
//stack.set("key","value");  
或  
//stack.push("value");  取 <s:property value="[...].top">  
或  
private String name;
get()....;  
name="value";  
```
- 值栈放对象:  
```  
private Entity Entity_name = new Entity();  
public Entity getEntity(){  
	return Entity_name;  
	}  
Entity_name.set().....;  
```
- 值栈放List:  
```  
private List<Entity> list = new ArrayList<Entity>();  
public List<Entity> getList(){  
	return list;  
}  
```
- 值栈取数据:  
```  
<s:property value="key"/>  
```
- 值栈取对象:  
```  
<s:property value="对象.属性"/>  
```
- 值栈取List:  
```  
<s:property value="List[...].属性"/> 或 <s:iterator value="list"><s:property value="属性"/></s:iterator>  
或 <s:iterator value="list" var="name"><s:property value="#name.属性"/><s:iterator>  
```
#### <span id="29">拦截器:</span>  
```
extends AbstractInterceptor 
//implements Interceptor   
void init()  
void destory()  
String intercept(ActionInvocation invocation)  
或  
extends MethodFilterInterceptor  
重写doIntercept(ActionInvocation invocation)方法  
配置拦截器:  
<package内>  
<interceptors><interceptor name="名称" class="全路径"></interceptor></interceptors>  
<action内>  
<interceptor-ref name="名称"><param name="excludeMethods">方法名</param></interceptor-ref>  
<interceptor-ref name="defaultStack"></interceptor-ref>
登录拦截器:  
public class 名称 extends MethodFilterInterceptor{  
	@Override  
	protected String doIntercept(ActionInvocation arg0) throws Exception {  
		// TODO Auto-generated method stub  
		HttpServletRequest request = ServletActionContext.getRequest();  
		Object obj = request.getSession().getAttribute("session名称");  
		if(obj != null){  
			return arg0.invoke();  
		}else{  
			return "登录页面标签";  
		}  
	}  
}  
```
## <span id="ELJSTL">EL表达式</span>
```
${ Expression }  
  ${ 变量1 运算符 变量2}  
  pageScope requestScope sessionScope applicationScope  
  ${ bean.property }  
  ${ collection[elementName] }  
  pageContext param paramValues cookie initParam  
```
## JSTL表达式
```
<c:out value="变量名"></c:out>  
<c:set value="值" scope="容器" var="元素名"></c:set>  
<c:remove var="元素名" scope="容器"></c:remove>  
<c:if test="${ 判断条件 }"> ... </c:if>  
<c:choose>  
<c:when test="${ 条件1 }">...</c:when>  
<c:when test="${ 条件2 }">...</c:when>  
<c:otherwise>...</c:otherwise>  
</c:choose>  
<c:forEach var="元素名"> items="集合名" begin="起始" end="结束" step="步长">....</c:forEach>  
<c:forTokens items="字符串" delims="分隔符" var="子串名" begin="起始" end="结束" step="步长">...</c:forTokens>  
${ fn:length(String或集合) }  
${ fn:contains("源字符串","子字符串")  
${ fn:startsWith("源字符串","指定字符串") }  
${ fn:endsWith("源字符串","指定字符串") }  
${ fn:escapeXML(特殊字符) }  
${ fn:indexOf("源字符串","指定字符串") }  
${ fn:join(数组,"分隔符") }  
${ fn:replace("源字符串","被替换字符","替换字符") }  
${ fn:split("源字符串","分隔符") }   
${ fn:substring("源字符串",起始位置,结束位置) }  
${ fn:substringAfter("源字符串","子字符串") }  
${ fn:substringBefore("源字符串","子字符串") }  
${ fn:toLowerCase("源字符串") }  
${ fn:toUpperCase("源字符串") }  
${ fn:trim("源字符串") } 
```
## <span id="AJAX">AXAJ与Struts2</span>
- Jquery
```
<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
<script type="text/javascript">  
$(function() {  
    $("#按钮id").click(function() {
    //提交的参数，name是struts action中对应的接收变量  
        var params = {  
           name : $("#表单名称").val()
        };  
        $.ajax({  
            type: "POST",  
            url: "Action名称",
            data: params,  
            dataType:"text",
            //ajax返回值设置为text（json格式也可用它返回，可打印出结果，也可设置成json） 
            success: function(json){    
                var obj = $.parseJSON(json);
                var state_value = obj.result;
                //result是和action中定义的result变量的get方法对应的
                方法......
                ....
            },  
            error: function(json){  
                方法...
                ...
                return false;  
            }  
        });  
    });  
});  
</script>
```
- struts.xml
```
 <package name="" extends="json-default">  
	<action name="" class="" method="">  
		<result name="success" type="json"></result>  
	</action>  
</package> 
```
- Action
```
package action;
import com.opensymphony.xwork2.ActionSupport;
public class AjaxAction extends ActionSupport{
	private static final long serialVersionUID = 1L;
	private String name;  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
    private String result;  
    public String getResult() {  
        return result;  
    }  
    public void setResult(String result) {  
        this.result = result;  
    }  
    public String addType() {  
        result = f(typename);  
        ....
        return SUCCESS;  
    }  
}

```
## <span id="BootStrap">栅格与响应式</span>
- 栅格
```
超小屏幕 手机 (<768px) .col-xs-
小屏幕 平板 (≥768px) .col-sm-
中等屏幕 桌面显示器 (≥992px) .col-md-
大屏幕 大桌面显示器 (≥1200px) .col-lg-
<div class="row">
  <div class="col-xx-n">12栅格</div>
</div>
```
- 响应式
```
hidden-xs 超小屏幕不可见
visible-xs-block 在超小屏幕上可见
hidden-sm 小屏幕不可见
visible-sm-block 在小屏幕上可见不可见
hidden-md 中等屏幕不可见
visible-md-block 在中等屏幕上可见
hidden-lg 大屏幕不可见
visible-lg-block 在大屏幕上可见
hidden-xs hidden-sm 超小屏幕和小屏幕不可见
visible-xs-block visible-sm-block 在超小屏幕和小屏幕上可见
hidden-md hidden-lg 中等屏幕和大屏幕不可见
visible-md-block visible-lg-block 在中等屏幕和大屏幕上可见
hidden-xs hidden-md 超小屏幕和中等屏幕不可见
visible-xs-block visible-md-block 在超小屏幕和中等屏幕上可见
hidden-sm hidden-lg 小屏幕和大屏幕不可见
visible-sm-block visible-lg-block 在小屏幕和大屏幕上可见
hidden-xs hidden-lg 超小屏幕和大屏幕不可见
visible-xs-block visible-lg-block 在超小屏幕和大屏幕上可见
hidden-sm hidden-md 小屏幕和中等屏幕不可见
visible-sm-block visible-md-block 在小屏幕和中等屏幕上可见
```
## <span id="websocket">websocket</span>
- websocket
    - 前端页面
    ```
    <%@ page language="java" pageEncoding="UTF-8" %>
    <!DOCTYPE html>
    <html>
    <head>
        <title>WebSocket</title>
    </head>
    <body>
        <button onclick="send()">发送消息</button>
        <button onclick="closeWebSocket()">关闭WebSocket连接</button>
    </body>
    
    <script type="text/javascript">
        var websocket = null;
        //判断当前浏览器是否支持WebSocket
        if ('WebSocket' in window) {
           websocket = new WebSocket("ws://112.74.97.185/websocket"); // 此处为服务器地址!!!不能是localhost!!!
        }
        else {
            alert('当前浏览器不支持websocket')
        }
        //连接发生错误的回调方法
        websocket.onerror = function () {
            setMessageInnerHTML("WebSocket连接发生错误");
        };
        //连接成功建立的回调方法
        websocket.onopen = function () {
            //..
        }
    
        //接收到消息的回调方法
        websocket.onmessage = function (event) {
            //..
        }
        //连接关闭的回调方法
        websocket.onclose = function () {
            //..
        }
        //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
        window.onbeforeunload = function () {
            closeWebSocket();
        }
        //关闭WebSocket连接
        function closeWebSocket() {
            websocket.close();
        }
        //发送消息
        function send() {
            var message = //..
            websocket.send(message);
        }
    </script>
    </html>

    ```
    - JAVA类
    ```
    package websocket;
    import java.io.IOException;
    import java.util.concurrent.CopyOnWriteArraySet;
    import javax.websocket.*;
    import javax.websocket.server.ServerEndpoint;
    // @ServerEndpoint 注解是一个类层次的注解
    @ServerEndpoint("/websocket")
    public class Websocket {
        //静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
        private static int onlineCount = 0;
        //concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象。若要实现服务端与单一客户端通信的话，可以使用Map来存放，其中Key可以为用户标识
        private static CopyOnWriteArraySet<Websocket> webSocketSet = new CopyOnWriteArraySet<Websocket>();
        //与某个客户端的连接会话，需要通过它来给客户端发送数据
        private Session session;
        /**
         * 连接建立成功调用的方法
         * @param session  可选的参数。session为与某个客户端的连接会话，需要通过它来给客户端发送数据
         */
        @OnOpen
        public void onOpen(Session session){
            this.session = session;
            webSocketSet.add(this);     //加入set中
        }
        //连接关闭调用的方法
        @OnClose
        public void onClose(){
            webSocketSet.remove(this);  //从set中删除
        }
        /**
         * 收到客户端消息后调用的方法
         * @param message 客户端发送过来的消息
         * @param session 可选的参数
         */
        @OnMessage
        public void onMessage(String message, Session session) {
            //群发消息
            for(Websocket item: webSocketSet){
                try {
                    item.sendMessage(message);
                } catch (IOException e) {
                    e.printStackTrace();
                    continue;
                }
            }
        }
        //发生错误时调用
        @OnError
        public void onError(Session session, Throwable error){
            System.out.println("发生错误");
            error.printStackTrace();
        }
        public void sendMessage(String message) throws IOException{
            this.session.getBasicRemote().sendText(message);
        }
    ```
- websocket使用http域对象
    - Configurator类中
    ```
    public class GetHttpSessionConfigurator extends Configurator {
        @Override
        public void modifyHandshake(ServerEndpointConfig sec,
                HandshakeRequest request, HandshakeResponse response) {
            // TODO Auto-generated method stub
            HttpSession httpSession=(HttpSession) request.getHttpSession();
            sec.getUserProperties().put(HttpSession.class.getName(),httpSession);
        }
    }

    ```
    - websocket类中
    ```
    @ServerEndpoint(value="/...",configurator=GetHttpSessionConfigurator.class)
    public void ...(...,EndpointConfig config)
    HttpSession httpSession= (HttpSession)config.getUserProperties().get(HttpSession.class.getName());
    ```
