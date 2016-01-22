## MyBatis自动映射
### 什么是自动映射？
自动映射是指 MyBatis 根据一定的规则将结果集 ResultSet 中的列映射成 POJO 中对应属性，并将列值赋给对应属性的过程。

自动映射的好处就是，我们无需再手动建立结果集 ResultSet 中的列与 POJO 中属性的对应关系，查询完成后返回的 POJO 对象的各个属性的值已被正确的设置。
### 自动映射的规则有哪些？
1. SQL 返回的列名与 POJO 属性名相同(忽略大小写)
2. 先执行自动映射(如果开启了的话)，再执行手动映射(如果有的话)，如果某些属性既有自动映射又有手动映射，那么后者将覆盖前者
### 如何开启/关闭自动映射？
我们知道，MyBatis 的查询有 2 种指定返回结果的方式，一种是指定 resultType，另一种是指定 resultMap，而我们定义 resultMap 的目的就是手动建立列与属性的映射关系，在这种情况下，MyBatis 允许自动映射与手动映射并存。

如果指定的是 resultType，那么只能通过自动映射来封装查询结果(因为我们没有地方可以再手动指定映射关系)，这种情况下关闭自动映射，将导致 MyBatis 始终返回 null，即使能查询到结果。

对于指定 resultType 的情形，我们只能通过 `autoMappingBehavior` 全局设置来关闭自动映射(注意：这种设置对指定 resultMap 的情形同样适用)，并且在这种情形下，`PARTIAL` 和 `FULL` 的作用是相同的。

    <settings>
        <setting name="autoMappingBehavior" value="NONE|PARTIAL|FULL"/>
    </settings>
其中，`NONE` 表示关闭自动映射，`PARTIAL` 和 `FULL` 都表示开启自动映射，只是级别不一样，关于它们的区别后面再讲，默认值是 `PARTIAL`。

对于指定 resultMap 的情形，我们除了通过 `autoMappingBehavior` 全局设置来关闭自动映射外，还可以单独关闭某个 resultMap 的自动映射。

    <resultMap type="" id="" autoMapping="true|false">
    	...
    </resultMap>
`true` 代表开启该 resultMap 的自动映射功能，`false` 则代表关闭。

注意：对于指定 resultType 的情况，如果关闭自动映射，将导致查询结果始终返回 null，如下所示：

MyBatis 配置文件：

    <settings>
        <setting name="autoMappingBehavior" value="NONE"/>
    </settings>

mapper 文件：

	<select id="test01" parameterType="int" resultType="map">
        SELECT 
        	*
        FROM 
        	class_tbl t 
        WHERE 
        	t.class_id = #{id}
    </select>

Java 文件：

	@Test
	public void test01() {
		ClassMapper mapper = this.session.getMapper(ClassMapper.class);
		Object cls = mapper.test01(3); // 始终返回 null
		Assert.assertNotNull(cls); // 失败
		System.err.println(cls);
	}

上面的示例是以 map 为例，对于其他类型效果一样。

### MyBatis 自动映射的3种级别
上面我们提及了 MyBatis 自动映射的3种级别：NONE、PARTIAL、FULL，下面分别讨论。

* NONE：关闭自动映射
* PARTIAL：自动映射除与嵌套结果有关的列以外的列与属性
* FULL：自动映射所有列与属性，包括嵌套结果中的列

`PARTIAL` 与 `FULL` 的区别看下面的示例：

	<select id="selectBlog" resultMap="blogResult">
	  select
	    B.id,
	    B.title,
	    A.username,
	  from Blog B left outer join Author A on B.author_id = A.id
	  where B.id = #{id}
	</select>
	
	<resultMap id="blogResult" type="Blog">
	  <association property="author" resultMap="authorResult"/>
	</resultMap>
	
	<resultMap id="authorResult" type="Author">
	  <result property="username" column="author_username"/>
	</resultMap>

Blog 类中有一个 Author 类型的属性，在 Blog 对应的 resultMap 中使用 association 关联了该属性，并为其指定了 Author 对应的 resultMap，这就是使用嵌套结果来进行关联查询。

我们知道通过 blogRsult 来组装 Blog 对象时，会触发自动装配，能够自动装配 id 列与 Blog.id 属性；在通过 authorResult 来组装 Author 对象时，也应该触发自动装配，这个自动装配是否触发完全有自动映射级别决定：`PARTIAL` 级别不会触发，而 `FULL` 级别会触发。

`PARTIAL` 级别不会触发导致的结果是 Author.id 为 null，这是正确的，因为我们根本就没有查 Author 的 id。

而 `FULL` 级别会触发导致的结果是把 B.id 列的值赋给 Author.id 属性，这就完全不对了。

所以必须谨慎的使用 `FULL` 级别！！

最后列几个自动映射的注意事项：

1. autoMappingBehavior 的默认值是 PARTIAL, 即自动映射默认是开启的
2. 如果所有列都无法映射到某个属性, 那么 MyBatis 将返回 null，即使能查询到记录
3. 自动映射在查找同名的列与属性时，忽略大小写
4. 先执行自动映射(如果开启了的话)，再执行手动映射(如果有的话)，如果某些属性既有自动映射又有手动映射，那么后者将覆盖前者