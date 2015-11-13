## MySql 字符集支持

MySQL的字符集支持(Character Set Support)有两个方面：字符集(Character set)和排序方式(Collation)。

### 基本概念

**字符（Character）**

>字符(Character)是指人类语言中最小的表义符号，例如'A'、'B'等。

**字符的编码（Encoding）**

>计算机只能识别 `01` 代码，要想把字符存储在计算机中就必须将字符用 `01` 代码的形式表示出来。
>
>对给定的一系列字符，为每个字符赋予一个由01组成的唯一数值，用此数值来代表对应的字符，这一数值就是字符的编码(Encoding)，例如，我们给字符'A'赋予数值000，给字符'B'赋予数值001，则0000就是字符'A'的编码。

**字符集（Character Set）**

>给定一系列字符并赋予对应的编码后，所有这些字符和编码对组成的集合就是字符集(Character Set)。

**字符序（Collation）**

>字符序(Collation)是指在同一字符集内字符之间的比较排序规则。
>
>确定字符序后，才能在一个字符集上定义什么是等价的字符，以及字符之间的大小关系。

>每个字符序唯一对应一种字符集，但一个字符集可以对应多种字符序，其中有一个是默认字符序(Default Collation)。

### MySQL 字符集

MySQL 对字符集的支持可以细化的4个层次：

&nbsp;&nbsp;&nbsp;&nbsp;服务器（Server）、数据库（Database）、表（Table）、列（Column）

1. 编译MySQL 时，指定了一个默认的字符集，这个字符集是 latin1
2. 安装MySQL 时，可以在配置文件(my.ini)中指定默认的字符集（character_set_server），如果没指定，这个值将继承自编译时指定的值
3. 启动 mysqld（MySQL服务） 时，可以在命令行参数中为系统变量 `character_set_server` 指定字符集，如果没指定，该变量将继承自配置文件（my.ini）中的配置，此时 `character_set_server` 被设定为这个默认的字符集
4. 当创建一个新的数据库时，除非明确指定该数据库使用的字符集（`character_set_database` 变量），否则这个数据库的字符集被缺省设定为 `character_set_server` 的值
5. 在这个数据库里创建一张表时，如果没有明确指定表使用的字符集，表默认的字符集被设定为 `character_set_database`，也就是这个数据库默认的字符集
6. 当在表内设置一栏时，除非明确指定，否则此栏缺省的字符集就是表默认的字符集

查看 MySQL 拥有的字符集可以使用以下命令：

	show charset;

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/022.png/?dir=0&filepath=022.png&oid=da7859f75f59d2b468fdf58b0f3d8d5bef174077&sha=e40b21b2f1f6d12fa16018b724fefb0873c3bd33)

其中，Charset 表示名称，Description 表示描述，Default collation 表示默认字符序，Maxlen 表示存储一个字节最多需要的字节数。

查看 MySQL 当前使用的字符集可以使用以下命令：

	show variables like 'character%';

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/023.png/?dir=0&filepath=023.png&oid=f1899431832d6d5b2fbd7305fc9421c68664c629&sha=03b9290607d85a01a74b363341e36f528ef3fbb0)

* character\_set\_client
	>客户端来源数据使用的字符集

* character\_set\_connection
	>连接层字符集

* character\_set\_database
	>当前选中数据库的默认字符集

* character\_set\_results
	>查询结果使用的字符集

* character\_set\_server
	>MySQL 服务默认的内部操作字符集

* character\_set\_system
	>系统元数据(字段名等)字符集

MySQL中的字符集转换过程如下：

1. MySQL Server 收到请求时将请求数据从 `character_set_client` 转换为 `character_set_connection`
2. 进行内部操作前将请求数据从 `character_set_connection` 转换为内部操作字符集，内部操作字符集的确定方法如下： 
	* 使用每个数据字段的CHARACTER SET设定值； 
	* 若上述值不存在，则使用对应数据表的DEFAULT CHARACTER SET设定值(MySQL扩展，非SQL标准)； 
	* 若上述值不存在，则使用对应数据库的DEFAULT CHARACTER SET设定值； 
	* 若上述值不存在，则使用 `character_set_server` 设定值。 
