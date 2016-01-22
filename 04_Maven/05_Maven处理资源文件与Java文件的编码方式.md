## 配置 Maven 处理资源文件与 Java 文件的编码方式
Maven 处理资源文件是通过 `maven-resources-plugin` 插件完成的，编译 Java 文件是通过 `maven-compiler-plugin` 插件完成的，我们可以通过给这 2 个插件配置 `encoding` 属性来为 Maven 指定具体的编码方式。

pom.xml 文件：

	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-resources-plugin</artifactId>
			<version>2.7</version>
			<configuration>
				<!-- 编码 -->
				<encoding>UTF-8</encoding>
				<skip>false</skip>
			</configuration>
		</plugin>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>3.3</version>
			<configuration>
				<source>1.7</source>
				<target>1.7</target>
				<!-- 编码 -->
				<encoding>UTF-8</encoding>
				<skip>false</skip>
			</configuration>
		</plugin>
	</plugins>