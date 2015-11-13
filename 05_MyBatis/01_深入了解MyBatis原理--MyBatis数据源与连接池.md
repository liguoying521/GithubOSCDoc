## 《深入了解MyBatis原理 - MyBatis数据源与连接池》

### MyBatis DataSource 的分类

MyBatis数据源的实现在以下4个包中：

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/007.png?dir=0&filepath=007.png&oid=5c71ee49d624fa26de9a685e9ac6b219a798ae1c&sha=55c7f9ce9d4476a012ed148081b555dacaebd20a)

MyBatis 把数据源分成如下3类：

* JNDI
* POOLED
* UNPOLLED

相应的，MyBatis 内部定义了2种数据源实现：

* PooledDataSource
* UnpooledDataSource

**注意：**

> MyBatis内部没有定义 JNDI 对应的数据源实现，这是因为 JNDI 类型的数据源是从应用程序服务器获得的。

这里多说一点 DataSource、PooledDataSource、UnpooledDataSource 之间的的关系：

> PooledDataSource 和 UnpooledDataSource 都实现了 DataSource 接口。<br/>
> PooledDataSource 持有一个 UnpooledDataSource 的引用，当 PooledDataSource 需要创建一个 java.sql.Connection 实例对象时，还是通过 UnpooledDataSource 来创建的，但是 PooledDataSource 额外提供了一种连接缓存机制。

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/008.png?dir=0&filepath=008.png&oid=a57cefd454c9a02122386d9958c05e880578daa4&sha=9072a7f98808563104f7ccb30fc8089eaf9b54cf)

### MyBatis 解析 DataSource 配置信息的过程

在 MyBatis 的配置文件中，使用 `<dataSource>` 节点配置数据源：

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/009.png?dir=0&filepath=009.png&oid=e81650caf03dc369534479dc0fd2867ff31091c3&sha=a3e0042fd990089d159deab2e806a7f09ad2f4f3)

MyBatis 在 XMLConfigBuilder 中解析配置文件，在此期间会根据 `<dataSource>` 节点创建对应的 DataSource。

解析过程中的核心方法摘要：

> 1.从 `<configuration>` 节点开始，逐层解析

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/010.png?dir=0&filepath=010.png&oid=eaf2a224b2389f3675cd5b9cef34237aacb95d96&sha=a2a712dc0798803097d8dc6e14174163599ef8f2) 

> 2.解析 `<configuration>` 节点

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/011.png?dir=0&filepath=011.png&oid=01391e9e9239607675985c6facafab6924300306&sha=a2a712dc0798803097d8dc6e14174163599ef8f2)

> 3.解析 `<environments>` 节点

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/012.png?dir=0&filepath=012.png&oid=119b6d1a408c24d6a0675c13cf23f9d9d15f8d55&sha=a2a712dc0798803097d8dc6e14174163599ef8f2)

> 4.解析 `<dataSource>` 节点

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/013.png?dir=0&filepath=013.png&oid=09b9b8bd262c5eadfa0e9644e6f43c36a5babfa3&sha=f56cdcce9f01d90fd2c21e11be085c87f67932aa)

**说明**：

> `type` 属性可取的值有 `POOLED`、`UNPOOLED` 和 `JNDI` (不包含使用第三方数据源的情况)，这3个可取值实际上都是类型别名，它们分别对应 `JndiDataSourceFactory`、`PooledDataSourceFactory`、`UnpooledDataSourceFactory` 3种类型。<br/>这里要注意，`type` 属性指定的是数据源对应的工厂(DataSourceFactory)，而不是指定数据源本身(DataSource)，这一点在配置第三方数据源时需注意。<br/><br/>
> MyBatis 会根据 `type` 的取值创建相应的 DataSourceFactory，进而获得相应的 DataSource。

以下是 `Configuration` 类中注册与数据源相关的别名：

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/014.png?dir=0&filepath=014.png&oid=296e8ca3a717414b712db40a9f450ccffab8dc7c&sha=bed72c58f63f9d86f2f412487d63a2b8fba9331e)

### MyBatis 通过 DataSourceFactory 创建 DataSource 的过程

以下是 MyBatis 内置数据源工厂的 UML 图：

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/015.png?dir=0&filepath=015.png&oid=730f7a654c7d162b6459d3893306021306704428&sha=075d22d31097b91969f0672599ec7c640714a0ac)

`UnpooledDataSourceFactory` 和 `JndiDataSourceFactory` 实现了 `DataSourceFactory`接口，`PooledDataSourceFactory` 继承了 `UnpooledDataSourceFactory`。

`DataSourceFactory` 接口的定义如下：

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/016.png?dir=0&filepath=016.png&oid=bdad63e8ff73e321d7a6a269af89bda04857f99e&sha=b41f772111e9378c74e5b02624cda9396cce7e95)