3. 将操作结果从内部操作字符集转换为 `character_set_results`，返回客户端。

修改 MySQL 使用的字符集可以使用以下命令：

	# 设置与客户端有关的字符集，有无引导都可
	set character_set_client = 'utf8';
	set character_set_connection = utf8;
	set character_set_results = utf8;
	# 一次设置上面3个与客户端相关的字符集，有无引导都可
	set names latin1;
	set names 'utf8';
	# 不能使用 = 这种形式
	# set names = 'utf8';
	# 设置 MySQL 服务默认使用的字符集
	set character_set_server = utf8;
	# 设置当前数据库默认使用的字符集
	set character_set_database = utf8;

修改 my.ini 文件修改 MySQL Server 默认使用的字符集：

	[mysqld]
	character_set_server = utf8

关于 `character_set_client`、`character_set_results`：

>我们使用客户端访问时一般会直接使用 `set names 'utf8'`，这样 client 和 result 就会同时为utf8，不会出现什么问题。
>
>但如果 `character_set_client` 和 `character_set_result` 不相同的话会很容易出现乱码，在一次操作中，这2个变量都是指客户端使用的编码，那为什么要使用2个变量分开设置呢？？

>这是因为 `character_set_client` 变量并不作为接收显示时使用的字符集，只有 `character_set_results` 才作为显示时使用的字符集，所以需要将其拆成两个变量。

关于 `character_set_connection` ：

>客户端查询时：client => connection => table/database，
>
>返回数据时：results <= connection <= table/database

>设置 connection 为任何支持传输的数据的字符集都可以，不一定要跟 client 和 database 保持一致。

>大部分情况下，其实不需要 connetion 这个设置，除非是为了降低网络传输压力，将其设置为占用字节较少的字符集。

### MySQL 字符序

查看 MySQL 中有哪些 Collation 可以使用以下命令：

	show collation;

![](http://git.oschina.net/liguoying/GithubOSCImages/raw/master/24.png/?dir=0&filepath=24.png&oid=8aea20f2d33172242bf317e4cc5222760a32bc04&sha=4e299995fd553109bd501b3d43aff419500e6d06)

MySQL Collation 的命令规则：

>命名规则可以分为以下2类：
>
>* <character set\>\_<language/other>\_<ci/cs>
>* <character set\>_bin

>ci：case insensitive（大小写不敏感）<br/>
>cs：case sensitive（大小写敏感）<br/>
>bin：binary（以二进制比较）

MySQL 对 Collation 的支持可以细分到3个层次：

&nbsp;&nbsp;&nbsp;&nbsp;数据库（Database）、表（Table）、列（Column）

创建 Database 时，如果同时指定了 char set 和 collate，则使用指定的 char set 和 collate;<br/>
如果只指定了 char set，没有指定 collate，则使用指定的 char set 和 该字符集默认的 collate;<br/>
如果既没指定 char set 又没指定 collate，则使用 `character_set_server` 变量指定的字符集和该字符集默认的 collate。

创建数据库时指定 collate：

	# 创建 database，使用 server 默认的编码及校对方式
	create database test01 char set latin1 collate latin1_bin;
	create database test02 default char set = ucs2 default collate = ucs2_bin;

创建表时的情况跟创建数据库时类似，如果明确指定了，则使用指定的，否则从数据库继承。

创建表时指定 collate：

	# 使用指定的 char set 及 collate，覆盖 database 默认的
	create table test03_tbl (
		col01 varchar(10)
	) char set latin1 collate latin1_bin;

定义列时的情况跟创建表时类似，如果明确指定了，则使用指定的，否则从表继承。

定义列时指定 collate：

	create table test02_tbl (
		col01 varchar(10) char set utf8 collate utf8_bin
	);

修改 collate:

	# 修改 table 级别的 char set 和 collate 会导致其列的 char set 和 collate 也被修改
	alter table test03_tbl convert to char set ucs2 collate ucs2_bin;
	# 修改列的 char set 和 collate
	alter table test03_tbl modify column col01 varchar(10) char set ucs2 collate ucs2_bin;