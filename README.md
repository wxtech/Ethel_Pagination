# Ethel_Pagination

# 简介 #
	Ethel是一款基于mybatis的分页插件，支持多种数据库，简单配置就可以使用。前后端可以完全分离，传递需要的参数到后台就可以，通过json与前端交互。

- github：[https://github.com/wesley5201314/Ethel_Pagination](https://github.com/wesley5201314/Ethel_Pagination)
- gitOSC:[http://git.oschina.net/zhengweishan/Ethel_Pagination](http://git.oschina.net/zhengweishan/Ethel_Pagination)

# 使用 #
## 简单配置 ##
mybatis-config.xml添加如下代码：

	//数据库方言选择
    <properties>
    	<property name="dialectClass" value="com.ethel.pagination.dialect.MySql5Dialect"/>
    </properties>

	//插件配置
 	<plugins>
        <plugin interceptor="com.ethel.pagination.dialect.mybatis.PaginationStatementHandlerInterceptor"/>
    </plugins>

完整代码：

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		
	<properties>
		<property name="dialectClass" value="com.ethel.pagination.dialect.MySql5Dialect"/>
	</properties>
	
	<!-- 配置mybatis的缓存，延迟加载等等一系列属性 -->
	<settings>
	
	<!-- 全局映射器启用缓存 -->
	<setting name="cacheEnabled" value="true"/>
	
	<!-- 查询时，关闭关联对象即时加载以提高性能 -->
	<setting name="lazyLoadingEnabled" value="true"/>
	
	<!-- 对于未知的SQL查询，允许返回不同的结果集以达到通用的效果 -->
	<setting name="multipleResultSetsEnabled" value="true"/>
	
	<!-- 允许使用列标签代替列名 -->
	<setting name="useColumnLabel" value="true"/>
	
	<!-- 不允许使用自定义的主键值(比如由程序生成的UUID 32位编码作为键值)，数据表的PK生成策略将被覆盖 -->
	<setting name="useGeneratedKeys" value="false"/>
	
	<!-- 给予被嵌套的resultMap以字段-属性的映射支持 FULL,PARTIAL -->
	<setting name="autoMappingBehavior" value="PARTIAL"/>
	
	<!-- 对于批量更新操作缓存SQL以提高性能 BATCH,SIMPLE -->
	<setting name="defaultExecutorType" value="SIMPLE" />
	
	<!-- 数据库超过25000秒仍未响应则超时 -->
	<!-- <setting name="defaultStatementTimeout" value="25000" /> -->
	
	<!-- Allows using RowBounds on nested statements -->
	<setting name="safeRowBoundsEnabled" value="false"/>
	
	<!-- Enables automatic mapping from classic database column names A_COLUMN to camel case classic Java property names aColumn. -->
	<setting name="mapUnderscoreToCamelCase" value="true"/>
	
	<!-- MyBatis uses local cache to prevent circular references and speed up repeated nested queries. By default (SESSION) all queries executed during a session are cached. If localCacheScope=STATEMENT 
	local session will be used just for statement execution, no data will be shared between two different calls to the same SqlSession. -->
	<setting name="localCacheScope" value="SESSION"/>
	
	<!-- Specifies the JDBC type for null values when no specific JDBC type was provided for the parameter. Some drivers require specifying the column JDBC type but others work with generic values 
	like NULL, VARCHAR or OTHER. -->
	<setting name="jdbcTypeForNull" value="OTHER"/>
	
	<!-- Specifies which Object's methods trigger a lazy load -->
	<setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
	
	<!-- 设置关联对象加载的形态，此处为按需加载字段(加载字段由SQL指 定)，不会加载关联表的所有字段，以提高性能 -->
	<setting name="aggressiveLazyLoading" value="false"/>
	
	</settings>
	
	<typeAliases>
	<package name="com.ethel.pagination.*.po"/>
	</typeAliases>
	
	<plugins>
		<plugin interceptor="com.ethel.pagination.dialect.mybatis.PaginationStatementHandlerInterceptor"/>
	</plugins>
	
	</configuration>

## 简单使用 ##
说明：可以用内部提供的com.ethel.pagination.dialect.mybatis.RspPage作为返回实体，也可以自己定义。
示例代码：

	//controller
	@RestController
	public class RestDataController {
	
		@Resource
		private PropertyService propertyService;
		
		@RequestMapping("/dataList")
		public RspPage<Property> list(Integer pageIndex, Integer pageSize){
			if(null == pageIndex){
			pageIndex = 1; //默认从第一页开始查
		}
			pageIndex = pageIndex + 1; //dataTable插件默认传递的是pageIndex是0，需要加1，我前端用的datatable
		if(null == pageSize){
			pageSize = 10; //一页10条数据
		}
		//返回数据
			RspPage<Property> pages = propertyService.queryList(pageIndex,pageSize);
			return pages;
		}
	}

	//service
	public RspPage<Property> queryList(Integer pageNo, Integer pageSize) {
		//分页对象
		Page<Property> page = new Page<Property>(pageNo,pageSize);
		List<Property> list = propertyMapper.queryList(page);
		//分页数据返回
		RspPage<Property> rspPage = new RspPage<Property>();
		rspPage.setRows(list);
		rspPage.setTotal(page.getTotalCount());
		rspPage.setTotalPages(page.getTotalPages());
		return rspPage;
	}

	//dao
	List<Property> queryList(Page<Property> page);

mapper文件查询语句：
	
	<select id="queryList" resultMap="BaseResultMap">
		select 
		<include refid="Base_Column_List" />
		from loupan
	</select>

你会发现这个sql中并没有分页参数，插件帮你做了这个事情，是不是很清爽的sql啊。

相关连接：

1. mybatis：[https://github.com/mybatis](https://github.com/mybatis)
2. mybatis blog:[http://blog.mybatis.org](http://blog.mybatis.org)