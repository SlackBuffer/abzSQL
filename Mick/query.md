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
- 查询结果中列的顺序和 `SELECT` 子句中列的顺序相同
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
    /*
    string | number |    date    | product_id | product_name
    --------+--------+------------+------------+--------------
    商品   |     38 | 2009-02-24 | 0001       | T恤衫
    商品   |     38 | 2009-02-24 | 0002       | 打孔器
    商品   |     38 | 2009-02-24 | 0003       | 运动T恤
    商品   |     38 | 2009-02-24 | 0004       | 菜刀
    商品   |     38 | 2009-02-24 | 0005       | 高压锅
    商品   |     38 | 2009-02-24 | 0006       | 叉子
    商品   |     38 | 2009-02-24 | 0007       | 擦菜板
    商品   |     38 | 2009-02-24 | 0008       | 圆珠笔
    */
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

    - 多列的数据都相同记录才会被合并为一条
    - `DISTINCT` 关键字只能放在第一个列名前，不能写成 `regist_date, DISTINCT product_type`
- `FROM` 子句并非必须

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
- `WHERE` 子句中的条件表达式

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
# 视图（"Alias" 的执行结果）
- 从 SQL 的角度来看视图就是一张表
- 视图和表的区别只有一个，即“是否保存了实际的数据”
    - 创建表时会通过 `INSERT` 语句将数据保存到数据库中，数据库中的数据会被保存到计算机的存储设备
    - 使用视图时不会将数据保存到存储设备，也不会将数据保存到其他任何地方，视图保存的是 `SELECT` 语句
    - 表中存储的是**实际数据**，视图中保存的是从表中取出数据所使用的 `SELECT` 语句
- 从视图中读取数据时，视图会在内部执行该 `SELECT` 语句并创建出一张临时表
- 创建好视图后，只需在 `SELECT` 语句中进行调用，就可以方便地得到结果
- 视图中的数据会随着原表的变化**自动更新**
      - **视图归根到底就是 `SELECT` 语句**，所谓“参照视图”也就是“执行 `SELECT` 语句”的意思，因此可以保证数据的最新状态
      - 数据保存在表中时，必须显式地执行 SQL 更新语句才能对数据进行更新
- 创建视图

    ```sql
    CREATE VIEW 视图名称(<视图列名1>, <视图列名2>, ...)
    AS
    <SELECT语句>
    ```

    - `SELECT` 语句中列的排列顺序和视图中列的排列顺序相同
- `ProductSum` 视图

    ```sql
    CREATE VIEW ProductSum (product_type, cnt_product)
    AS
    SELECT product_type, COUNT(*)
      FROM Product
     GROUP BY product_type;

    SELECT product_type, cnt_product
      FROM ProductSum;
    ```

- 多重视图：以视图为基础创建视图

    ```sql
    CREATE VIEW ProductSumJim (product_type, cnt_product)
    AS
    SELECT product_type, cnt_product
      FROM ProductSum -- 视图
     WHERE product_type = '办公用品';

    SELECT product_type, cnt_product
      FROM ProductSumJim;
    ```

    - 尽量避免在视图的基础上创建视图。对多数 DBMS 来说，多重视图会降低 SQL 的性能
- 定义视图时不要使用 `ORDER BY` 子句
    - 视图和表一样，**数据行**都是没有顺序的
    - 有些 DBMS 在定义视图的语句中可以使用 `ORDER BY` 子句的，但这不是通用的语法
- 如果定义视图的 `SELECT` 语句能够满足某些条件，那么这个视图就可以被更新（对视图进行更新操作会反映到原表）
    1. `SELECT` 子句中未使用 `DISTINCT`
    2. `FROM` 子句中只有一张表
    3. 未使用 `GROUP BY` 子句
    4. 未使用 `HAVING` 子句
- 视图和表需要**同时更新**，因此通过汇总得到的视图无法进行更新
    - 使用视图（`ProductSum`）来保存原表的汇总结果时，是无法判断如何将视图的更改反映到原表中
        - `INSERT INTO ProductSum VALUES ('电器制品', 5);` 向表中添加商品种类为“电器制品”的 5 行数据，但是这些商品对应的商品编号、商品名称和销售单价等都无从得知，所以操作无法成功
    - 视图归根结底是从表派生出来的，因此如果原表可以更新，那么视图中的数据也可以更新。反之亦然，如果视图发生了改变，而原表没有进行相应更新的话，就无法保证数据的一致性
- 可以更新的视图

    ```sql
    CREATE VIEW ProductJim (product_id, product_name, product_type,  sale_price, purchase_price, regist_date)
    AS
    SELECT *
      FROM Product
     WHERE product_type = '办公用品';

    INSERT INTO ProductJim VALUES ('0009', '印章', '办公用品', 95, 10, '2009-11-30');
    ```

    - > 设置允许 PostgreSQL 对视图进行更新
    
    ```sql
    -- ERROR: 不能向视图中插入数据
    -- HINT: 需要一个无条件的ON INSERT DO INSTEAD规则
    CREATE OR REPLACE RULE insert_rule
    AS ON INSERT
    TO ProductJim DO INSTEAD
    INSERT INTO Product VALUES (
                new.product_id,
                new.product_name,
                new.product_type,
                new.sale_price,
                new.purchase_price,
                new.regist_date);
    ```
    