下面分析一下 DataSourceFactory 创建 DataSource 的过程：

1. 根据 `dataSource` 节点的 `type` 属性的值创建工厂类的实例(内置别名针对内置数据源，全限定类名针对第三方数据源)

		DataSourceFactory factory = (DataSourceFactory) resolveClass(type).newInstance();
上面是通过反射创建工厂类的实例，调用的是无参构造器，以下是 `UnpooledDataSourceFactory` 的无参构造器：

		public UnpooledDataSourceFactory() {
    		this.dataSource = new UnpooledDataSource();
  		}
该无参构造器的实现很简单，仅仅 new 了一个 `UnpooledDataSource` 对象， `UnpooledDataSource` 的无参构造器的实现为空：
		
		public UnpooledDataSource() {
  		}
从上面的代码可以看出，工厂类仅仅创建了一个数据源对象，没有将任何数据库配置信息传入。

* 第一步中得到的 `DataSource` 没有包含任何数据源的配置信息，所以需要调用 `setProperties`方法为其注入配置信息：

		factory.setProperties(props);
至此，就能够通过 `DataSource` 获取 `Connection` 了。

* 拿到 `DataSourceFactory` 创建的 `DataSource` 需要调用其 `getDataSource` 方法：

		factory.getDataSource();

### UnpooledDataSource 分析

`UnpooledDataSource` 不会对创建的 `Connection` 连接进行缓存，每次调用 `getConnection` 方法都会返回一个新的 `Connection` 连接。

以下是对 `getConnection` 方法的分析：

    @Override
    public Connection getConnection() throws SQLException {
    	return doGetConnection(username, password);
    }
    
    @Override
    public Connection getConnection(String username, String password) throws SQLException {
    	return doGetConnection(username, password);
    }

`doGetConnection(String username, String password)` 方法：

    private Connection doGetConnection(String username, String password) throws SQLException {
    	Properties props = new Properties();
    	if (driverProperties != null) {
    	  props.putAll(driverProperties);
    	}
    	if (username != null) {
    	  props.setProperty("user", username);
    	}
    	if (password != null) {
    	  props.setProperty("password", password);
    	}
    	return doGetConnection(props);
    }

`doGetConnection(Properties properties)` 方法：

    private Connection doGetConnection(Properties properties) throws SQLException {
    	// 1.初始化驱动
    	// 判断当前数据源使用的驱动程序是否已被加载注册，如果没有则被加载注册
    	initializeDriver();
    	// 2.创建 Connection 对象
    	Connection connection = DriverManager.getConnection(url, properties);
    	// 3.配置 Connection 对象
    	// 设置是否自动提交 autoCommit 及事务隔离级别 isolationLevel
    	configureConnection(connection);
    	return connection;
    }

### PooledDataSource 分析

* **PooledConnction**

	MyBatis 的连接池中保存的并不是 `java.sql.Connection`，而是 `org.apache.ibatis.datasource.pooled.PooledConnection`。

	为了实现对数据库连接的重用——当我们调用 `connection.close()` 方法时不是真的关闭数据库连接，而是将数据库连接放入连接池，MyBatis 使用 JDK 动态代理为 Connection 创建了代理对象，我们知道 JDK 动态代理需要一个 InvocationHandler 的实现，PooledConneciton 就充当了 InvocationHandler 实现。

	PooledConnection的构造方法：
	![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/017.png?dir=0&filepath=017.png&oid=8785303a56978d397bc48fa8f11c06c23883d2fa&sha=bf6a8a9349790cfc002650e2a5a7a85c8c670cbb)

	InvocationHandler 的 invoke 方法：<br/>
	![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/018.png?dir=0&filepath=018.png&oid=cbea762da70616bb79f6cde3e9e6e563169b1972&sha=bf6a8a9349790cfc002650e2a5a7a85c8c670cbb)

* **PoolState**

	`PooledDataSource` 中使用了连接池来缓存数据库连接，MyBatis 使用 `PoolState` 来表示连接池。

	MyBatis 将连接池中的 PooledConnection 分为两种状态：空闲状态(idle)和活动状态(active)，这两种状态的 PooledConnection 对象分别被存储到 PoolState 容器内的 idleConnections 和 activeConnections 两个 List 集合中。

	* **idleConnections：**空闲状态的 PooledConnection 对象被放置到此集合中，表示当前闲置的没有被使用的 PooledConnection 的集合，调用 PooledConnection 的 getConnection 方法时，会优先从该集合中去 PooledConnection 对象；当用完一个 java.sql.Connection 对象时，MyBatis 会将其包装成 PooledConnection 对象放到此集合中。
	* **activeConnections：**活动状态的 PooledConnection 对象被放置到名为 activeConnections 的 List 集合中，表示当前正在被使用的 PooledConnection 的集合。