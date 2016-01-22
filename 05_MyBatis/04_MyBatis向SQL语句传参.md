## Mybatis 向 SQL 语句传参
### 方式1：直接传入 SQL 语句需要的参数
所谓“直接传入 SQL 语句需要的参数”是指，在定义 Mapper 方法的时候把 SQL 语句需要的参数以形参的形式定义在方法上，在执行 SQL 语句时，把参数传入方法即可，比如，我们需要根据 ID 查询用户，就把 ID 定义成对应方法的形参。

这种方式的难处在于如何拿到传入方法的参数，能不能通过定义方法时指定的形参的名字呢，答案是不能，因为通过反射获取方法的定义时是无法获取形参的名字的，这就更谈不上通过形参的名字来取实参的值了。

MyBatis 为这种传参方式提供了 2 种取值方法，一种是按参数的次序取，一种是允许用户用 `@Param` 注解为形参定义一个标识，然后通过标识来取。

1. 按次序取值
	* \#{n}（n >= 0）
		> \#{0} 代表第1个参数，\#{1} 代表第2个参数，依次类推

	* \#{paramn}（n >= 0）
		> \#{param0} 代表第1个参数，\#{param1} 代表第2个参数，依次类推

	示例：<br/>
	.java：

		Teacher selectByIdNameAge(Integer id, String name, Integer age);

	.xml:

		<!-- #{n}的形式 -->
		<select id="selectByIdNameAge" resultMap="BaseResultMap">
			SELECT * FROM TEACHER T WHERE
			T.ID = #{0, jdbcType=NUMERIC, javaType=int} AND
			T.NAME = #{1, jdbcType=VARCHAR, javaType=string} AND
			T.AGE = #{2, jdbcType=NUMERIC, javaType=int}
		</select>

		<!-- #{paramn}的形式 -->
		<select id="selectByIdNameAge" resultMap="BaseResultMap">
			SELECT * FROM TEACHER T WHERE
			T.ID = #{param1, jdbcType=NUMERIC, javaType=int} AND
			T.NAME = #{param2, jdbcType=VARCHAR, javaType=string} AND
			T.AGE = #{param3, jdbcType=NUMERIC, javaType=int}
		</select>
2. @Param注解
	
	`@Param` 注解用来标记 Mapper 方法的形参，**必须**指定 `value` 属性，`value` 属性的值就是形参的标识。

	示例：<br/>
	.java:

		Teacher selectByIdNameAge(@Param("id") Integer id, @Param("name") String name, @Param("age") Integer age);

	.xml:

		<select id="selectByIdNameAge" resultMap="BaseResultMap">
			SELECT * FROM TEACHER T WHERE
			T.ID = #{id, jdbcType=NUMERIC, javaType=int} AND
			T.NAME = #{name, jdbcType=VARCHAR, javaType=string} AND
			T.AGE = #{age, jdbcType=NUMERIC, javaType=int}
		</select>
### 方式2：传入 Map 集合
MyBatis 也允许我们把需要传入的参数放入一个 Map 中，其中的 key 为标识，value 为值，将来就可以根据标识 key 来取对应的值。

在传入 Map 的情况下，我们应该显式告诉 MyBatis 传入的是 Map（当然这不是必须的），可以通过指定 `parameterType` 属性来实现（即使不指定 `parameterType` 属性 MyBatis 也可以正常工作）。

示例：<br/>
.java:

	// 方法定义
	TeacherEntity selectTeacherByIdNameAge02(Map<String, Object> params);
	
	// 调用
	TeacherMapper teacherMapper = session.getMapper(TeacherMapper.class);
	Map<String, Object> params = new HashMap<String, Object>();
	params.put("id", "1");
	params.put("name", "小明");
	params.put("age", 23);
	TeacherEntity teacher = teacherMapper.selectTeacherByIdNameAge02(params);

.xml:

	<select id="selectTeacherByIdNameAge02" parameterType="map" resultMap="TeacherBaseResultMap">
    	select * from teacher_tbl t where 
    	t.teacher_id = #{id, jdbcType=VARCHAR, javaType=string}
    	and t.teacher_name = #{name, jdbcType=VARCHAR, javaType=string}
    	and t.teacher_age = #{age, jdbcType=NUMERIC, javaType=int}
    </select>
### 方式3：出入 POJO
MyBatis 也允许我们把参数封装进一个 POJO 中然后传入，其中成员属性的名字为标识，将来就可以通过成员属性的名字获取参数值。

在传入 POJO 的情况下，我们应该显式告诉 MyBatis 传入的是 POJO（当然这不是必须的），可以通过指定 `parameterType` 属性来实现（即使不指定 `parameterType` 属性 MyBatis 也可以正常工作）。

示例：<br/>
.java:

	// 方法定义
	TeacherEntity selectTeacherByIdNameAge03(TeacherEntity teacher);

	// 调用
	TeacherMapper teacherMapper = session.getMapper(TeacherMapper.class);
	TeacherEntity teacher = new TeacherEntity();
	teacher.setTeacherId(1);
	teacher.setTeacherName("小明");
	teacher.setTeacherAge(23);
	teacher = teacherMapper.selectTeacherByIdNameAge03(teacher);

.xml:

	<select id="selectTeacherByIdNameAge03" parameterType="teacherEntity" resultMap="TeacherBaseResultMap">
    	select * from teacher_tbl t where 
    	t.teacher_id = #{teacherId}
    	and t.teacher_name = #{teacherName}
    	and t.teacher_age = #{teacherAge}
    </select>
