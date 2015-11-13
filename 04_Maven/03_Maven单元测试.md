## Maven 单元测试

<a name="catagory"></a>
### 目录
* [maven-surefire-plugin 插件简介](#a)
* [跳过单元测试](#b)
* [跳过单元测试](#c)

<a name="a"></a>
### maven-surefire-plugin 插件简介（[返回目录](#catagory)）

>Maven 本身并不是一个单元测试框架，它只是在构建过程执行到特定生命周期阶段的时候，通过插件来执行 JUnit 或者 TestNG 的测试用例。
>
>这个插件就是 maven-surefire-plugin ，也可以称为测试运行器(Test Runner)，它能兼容 JUnit 3、JUnit 4 以及 TestNG 。
>
>在默认情况下，maven-surefire-plugin 的 test 目标会自动执行测试源码路径（默认为src/test/java/）下所有符合一组命名模式的测试类。
>
>这组命名模式如下：

>* `.../Test*.java`：任何子目录下所有命名以 Test 开关的 Java 类。
>* `.../*Test.java`：任何子目录下所有命名以 Test 结尾的 Java 类。
>* `.../*TestCase.java`：任何子目录下所有命名以 TestCase 结尾的 Java 类。

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/019.png/?dir=0&filepath=019.png&oid=4735e5be3beae7994fe09a91c4c4d70741a7700c&sha=20480c15b082044e905760540a134782fd8b2c0c)

<a name="b"></a>
### 跳过单元测试（[返回目录](#catagory)）

跳过单元测试有2种方式，一种是把 Maven 本身配置成跳过测试阶段，另一种是配置各个插件，使它们跳过有关测试的步骤。

测试阶段不仅仅指运行测试用例，还包括处理测试资源、编译测试用例等步骤。

如果通过配置 Maven 来跳过测试阶段，那么将同时跳过测试阶段的所有步骤，无法只跳过某些步骤。如果希望跳过某些步骤，可以通过配置插件的方式来实现。

* 配置 Maven

	>Maven 跳过测试是通过环境变量 maven.test.skip 来控制的。

	有2种方式可以配置：

	* 在命令行（暂时的，只针对本次命令）

			mvn package -Dmaven.test.skip=true

	* 在 pom.xml 中（永久的）

			<properties>
			  <maven.test.skip>true</maven.test.skip>
			</properties>

* 配置插件

	与测试阶段有的插件主要有3个 `maven-resources-plugin` 、`maven-compiler-plugin` 、`maven-surefire-plugin`，分别负责复制测试资源、编译测试用例、运行测试用例，可以通过配置相应插件来跳过特定步骤。

	有2种方式可以配置：

	* 在 pom.xml 中

			<build>
				<plugins>
					<!-- 跳过复制测试资源的步骤 -->
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-resources-plugin</artifactId>
						<version>2.6</version>
						<configuration>
							<skip>true</skip>
						</configuration>
					</plugin>
					<!-- 跳过编译测试用例的步骤 -->
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-compiler-plugin</artifactId>
						<version>3.1</version>
						<configuration>
							<skip>true</skip>
						</configuration>
					</plugin>
					<!-- 跳过运行测试用例的步骤 -->
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-surefire-plugin</artifactId>
						<version>2.12.4</version>
						<configuration>
							<skipTests>true</skipTests>
						</configuration>
					</plugin>
				</plugins>
			</build>

	* 在命令行（只能跳过运行测试用例的步骤，无法跳过前2个步骤）

			mvn package -DskipTests=true

		暂时还不清楚如何通过命令行参数跳过复制测试资源和编译测试用例步骤。

	下面是效果图：

	![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/020.png/?dir=0&filepath=020.png&oid=34db5316ac4587216f933d9cc08292a85bd91d10&sha=0bbed84a9802b18290e0be4ed59e2a295a306381)



	![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/021.png/?dir=0&filepath=021.png&oid=b3537eb9315848e9d2ff767d8db5fc845aa67732&sha=0bbed84a9802b18290e0be4ed59e2a295a306381)

<a name="c"></a>
### 动态指定要运行的测试用例（[返回目录](#catagory)）

* 通过 `test` 命令行参数

	`maven-surefire-plugin` 提供了一个 test 参数让用户能够在命令行指定要运行的测试用例。如：
	
		mvn test -Dtest=RandomGeneratorTest
	
	也可以使用通配符：
	
		mvn test -Dtest=Random*Test
	
	或者也可以使用“,”号指定多个测试类：
	
		mvn test -Dtest=Random*Test,AccountCaptchaServiceTest
	
	如果没有指定测试类或没有找到指定的测试类，那么会报错并导致构建失败，这时候可以添加 `-DfailIfNoTests=false` 参数告诉 `maven-surefire-plugin` 即使没有任何测试也不要报错。
	
		mvn test -Dtest -DfailIfNoTests=false
	
	这种方式也可以用来运行那些不符合命名约定的测试类。

* 通过 `includes` `excludes` 标签

		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-surefire-plugin</artifactId>
			<version>2.12.4</version>
			<configuration>
				<skipTests>false</skipTests>
				<!-- 被执行的测试用例，无论用例是否满足命名约定 -->
				<includes>
					<!-- **表示任意层级的目录，*表示任意一级的目录或测试用例名中的通配符 -->
					<include>**/mybatis/*/*.java</include>
				</includes>
				<!-- 不被执行的测试用例 -->
				<excludes>
					<exclude>org/lgy/mybatis/b_transaction/TransactionTest.java</exclude>
				</excludes>
			</configuration>
		</plugin>