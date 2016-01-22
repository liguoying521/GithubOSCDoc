## 配置 Maven 编译时使用的 JDK 版本
当在代码中使用高版本 JDK 的特性，Maven 却使用低版本 JDK 来编译时，就会出现类似下面的错误提示：<br/>
![](https://github.com/liguoying521/GithubOSCImages/raw/master/25.png)

这个时候我们就需要配置 Maven 使用高版本的 JDK 来编译项目。

在 Maven 中有 2 种方式来配置编译时使用的 JDK 版本，一种是在 `settings.xml` 文件中配置，这种配置对所有项目都有效，另一种是在 `pom.xml` 文件中配置，这种配置仅对当前项目有效，下面分别讨论。

settings.xml 文件：

	<profiles>
	    <profile>
			<id>jdk-1.7</id>
			<activation>
				<activeByDefault>true</activeByDefault>
				<jdk>1.7</jdk>
			</activation>
			<properties>
				<maven.compiler.source>1.7</maven.compiler.source>
				<maven.compiler.target>1.7</maven.compiler.target>
				<maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
			</properties>
		</profile>
	</profiles>

pom.xml 文件：

	<plugins>
  		<plugin>
  			<groupId>org.apache.maven.plugins</groupId>
  			<artifactId>maven-compiler-plugin</artifactId>
  			<version>3.3</version>
  			<configuration>
  				<source>1.7</source>
  				<target>1.7</target>
  				<encoding>UTF-8</encoding>
  				<skip>false</skip>
  			</configuration>
  		</plugin>
  	</plugins>

最后别忘了执行 maven -- update project