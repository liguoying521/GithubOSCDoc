<style>
	td{text-align:center; font-size: 12px;}
</style>

<a name="0"></a>
## 目录
* [CHAR、VARCHAR 和 LONGVARCHAR](#a)
* [TINYINT、SMALLINT、INTEGER 和 BIGINT](#b)
* [REAL、DOUBLE 和 FLOAT](#c)
* [DECIMAL 和 NUMERIC](#d)
* [DATE、TIME 和 TIMESTAMP](#e)
* [BIT](#f)
* [BINARY、VARBINARY 和 LONGVARBINARY](#g)

<a name="a"></a>
### CHAR、VARCHAR 和 LONGVARCHAR（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td rowspan="3">java.lang.String<br/><br/>char[]</td>
			<td>CHAR</td>
			<td>CHAR</td>
			<td>固定长度的小字符串</td>
		</tr>
		<tr>
			<td>VARCHAR</td>
			<td>VARCHAR</td>
			<td>可变长度的小字符串</td>
		</tr>
		<tr>
			<td>LONGVARCHAR</td>
			<td></td>
			<td>可变长度的大字符串</td>
		</tr>
	</tbody>
</table>

**CHAR**

>与 JDBC CHAR 对应的是 SQL CHAR 类型，其定义由 SQL-92 给出，且所有主要的数据库都支持它。
>
>它接受用于指定字符串最大长度的参数，例如 CHAR(12) 即定义了一个长度为 12 个字符的字符串，所有主要的数据库都支持长度达 254 个字符的 CHAR。

**VARCHAR**

>与 JDBC VARCHAR 对应的是 SQL VARCHAR 类型，其定义由 SQL-92 给出，且所有的主要数据库都支持它。
>
>它接受用于指定字符串最大长度的参数，例如 VARCHAR(12) 即定义了一个最大长度为 12 个字符的字符串。所有主要数据库都至少支持长度达 254 个字符的 VARCHAR。
>
>当把字符串的值赋给 VARCHAR 变量时，数据库就记住该字符串的长度，使用 SELECT 时，它可以返回准确的原始字符串。

**LONGVARCHAR**

>不幸的是，对于 JDBC LONGVARCHAR 类型，目前并没有一致的 SQL 映射。所有主要数据库都支持某种类型的长度可变的大字符串，这种字符串支持高达十亿位字节的数据，但 SQL 类型名称却变化多样。

Java 程序员不必区分 CHAR、VARCHAR 和 LONGVARCHAR 这三种类型的 JDBC 字符串。它们都可表示为 Java String，并且在不知道所需要的确切数据类型时也可正确读写 SQL 语句。

CHAR、VARCHAR 和 LONGVARCHAR 可映射为 String 或 char[]，但 String 更适合于一般用法。同时， String 类能使 String 和 char[] 之间的转换更为容易：它有一个用于将 String对象转换为 char[] 的方法，还有一个将 char[] 转换为 String 对象的构造函数。

方法 ResultSet.getString 用于分配和返回新的 String 对象。我们建议用它来从 CHAR、VARCHAR 和LONGVARCHAR 域中检索数据。它适用于检索普通的数据，但如果用 JDBC 类型LONGVARCHAR 来储存多个兆字节的字符串时，用它进行检索将显得十分笨拙。为此，ResultSet 接口中有两个方法可供程序员将 LONGVARCHAR 值作为 Java 输入流进行检索，之后可从该流中以任意大小的块来读取数据。这两个方法是：getAsciiStream 和 getUnicodeStream，它们将把储存在 LONGVARCHAR 列的数据作为 Ascii 或 Unicode 字符流来传送。

<a name="b"></a>
### TINYINT、SMALLINT、INTEGER 和 BIGINT（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>byte / short</td>
			<td>TINYINT</td>
			<td>TINYINT</td>
			<td>8 位无符号整数，其值在 0 到 255 之间</td>
		</tr>
		<tr>
			<td>short</td>
			<td>SMALLINT</td>
			<td>SMALLINT</td>
			<td> 16 位的有符号整数，其值在 -32768 和 32767 之间</td>
		</tr>
		<tr>
			<td>int</td>
			<td>INTEGER</td>
			<td>INTEGER</td>
			<td>32 位的有符号整数，其值在 - 2147483648 和 2147483647 之间</td>
		</tr>
		<tr>
			<td>long</td>
			<td>BIGINT</td>
			<td>BIGINT</td>
			<td>64 位的有符号整数，其值在 -9223372036854775808 和 9223372036854775807 之间</td>
		</tr>
	</tbody>
</table>

**TINYINT**

>JDBC 类型 TINYINT 代表一个 8 位无符号整数，其值在 0 到 255 之间。

>对应的 SQL 类型 TINYINT 目前只有一部份的数据库支持它。因此，可移植的代码也许宁愿用 JDBC SMALLINT 类型，这种类型已得到广泛支持。

>JDBC TINYINT 类型的 Java 映射的推荐类型是 Java byte 或 Java short。
>
>8 位的 Java byte 类型代表一个有符号的整数，其值在 -128 到 127 之间，因此对于大的 TINYINT 值它并非总合适，而 16 位的 Java short 类型却总能存储所有的 TINYINT 值。

**SMALLINT**

>JDBC 类型 SMALLINT 代表一个 16 位的有符号整数，其值在 -32768 和 32767 之间。

>对应的 SQL 类型 SMALLINT，其定义由 SQL- 92 给出，并为所有主流数据库所支持。SQL-92 标准将 SMALLINT 的精度留给实现去决定。但事实上，所有的主流数据库都至少支持 16 位。

>JDBC SMALLINT 类型的 Java 映射的推荐类型是 Java short 类型。

**INTEGER**

>JDBC 类型 INTEGER 代表一个 32 位的有符号整数，其值在 - 2147483648 和 2147483647 之间。

>对应的 SQL 类型 INTEGER，其定义由 SQL- 92 给出，并为所有主流数据库所广为支持。SQL-92 标准将 INTEGER 的精度留给实现去决定。但事实上，所有的主流数据库都至少支持 32 位。

>INTEGER 类型 Java 映射的推荐类型是 Java int 类型。

**BIGINT**

>JDBC 类型 BIGINT 代表一个 64 位的有符号整数，其值在 -9223372036854775808 和 9223372036854775807 之间。

>对应的 SQL 类型 BIGINT 是 SQL 的一个非标准扩展。事实上，目前还没有任何数据库实现 SQL BIGINT 类型。我们建议在可移植的代码中避免使用该类型。

>BIGINT 类型的 Java 映射的推荐类型是 Java long 类型。

<a name="c"></a>
### REAL、DOUBLE 和 FLOAT（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>float</td>
			<td>REAL</td>
			<td>REAL</td>
			<td>有 7 位尾数的“单精度”浮点数</td>
		</tr>
		<tr>
			<td>double</td>
			<td>DOUBLE</td>
			<td>DOUBLEPRECISION</td>
			<td>有 15 位尾数的“双精度”浮点数</td>
		</tr>
		<tr>
			<td>double</td>
			<td>FLOAT</td>
			<td>FLOAT</td>
			<td>同 DOUBLE</td>
		</tr>
	</tbody>
</table>

**REAL**

>JDBC 类型 REAL 代表一个有 7 位尾数的“单精度”浮点数。

>对应的 SQL 类型 REAL，其定义由 SQL- 92 给出。虽然未得到普遍支持，但在主流数据库中却已得到广泛支持。SQL-92 标准将 REAL 的精度留给实现去决定。但事实上，所有的支持 REAL类型的主流数据库都支持至少 7 位数的尾数精度。

>REAL 类型的 Java 映射的推荐类型为 Java float 类型。

**DOUBLE**

>JDBC 类型 DOUBLE 代表一个有 15 位尾数的“双精度”浮点数。

>对应的 SQL 类型是 DOUBLE PRECISION，其定义由 SQL- 92 给出，并为主流数据库所广为支持。SQL-92 标准将 DOUBLE PRECISION 的精度留给实现去决定。但事实上，所有支持 DOUBLEPRECISION 类型的主流数据库都支持至少 15 位数的尾数精度。

>DOUBLE 类型的 Java 映射的推荐类型为 Java double 类型。

**FLOAT**

>JDBC 类型 FLOAT 基本上与 JDBC 类型 DOUBLE 相同。我们同时提供了 FLOAT 和 DOUBLE，其目的是与以前的 API 实现一致。但这却有可能产生误导。FLOAT 代表一个有 15 位尾数的“双精度”浮点数。

>对应的 SQL 类型 FLOAT，其定义由 SQL-92 给出。SQL-92 标准将 FLOAT 的精度留给实现去决定。但事实上，所有支持 FLOAT 类型的主流数据库都支持至少 15 位数的尾数精度。

>FLOAT 类型的 Java 映射的推荐类型为 Java double 类型。然而，由于 SQL FLOAT 和单精度的 Java float类型间可能产生混淆，因此建议 JDBC 程序员通常选用 JDBC DOUBLE 类型而不选用 FLOAT。

<a name="d"></a>
### DECIMAL 和 NUMERIC（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td rowspan="2">java.math.BigDecimal</td>
			<td>DECIMAL</td>
			<td>DECIMAL</td>
			<td>带固定精度和小数位数的数值数据类型</td>
		</tr>
		<tr>
			<td>NUMERIC</td>
			<td>NUMERIC</td>
			<td>带固定精度和小数位数的数值数据类型</td>
		</tr>
	</tbody>
</table>

JDBC 类型 DECIMAL 和 NUMERIC 两者非常相似。它们都表示固定精度的十进制值。

相应的 SQL 类型 DECIMAL 和 NUMERIC，其定义在 SQL-92 中给出，并得到广泛支持。这些 SQL 类型都带有精度和比例参数。精度是所支持的十进制数字的总位数，比例是小数点后的数字位数。比例必须永远小于或等于精度。例如，值 "12.345" 有 5 位精度和 3 位比例，而值 ".11" 有 2 位精度和 2 位比例。JDBC 要求所有 DECIMAL 和 NUMERIC 类型都必须支持至少 15 位的精度和比例。

DECIMAL 和 NUMERIC 之间的唯一区别是 SQL-92 规范要求 NUMERIC 类型必须以确切指定的精度来表示，而对 DECIMAL 类型，它允许实现在创建该类型时所指定的精度以外再添加额外的精度。因此，创建为类型 NUMERIC(12,4) 的列将总是用 12 位数来表示，而创建为类型 DECIMAL(12,4) 的列则可用更大的位数来表示。

DECIMAL 和 NUMERIC 类型的 Java 映射的推荐类型是 java.math.BigDecimal，该 Java 类型也用绝对精度来表示定点数。java.math.BigDecimal 类型提供了一些数学操作，可对BigDecimal 类型与其它的 BigDecimal 类型、整数类型和浮点数类型进行加、减、乘、除的运算。

用于检索 DECIMAL 和 NUMERIC 值的推荐方法是 ResultSet.getBigDecimal。JDBC 还允许将这些 SQL 类型作为简单的 Strings 或 char 数组来访问。因此，Java 程序员可用getString 来检索 DECIMAL 或 NUMERIC 结果。然而，这将使常见的用 DECIMAL 或 NUMERIC 来表示的货币值变得极为尴尬，因为它意味着应用程序编程人员必须对字符串进行数学运算。当然，也可将这些 SQL 类型作为 Java 数值型类型来检索。

<a name="e"></a>
### DATE、TIME 和 TIMESTAMP（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>java.sql.Date</td>
			<td>DATE</td>
			<td>DATE</td>
			<td>由年、月、日组成的日期</td>
		</tr>
		<tr>
			<td>java.sql.Time</td>
			<td>TIME</td>
			<td>TIME</td>
			<td>由小时、分钟和秒组成的时间</td>
		</tr>
		<tr>
			<td>java.sql.Timestamp</td>
			<td>TIMESTAMP</td>
			<td>TIMESTAMP</td>
			<td> DATE 加上 TIME，外加一个纳秒域</td>
		</tr>
	</tbody>
</table>

有三种 JDBC 类型与时间有关：

* JDBC DATE 类型表示一个由年、月、日组成的日期。对应的是 SQL DATE 类型，其定义由 SQL-92 给出，但只有一部份主流数据库实现它。某些数据库提供了另外一些支持类似语义的 SQL 类型。
* JDBC TIME 类型表示一个由小时、分钟和秒组成的时间。对应的是 SQL TIME 类型，其定义由 SQL-92 给出，但只有一部份主流数据库实现它。与 DATE 一样，某些数据库提供了另外一些支持类似语义的 SQL 类型。
* JDBC TIMESTAMP 类型表示 DATE 加上 TIME，外加一个纳秒域。对应的 TIMESTAMP 类型，其定义由 SQL-92 给出，但只有少数几个数据库实现它。

由于标准的 Java 类 java.util.Date 并不与这三个 JDBC 日期—时间类型完全匹配（它含有 DATE 和 TIME 的信息但不含纳秒信息），因此 JDBC 定义了三个 java.util.Date 的子类与 SQL 类型对应。它们是：

* java.sql.Date，对应于 SQL DATE 信息。java.util.Date 基本类中的小时、分钟和秒都设为 0。
* java.sql.Time，对应于 SQL TIME 信息。java.util.Date 基本类中的年、月、日域设为 1970 年 1 月 1 日。这是 Java 纪元的“零”日期。
* java.sql.Timestamp，对应于 SQL TIMESTAMP 信息。该类扩展了 java.util.Date，添加了纳秒域。

所有这三个与时间有关的 JDBC 类都是 java.util.Date 的子类，因此它们可用在任何可以使用 java.util.Date 的地方。例如，国际化 (internationalization) 方法将 java.util.Date 对象用作变量，因此可将这三个与时间有关的 JDBC 类中任何一个的实例作为参数传给国际化方法。

JDBC Timestamp 对象除了具有其父类的日期和时间成份外，还有一个独立的纳秒组件。如果将 java.sql.Timestamp 对象用于需要 java.util.Date 对象的地方，则纳秒组件将丢失。但由于是以毫秒的精度来储存 java.util.Date 对象的，因此将 java.sql.Timestamp 对象转换为 java.util.Date 对象时可以保持这样的精度。这可通过将纳秒组件中的纳秒转换为毫秒（用纳秒数除以 1,000,000）并将之添到 java.util.Date 对象中来实现。转换中可能丢失高达 999,999 纳秒，但所产生的 java.util.Date 对象将可精确到毫秒以内。

下述代码段将 java.sql.Timestamp 对象转换为精度达到毫秒量级的 java.util.Date 对象：

    Timestamp t = new Timestamp(100, 0, 1, 15, 45, 29, 987245732);
    java.util.Date d;
    d = new java.util.Date(t.getTime() + (t.getNanos() / 1000000));

<a name="f"></a>
### BIT（[返回目录](#0)）

<table style="width: 100%;">
	<thead>
		<tr>
			<th>Java类型</th>
			<th>JDBC类型</th>
			<th>标准SQL类型</th>
			<th>说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>boolean</td>
			<td>BIT</td>
			<td>BIT</td>
			<td> 代表一个位值，可为 0 或 1</td>
		</tr>
	</tbody>
</table>

JDBC 类型 BIT 代表一个位值，可为 0 或 1。SQL-92 定义了 SQL BIT 类型。但与 JDBC BIT 类型不同，这种 SQL-92 BIT 类型带参数，用于定义固定长度的二进制字符串。幸运的是，SQL-92 也允许用简单的非参数化的 BIT 类型来代表单个的二进制数字。这种用法对应于 JDBC BIT 类型。不幸的是，SQL-92 BIT 类型只有在 “完全” SQL-92 中才要求，且目前只有一部份主流数据库支持它。因此，可移植的代码也许宁愿用 JDBC SMALLINT 类型，这种类型已得到广泛支持。

JDBC BIT 类型的 Java 映射的推荐类型是 Java 布尔型。

<a name="g"></a>
### BINARY、VARBINARY 和 LONGVARBINARY（[返回目录](#0)）

JDBC 类型 BINARY、VARBINARY 和 LONGVARBINARY 密切相关。BINARY 表示固定长度的小二进制值， VARBINARY 表示长度可变化的小二进制值，而 LONGVARBINARY 表示长度可变化的大二进制值。

不幸的是，这些不同 BINARY 类型的使用还未被标准化，因而在各种主要数据库提供的支持有很大的不同。

对应于 JDBC BINARY 类型的 SQL BINARY 类型，是一种非标准的 SQL 扩展，只在某些数据库上才实现。它接受用于指定二进制字节数的参数。例如，BINARY(12) 即定义了一个长度为 12 个字节的 binary 类型。通常，BINARY 值被限定在 254 个字节以内。

对应于 JDBC VARBINARY 类型的 SQL VARBINARY 类型，是一种非标准的 SQL 扩展，只在某些数据库上才实现。它接受用于指定二进制字节最大数的参数。例如，VARBINARY(12) 即定义了一个长度最大可为 12 个字节的二进制类型。通常，VARBINARY 的值被限定在 254 个字节以内。当把二进制的值赋给 VARBINARY 变量时，数据库就记住这个所赋值的长度，调用 SELECT时，它返回准确的原始值。

遗憾的是，目前还没有一致的 SQL 类型名称与 JDBC LONGVARBINARY 类型相对应。所有主要数据库都支持某种类型的长度可变的大二进制类型，它可支持高达十亿个字节的数据，但 SQL 类型名称却变化多样。

在 Java 中，BINARY、VARBINARY 和 LONGVARBINARY 都可用同一 byte数组来表示。由于可在不知道所需的确切 BINARY 数据类型的情况下正确地读写 SQL 语句，因此，Java 程序员无需区分它们。

检索 BINARY 和 VARBINARY 值时，我们建议使用 ResultSet.getBytes。然而，如果类型为 JDBC LONGVARBINARY 的某列储存的是几兆字节长度的字节数组，则建议用方法getBinaryStream 来检索。与 LONGVARCHAR 的情形类似，该方法可以使 Java 程序员将 LONGVARBINARY 值作为 Java 输入流检索，然后可从该流中以更小的块来读取。