# `SELECT`

```sql
SELECT <列名>, ...
 FROM <表明>;
```

- 以上 `SELECT` 语句 包含 `SELECT` 和 `FROM` 两个子句（clause）

    ```sql
    SELECT product_id, product_name, purchase_price FROM Product;
    ```

- `SELECT` 子句中可以使用常数或者表达式
- 查询结果中列的顺序和 `SELECT` 字句中列的顺序相同
- `*` 表示全部的列
    - 使用 `*` 则无法设定列的显示顺序，此时会按执行 `CREATE TABLE` 语句时定义的顺序对列进行排序
- 为列设置别名

    ```sql
    SELECT product_id       AS id,
           product_name     AS name,
           purchase_price   AS price
      FROM Product;

    SELECT product_id       AS "商品编号",
           product_name     AS "商品名称",
           purchase_price   AS "进货单价"
      FROM Product;
    ```

    - **别名**需要用**双引号**括起来（不是单引号）
- 常数查询

    ```sql
    SELECT '商品' AS string, 38 AS number, '2009-02-24' AS date,
        product_id, product_name
    FROM Product;
    ```

    - SQL 语句中的字符串常数、日期常数要用单引号括起来
- 从结果中剔除重复行

    ```sql
    SELECT DISTINCT product_type FROM Product;
    ```

- 使用 `DISTINCT` 时， `NULL` 也被视为一类数据，`NULL` 存在于多行中时，也会被合并为一条数据

    ```sql
    SELECT DISTINCT purchase_price FROM Product;
    ```

- 多列之前使用 `DISTINCT`

    ```sql
    SELECT DISTINCT product_type, regist_date FROM Product;
    ```

    - 两列的数据都相同记录才会被合并为一条
- `DISTINCT` 关键字只能放在第一个列名前
    - 不能写成 `regist_date, DISTINCT product_type`
- `FROM` 字句并非必须

    ```sql
    SELECT (100 + 200) * 3 AS calculation;
    ```

    - 通过执行 `SELECT` 语句代替计算器的情况基本不存在
    - 极少数情况下，可以通过使用没有 `FROM` 子句的 `SELECT` 语句来实现某种业务，比如不管内容是什么，只希望得到一行临时数据
    - > Oracle 不允许省略 `SELECT` 语句中的 `FROM` 子句，可以使用 `DUAL` 临时表；DB2 中可以使用 `SYSIBM.SYSDUMMY1` 临时表
# `WHERE`

```sql
SELECT <列名>, ...
  FROM <表名>
 WHERE <条件表达式>;
```

- SQL 中子句的书写顺序是固定的，不能随意更改，否则会造成执行错误
    - `WHERE` 子句必须紧跟在 `FROM` 子句之后
- `WHERE` 字句中的条件表达式

    ```sql
    SELECT product_name, product_type
    FROM Product
    WHERE product_type = '衣服';

    -- 也可以不选取出作为查询条件的列
    SELECT product_name
      FROM Product
     WHERE product_type = '衣服';
    ```

- 首先通过 `WHERE` 子句查询出符合指定条件的记录，然后再选取出 `SELECT` 语句指定的列