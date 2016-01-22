<a name="catagory"></a>
## 目录
* [Maven中央仓库](#maven_central_repo)
* [在 pom.xml 中配置 repository](#pom)
* [在 settings.xml 中配置 repository](#settings)
* [mirror](#mirror)

<a name="maven_central_repo"></a>
### Maven中央仓库（[返回目录](#catagory)）

Maven有一个默认的中央仓库，当Maven无法从本地仓库中找到需要的组件时，就会自动从中央仓库将组件下载至本地。

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

如果没有在 `pom.xml` 或 `settings.xml` 中配置 repository ，那么 Maven 将从 central repository 中下载构件；反之，如果配置了 repository，那么 Maven 将从我们配置的 repository 中下载构件。

在 Maven 中有2个地方可以配置 Repository，一个是 `pom.xml`，另一个是 `settings.xml`，下面的内容会分别介绍这2种配置方式。

<a name="pom"></a>
### 在 `pom.xml` 中配置 repository（[返回目录](#catagory)）

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
			<!-- 以下是公司配置的2个仓库 -->
			<repository>
	            <id>Alfresco Maven Repository</id>
	            <url>https://maven.alfresco.com/nexus/content/groups/public/</url>
	        </repository>
	        <repository>
	            <id>sourceforge</id>
	            <url>http://oss.sonatype.org/content/groups/sourceforge/</url>
	        </repository>
		</repositories>
	</project>

一旦我们在 `pom.xml` 中配置了 Repository，Maven 将优先从我们配置的 Repository 中下载构件，而不是优先从 Central Repository 中下载。

如果我们配置了多个 Repository，那么 Maven 将自动选择第一个可用的 Repository 进行下载，也就是说，如果第一个可用，则从第一个下载，否则从下一个可用的下载。

如果我们配置的所有 Repository 都不可用，Maven 将使用 Central Repository 进行下载。

<a name="settings"></a>
### 在 `settings.xml` 中配置 repository（[返回目录](#catagory)）

在 `pom.mxl` 中配置的 Repository 仅对所属项目有效，如果我们希望配置的 Repository 对所有项目都有效，可以在 `settings.xml` 中进行配置。

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

<a name="mirror"/>
### mirror（[返回目录](#catagory)）

mirror 实际上也是一个远程仓库， mirror 是为现有的 repository 指定的，如果我们为某个 repository 指定了 mirror，那么所有向该 repository 发出的请求都会被转发给对应的 mirror。

mirror 的指定是在 `settings.xml` 中的：

	<settings>
	...
	  <mirrors>
	    <mirror>
	      <id>OSCMirrorForCentral</id>
	      <name>OSC Repository</name>
		  <!-- 一个真实的远程仓库 -->
	      <url>http://maven.oschina.net/content/groups/public/</url>
		  <!-- 一个已经存在的 repository 的 id -->
	      <mirrorOf>central</mirrorOf>
	    </mirror>
		<mirror>  
	      <id>my-org-repo</id>  
	      <name>Repository in My Orgnization</name>  
	      <url>http://192.168.1.100/maven2</url>  
	      <mirrorOf>*</mirrorOf>  
	    </mirror>
	  </mirrors>
	...
	</settings>