- 删除视图

    ```sql
    DROP VIEW ProductSum;
    DROP VIEW ProductSum CASCADE;
    ```

- 可以将常用的 `SELECT` 语句做成视图来使用
- 使用视图，可以轻松完成跨多表查询数据等复杂操作
# 子查询（IIFE）
- 子查询将用来定义视图的 `SELECT` 语句直接用于 `FROM` 子句中

    ```sql
    SELECT product_type, cnt_product
      FROM (SELECT product_type, COUNT(*) AS cnt_product
              FROM Product
             GROUP BY product_type) AS ProductSum;
    ```

    - 首先执行 `FROM` 子句中的 `SELECT` 语句，然后执行外层的 `SELECT` 语句
    - 子查询名称 `ProductSum` 是一次性的，不会像视图那样保存在存储介质中，`SELECT` 语句执行之后就消失
    - > Oracle 要省略 `AS` 关键字
- 作为内层查询的子查询会首先执行
- 多重嵌套

    ```sql
    SELECT product_type, cnt_product
      FROM (SELECT *
              FROM (SELECT product_type, COUNT(*) AS cnt_product
                      FROM Product
                     GROUP BY product_type) AS ProductSum
             WHERE cnt_product = 4) AS ProductSum2;
    ```

    - 随着子查询嵌套层数的增加，SQL 语句越发难读，性能也越来越差，尽量避免使用多层嵌套的子查询
- 原则上子查询必须设定名称，建议根据处理内容来设定名称
## 标量子查询（scalar subquery）
- 标量指单一
- 标量子查询必须且只能返回 1 行 1 列结果，即返回表中某一行某一列的值
- 由于返回的是单一的值，标量子查询的返回值可以用在 `=`, `<`, `>` 中
- 查询销售单价高于平均销售单价的商品

    ```sql
    -- 查询出销售单价高于平均销售单价的商品 --

    -- WHERE 子句中不能使用聚合函数！
    SELECT product_id, product＿name, sale_price
      FROM Product
     WHERE sale_price > AVG(sale_price);

    SELECT product_id, product_name, sale_price
      FROM Product
     WHERE sale_price > (SELECT AVG(sale_price)
                         FROM Product);
    ```

- 通常任何可以使用单一值的位置都可以使用标量子查询（**即能够使用常数或者列名的地方**），如 `SELECT` 子句，`GROUP BY` 子句，`HAVING` 子句，`ORDER BY` 子句

    ```sql
    -- SELECT
    SELECT product_id,
           product_name,
           sale_price,
           (SELECT AVG(sale_price)
              FROM Product) AS avg_price
      FROM Product;

    -- HAVING
    -- 选取出按照商品种类计算出的平均销售单价高于全部商品的平均销售单价的商品种类
    SELECT product_type, AVG(sale_price)
      FROM Product
     GROUP BY product_type
    HAVING AVG(sale_price) > (SELECT AVG(sale_price)
                                FROM Product);
    ```

# 关联子查询
- 在细分的组内进行比较时，需要使用关联子查询
- 选取各商品种类中高于该商品种类的平均销售单价的商品

    ```sql
    SELECT product_type, product_name, sale_price
      FROM Product AS P1 
     WHERE sale_price > (SELECT AVG(sale_price)
                           FROM Product AS P2 
                          WHERE P1.product_type = P2.product_type
                            GROUP BY product_type);
    ```

    - `WHERE P1.product_type = P2.product_type`: 在同一商品种类中对各商品的销售单价和该类商品的平均单价进行比较（限定“商品种类”对平均单价进行比较）
        - 没有此行，第二个 `SELECT` 语句会返回 3 行结果，即不是标量子查询，不能作比较
    - 首先需要计算各个商品种类中商品的平均销售单价，由于该单价会用来和商品表中的各条记录进行比较，因此关联子查询实际只能返回 1 会结果
    - 此处不使用 `GROUP BY` 语句也能得到正确结果，因为在 `WHERE` 子句中追加了`P1.product_type=P2.product_type` 这个条件，使得 `AVG` 函数按照商品种类进行了平均值计算
- 使用关联子查询时，需要在表所对应的列名之前加上表的**别名**，以 `<表名>.<列名>` 的形式记述
- 关联子查询和 `GROUP BY` 子句一样，也可以对表中的数据进行切分
![](src/关联子查询.jpg)
- 结合条件一定要写在子查询中
- 关联名称（如 `P1`, `P2`）的**作用域**
    - 子查询内部设定的关联名称，只能在该子查询内部使用；内部可以看到外部，而外部看不到内部
    ![](src/子查询关联名称作用域.jpg)
# 习题
5.1  
CREATE VIEW ViewPractice5_1
AS
SELECT product_name, sale_price, regist_date
  FROM Product
 WHERE sale_price >= 1000 AND regist_date='2009-09-20';

5.2 主键限制  

5.3  
SELECT product_id, product_name, product_type, sale_price, 
(SELECT AVG(sale_price)
    FROM Product) AS sale_price_all
FROM Product;

5.4  
SELECT product_id, product_name, product_type, sale_price, 
(SELECT AVG(sale_price)
   FROM Product AS P2 
  WHERE P1.product_type = P2.product_type
 GROUP BY product_type) AS avg_sale_price
FROM Product AS P1;