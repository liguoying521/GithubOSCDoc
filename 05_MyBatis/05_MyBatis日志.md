## MyBatis 日志

关于 MyBatis 日志的具体介绍请参见 [MyBatis 官方文档的日志部分](http://www.mybatis.org/mybatis-3/zh/logging.html)，本文不再赘述。

本文仅仅来说明一些需要注意的点。

日志功能对 MyBatis 来说不是必须的，无论有还是没有，MyBatis 都可以很好的运行，只不过有的话MyBatis 可以输出详细的调试信息。

在没有显式指定日志实现的情况下，MyBatis 会按照一定的顺序自动搜索可用的日志实现，它会使用最先找到的，如果一个都未找到，日志功能就会被禁用。

市面上常用的日志实现的优先级(依次降低)如下：

* SLF4J
* Apache Commons Logging
* Log4j2
* Log4j
* JDK logging

有些应用服务器自带了较高优先级的日志的私有实现(比如 WebSphere 提供了 Commons Logging 的私有实现)，在这种情况下你想使用低优先级的日志实现(比如 Log4j)，但是你又没有显式告知 MyBatis 使用它，MyBatis 就会使用应用服务器自带的私有实现。

还有一种比较诡异的情况，应用服务器没有带日志的私有实现，我们也为应用添加了日志功能，但是 MyBatis 依然不能打印日志，我在用 JUnit 进行单元测试时就碰到过这种情况，我自己写的日志信息能打印出来，MyBatis 就打印不出来。

为了避免上面情况的出现，MyBatis 允许我们显式指定我们打算使用的日志实现，我们可以通过以下2种方式来指定具体的日志实现：

* 通过编程的方式实现

	在调用任何 MyBatis 方法之前调用下列方法中的一个：
		
		org.apache.ibatis.logging.LogFactory.useCommonsLogging();
		org.apache.ibatis.logging.LogFactory.useJdkLogging();
		org.apache.ibatis.logging.LogFactory.useLog4J2Logging();
		org.apache.ibatis.logging.LogFactory.useLog4JLogging();
		org.apache.ibatis.logging.LogFactory.useSlf4jLogging();
		org.apache.ibatis.logging.LogFactory.useStdOutLogging();

	需要注意的是，这种方式必须要保证在调用任何 MyBatis 方法之前调用这些方法，这种方式在单独使用 MyBatis 时是可行的，却不适用于 MyBatis 与 Spring 整合的情况。

* 通过配置的方式实现

	MyBatis 为显式配置日志实现提供了一个参数 `logImpl`。我们只需在配置文件中配置该参数即可：

		<settings>
	        <setting name="logImpl" value="SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING"/>
	    </settings>

我们以 log4j2 为例来为 MyBatis 添加日志功能。

第一步：导入 log4j2 的相关 jar 包

	<dependency>
		<groupId>org.apache.logging.log4j</groupId>
		<artifactId>log4j-core</artifactId>
		<version>2.4.1</version>
	</dependency>

第二步：配置 `logImpl` 参数，启用 log4j2 日志

	<settings>
        <setting name="logImpl" value="LOG4J2"/>
    </settings>

第三步：创建 log4j2 的配置文件 `log4j2.xml`

	<?xml version="1.0" encoding="UTF-8"?>
	<Configuration status="ERROR">
		<Appenders>
			<Console name="ConsoleOut" target="SYSEM_OUT">
				<PatternLayout pattern="%d %-5p %c [%t] %m%n" />
			</Console>
		</Appenders>
	
		<loggers>
			<!-- 所有没有匹配 Logger 的日志都由 Root Logger 来打印 -->
			<Root level="DEBUG">
				<AppenderRef ref="ConsoleOut" />
			</Root>
			<!-- MyBatis Logger -->
			<Logger name="org.apache.ibatis" level="ERROR" additivity="false">
				<AppenderRef ref="ConsoleOut"/>
			</Logger>
			<!-- 为 org.lgy.mybatis.mapper 包下的所有类的所有方法指定 Logger -->
			<Logger name="org.lgy.mybatis.mapper" level="TRACE" additivity="false">
				<AppenderRef ref="ConsoleOut"/>
			</Logger>
			<!-- 为 org.lgy.mybatis.mapper.ClassMapper 类(或命名空间)中的所有方法、指定 Logger -->
			<Logger name="org.lgy.mybatis.mapper.ClassMapper" level="DEBUG" additivity="false">
				<AppenderRef ref="ConsoleOut"/>
			</Logger>
			<!-- 为 org.lgy.mybatis.mapper.ClassMapper 类(或命名空间)中的 logTest01 方法指定日志 -->
			<Logger name="org.lgy.mybatis.mapper.ClassMapper.logTest01" level="INFO" additivity="false">
				<AppenderRef ref="ConsoleOut"/>
			</Logger>
		</loggers>
	</Configuration>

到此为止，MyBatis 就已经可以打印日志了。

下面以调用 `org.lgy.mybatis.mapper.ClassMapper.logTest01` 方法为例分析一下 MyBatis 获取 Logger 的过程。

MyBatis 会试图获取 `name="org.lgy.mybatis.mapper.ClassMapper.logTest01"` 的 Logger，如果有，则使用该 Logger 打印日志；否则会再尝试获取其父 Logger，即 `name="org.lgy.mybatis.mapper.ClassMapper"`，如果有，则使用该 Logger 打印日志；否则会再尝试获取其父 Logger 的父 Logger，即 `name="org.lgy.mybatis.mapper"` 的 Logger，依次类推。

由于 Root Logger 是所有 Logger 的父亲，所以上述查找的过程最终会查找到 Root Logger，使用 Root Logger 来打印日志。

MyBatis 不仅仅会打印我们定义的 mapper 方法的日志，也会打印它自己的日志，所以我们在 `log4j2.xml` 文件中定义了一个 `name="org.apache.ibatis"` 的 Logger 专门用来让 MyBatis 打印自己的日志。如果有一天我们不想看到 MyBatis 自己的日志信息，只想看我们的日志信息，只需修改这个 Logger 的日志级别即可。