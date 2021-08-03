# Spark Doris Connector

Spark DorisDB Connector 可以支持通过 Spark 读取 DorisDB 中存储的数据。

- 当前版本只支持从`DorisDB`中读取数据。
- 可以将`DorisDB`表映射为`DataFrame`或者`RDD`，推荐使用`DataFrame`。
- 支持在`DorisDB`端完成数据过滤，减少数据传输量。

## 版本要求

| Connector | Spark  | Java | Scala |
| --------- | ----- | ---- | ----- |
| 1.0.0     | 2.x    | 8    | 2.11  |

### 使用示例

代码参考 "https://github.com/DorisDB/demo/tree/master/SparkDemo"

#### SQL

```sql
CREATE TEMPORARY VIEW spark_dorisdb
USING doris
OPTIONS(
  "table.identifier"="$YOUR_DORIS_DATABASE_NAME.$YOUR_DORIS_TABLE_NAME",
  "fenodes"="$YOUR_DORIS_FE_HOSTNAME:$YOUR_DORIS_FE_RESFUL_PORT",
  "user"="$YOUR_DORIS_USERNAME",
  "password"="$YOUR_DORIS_PASSWORD"
);

SELECT * FROM spark_dorisdb;
```

#### DataFrame

```scala
val dorisSparkDF = spark.read.format("doris")
  .option("doris.table.identifier", "$YOUR_DORIS_DATABASE_NAME.$YOUR_DORIS_TABLE_NAME")
 .option("doris.fenodes", "$YOUR_DORIS_FE_HOSTNAME:$YOUR_DORIS_FE_RESFUL_PORT")
  .option("user", "$YOUR_DORIS_USERNAME")
  .option("password", "$YOUR_DORIS_PASSWORD")
  .load()

dorisSparkDF.show(5)
```

#### RDD

```scala
import org.apache.doris.spark._
val dorisSparkRDD = sc.dorisRDD(
  tableIdentifier = Some("$YOUR_DORIS_DATABASE_NAME.$YOUR_DORIS_TABLE_NAME"),
  cfg = Some(Map(
    "doris.fenodes" -> "$YOUR_DORIS_FE_HOSTNAME:$YOUR_DORIS_FE_RESFUL_PORT",
    "doris.request.auth.user" -> "$YOUR_DORIS_USERNAME",
    "doris.request.auth.password" -> "$YOUR_DORIS_PASSWORD"
  ))
)

dorisSparkRDD.collect()
```

### 配置

#### 通用配置项

| Key                              | Default Value     | Comment                                                      |
| -------------------------------- | ----------------- | ------------------------------------------------------------ |
| doris.fenodes                    | --                | DorisDB FE http 地址，支持多个地址，使用逗号分隔            |
| doris.table.identifier           | --                | DorisDB 表名，如：db1.tbl1                                 |
| doris.request.retries            | 3                 | 向DorisDB发送请求的重试次数                                    |
| doris.request.connect.timeout.ms | 30000             | 向DorisDB发送请求的连接超时时间                                |
| doris.request.read.timeout.ms    | 30000             | 向DorisDB发送请求的读取超时时间                                |
| doris.request.query.timeout.s    | 3600              | 查询dorisDB的超时时间，默认值为1小时，-1表示无超时限制             |
| doris.request.tablet.size        | Integer.MAX_VALUE | 一个RDD Partition对应的DorisDB Tablet个数。此数值设置越小，则会生成越多的Partition。从而提升Spark侧的并行度，但同时会对DorisDB造成更大的压力。 |
| doris.batch.size                 | 1024              | 一次从BE读取数据的最大行数。增大此数值可减少Spark与Doris之间建立连接的次数。从而减轻网络延迟所带来的的额外时间开销。 |
| doris.exec.mem.limit             | 2147483648        | 单个查询的内存限制。默认为 2GB，单位为字节                      |
| doris.deserialize.arrow.async    | false             | 是否支持异步转换Arrow格式到spark-dorisdb-connector迭代所需的RowBatch                 |
| doris.deserialize.queue.size     | 64                | 异步转换Arrow格式的内部处理队列，当doris.deserialize.arrow.async为true时生效        |

#### SQL 和 Dataframe 专有配置

| Key                             | Default Value | Comment                                                      |
| ------------------------------- | ------------- | ------------------------------------------------------------ |
| user                            | --            | 访问DorisDB的用户名                                            |
| password                        | --            | 访问DorisDB的密码                                              |
| doris.filter.query.in.max.count | 100           | 谓词下推中，in表达式value列表元素最大数量。超过此数量，则in表达式条件过滤在Spark侧处理。 |

#### RDD 专有配置

| Key                         | Default Value | Comment                                                      |
| --------------------------- | ------------- | ------------------------------------------------------------ |
| doris.request.auth.user     | --            | 访问DorisDB的用户名                                            |
| doris.request.auth.password | --            | 访问DorisDB的密码                                              |
| doris.read.field            | --            | 读取DorisDB表的列名列表，多列之间使用逗号分隔                  |
| doris.filter.query          | --            | 过滤读取数据的表达式，此表达式透传给DorisDB。DorisDB使用此表达式完成源端数据过滤。 |

### DorisDB 和 Spark 列类型映射关系

| DorisDB Type | Spark Type                       |
| ---------- | -------------------------------- |
| NULL_TYPE  | DataTypes.NullType               |
| BOOLEAN    | DataTypes.BooleanType            |
| TINYINT    | DataTypes.ByteType               |
| SMALLINT   | DataTypes.ShortType              |
| INT        | DataTypes.IntegerType            |
| BIGINT     | DataTypes.LongType               |
| FLOAT      | DataTypes.FloatType              |
| DOUBLE     | DataTypes.DoubleType             |
| DATE       | DataTypes.StringType             |
| DATETIME   | DataTypes.StringType             |
| BINARY     | DataTypes.BinaryType             |
| DECIMAL    | DecimalType                      |
| CHAR       | DataTypes.StringType             |
| LARGEINT   | DataTypes.StringType             |
| VARCHAR    | DataTypes.StringType             |
| DECIMALV2  | DecimalType                      |
| TIME       | DataTypes.DoubleType             |
| HLL        | Unsupported datatype             |

- 注：Connector中，将`DATE`和`DATETIME`映射为`String`。由于`DorisDB`底层存储引擎处理逻辑，直接使用时间类型时，覆盖的时间范围无法满足需求。所以使用 `String` 类型直接返回对应的时间可读文本。
