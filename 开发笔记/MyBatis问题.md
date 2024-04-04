## 数据库类型对应Java实体类类型

出现Argument Type mismatch

| Type Name | Display Length | Database Type | Java Type | JDBC Type Index | 
|-----------|----------------|---------------|-----------|-----------------| 
| VARCHAR   | L+N            | VARCHAR       | String    | 12              | 
| CHAR      | N              | CHAR          | String    | 1               | 
| BLOB      | L+N            | BLOB          | byte[]    | -4              | 
| TEXT      | 65535          | VARCHAR       | String    | -1              | 
| INTEGER   | 4              | INTEGER UNSIGNED | Long    | 4               | 
| TINYINT   | 3              | TINYINT UNSIGNED | Integer | -6              | 
| SMALLINT  | 5              | SMALLINT UNSIGNED | Integer | 5              | 
| MEDIUMINT | 8              | MEDIUMINT UNSIGNED | Integer | 4             | 
| BIT       | 1              | BIT           | Boolean   | -7              | 
| BIGINT    | 20             | BIGINT UNSIGNED | BigInteger | -5           | 
| FLOAT     | 4+8            | FLOAT         | Float     | 7               | 
| DOUBLE    | 22             | DOUBLE        | Double    | 8               | 
| DECIMAL   | 11             | DECIMAL       | BigDecimal | 3              | 
| BOOLEAN   | 1              | Same as TINYINT |           |                 | 
| ID        | 11             | PK (INTEGER UNSIGNED) | Long | 4             | 
| DATE      | 10             | DATE          | Date      | 91              | 
| TIME      | 8              | TIME          | Time      | 92              | 
| DATETIME  | 19             | DATETIME      | Timestamp | 93              | 
| TIMESTAMP | 19             | TIMESTAMP     | Timestamp | 93              | 
| YEAR      | 4              | YEAR          | Date      | 91              |