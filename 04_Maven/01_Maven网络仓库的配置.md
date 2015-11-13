## Maven网络仓库的配置

<a name="catagory"></a>
### 目录
* [Maven中央仓库](#maven_central_repo)
* [在 pom.xml 中配置 repository](#pom)
* [在 settings.xml 中配置 repository](#settings)

<a name="maven_central_repo"></a>
### Maven中央仓库（[返回目录](#catagory)）

Maven有一个默认的中央仓库，当Maven无法从本地仓库中找到需要的组件时，就好自动从默认中央仓库将组件下载至本地。

Maven的默认中央仓库配置在其超级POM中，该文件位于 `{MAVEN_HOME}/lib/maven-model-builder-x.x.x.jar/org/apache/maven/model/pom-4.0.0.xml`，我们可以从该文件中找到如下有关中央仓库的配置：

	<repositories>
		<repository>
			<!-- 中央仓库的id -->
			<id>central</id>
			<name>Central Repository</name>
			<!-- 中央仓库的url -->
			<url>https://repo.maven.apache.org/maven2</url>
			<layout>default</layout>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>

如果项目的 pom 文件中没有配置 repository ，那么 Maven 将从 central repository 中下载构件；反之，如果配置了 repository，那么 Maven 将从我们配置的 repository 中下载构件。

<a name="pom"></a>
### 在 pom.xml 中配置 repository（[返回目录](#catagory)）

如果由于各种原因导致我们无法使用 central repository（无法连接外网、使用公司内部仓库等），我们就必须在 pom.xml 中配置能够使用的 repository。

	<project ...>
		... ...
		<!-- Repository -->
		<repositories>
			<!-- OSC Repository -->
			<repository>
				<id>OSCRepo</id>
				<name>OSC Repository</name>
				<url>http://maven.oschina.net/content/groups/publics/</url>
				<releases>
					<enabled>true</enabled>
				</releases>
				<snapshots>
					<enabled>false</enabled>
				</snapshots>
			</repository>
			<!-- Jboss Repository -->
			<repository>
				<id>JbossRepo</id>
				<name>Jboss Repository</name>
				<url>http://repository.jboss.org/nexus/content/groups/public-jboss/</url>
				<releases>
					<enabled>true</enabled>
				</releases>
				<snapshots>
					<enabled>false</enabled>
				</snapshots>
			</repository>
		</repositories>
	</project>

一旦我们在 pom.xml 中配置了 repository，Maven 将优先从我们配置的 repository 中下载构件，而不是优先从 central repository 中下载。

如果我们配置了多个 repository，那么 Maven 将自动选择第一个可用的 repository 进行下载，也就是说，如果第一个可用，则 从第一个下载，否则从下一个可用的下载。

如果我们配置的所有 repository 都不可用，Maven 将使用 central repository 进行下载。

<a name="settings"></a>
### 在 settings.xml 中配置 repository（[返回目录](#catagory)）

在 pom.mxl 中配置的 repository 仅对所属项目有效，如果我们希望配置对所有项目都有效的 repository，可以在 settings.xml 中进行配置。

	<settings>
		<profiles>
			<profile>
				<id>Repository</id>
				<repositories>
					<!-- OSC Repository -->
					<repository>
						<id>OSCRepo</id>
						<name>OSC Repository</name>
						<url>http://maven.oschina.net/content/groups/public/</url>
						<releases>
							<enabled>true</enabled>
						</releases>
						<snapshots>
							<enabled>false</enabled>
						</snapshots>
					</repository>
					<!-- Jboss Repository -->
					<repository>
						<id>JbossRepo</id>
						<name>Jboss Repository</name>
						<url>http://repository.jboss.org/nexus/content/groups/public-jboss/</url>
						<releases>
							<enabled>true</enabled>
						</releases>
						<snapshots>
							<enabled>false</enabled>
						</snapshots>
					</repository>
				</repositories>
			</profile>
		</profiles>
		
		<activeProfiles>
			<activeProfile>Repository</activeProfile>
		</activeProfiles>
	</settings>