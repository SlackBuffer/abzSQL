- 数据的更新（`INSERT`, `DELETE`, `UPDATE`）是以**记录（行）**为基本单位进行
# `INSERT`

```sql
-- `DEFAULT <默认值>`
CREATE TABLE ProductIns
(product_id CHAR(4) NOT NULL,
 product_name VARCHAR(100) NOT NULL,
 product_type VARCHAR(32) NOT NULL,
 sale_price INTEGER DEFAULT 0,
 purchase_price INTEGER ,
 regist_date DATE ,
 PRIMARY KEY (product_id));
```

- `INSERT`

    ```sql
    INSERT INTO <表名> (列1, 列2, 列3, ...) VALUES (值1, 值2, 值3, ...);
    ```

- 插入一条数据

    ```sql
    INSERT INTO ProductIns (product_id, product_name, product_type, sale_price, purchase_price, regist_date) VALUES ('0001', 'T恤衫', '衣服', 1000, 500, '2009-09-20');
    ```

    - 将列名和值用逗号隔开，分别括在 `()` 内，这种形式称为**清单**
    - 列清单，值清单
- 对表进行全列 `INSERT` 时，可以省略表名后的列清单，`VALUES` 子句的值会默认按照从左到右的顺序赋给每一列
- 原则上，执行一次 `INSERT` 语句会插入一行数据
    - 很多 RDBMS 都支持一次插入多行数据，这样的功能称为多行 `INSERT`（multi row INSERT）

        ```sql
        -- 通常的INSERT
        INSERT INTO ProductIns VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
        INSERT INTO ProductIns VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL);
        INSERT INTO ProductIns VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');

        -- 多行INSERT （Oracle以外）
        INSERT INTO ProductIns VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11'),
        ('0003', '运动T恤', '衣服', 4000, 2800, NULL),
        ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');

        -- Oracle中的多行INSERT
        INSERT ALL INTO ProductIns VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11')
                   INTO ProductIns VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL)
                   INTO ProductIns VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20')
        SELECT * FROM DUAL;
        ```

        - `DUAL` 是 Oracle 特有（安装时的必选项）的一种临时表
            - 在书写没有参照表的 `SELECT` 语句时，写在 `FROM` 子句中的表。它并没有实际意义，也不保存任何数据，同时也不能作为 `INSERT` 和 `UPDATE` 的对象
- `INSERT` 语句中想给某一列赋予 `NULL` 值时，可以直接在 `VALUES` 子句的值清单中写入 `NULL`
    - 想要插入 `NULL` 的列一定不能设置 `NOT NULL` 约束，向设置了 `NOT NULL` 约束的列中插入 `NULL` 时，`INSERT` 语句会出错，导致数据插入失败，即希望通过该 `INSERT` 语句插入的数据无法正常插入到表中
- `SQL` 语句执行失败不会对表中数据造成影响
- 默认值的使用方法通常有显式和隐式两种

    ```sql
    -- 显式
    INSERT INTO ProductIns (product_id, product_name, product_type, sale_price, purchase_price, regist_date) VALUES ('0007', '擦菜板', '厨房用具', DEFAULT, 790, '2009-04-28');

    -- 隐式
    -- 列清单和值清单中都省略设定了默认值的列
    INSERT INTO ProductIns (product_id, product_name, product_type, purchase_price, regist_date) VALUES ('0007', '擦菜板', '厨房用具', 790, '2009-04-28');
    ```

- 省略了没有设定默认值的列，该列的值就会被设定为 `NULL`
    - 如果省略的是设置了 `NOT NULL` 约束的列，`INSERT` 语句会出错
- **从其他表复制数据**（`INSERT ... SELECT`）

    ```sql
    CREATE TABLE ProductCopy
    (product_id CHAR(4)         NOT NULL,
     product_name VARCHAR(100)  NOT NULL,
     product_type VARCHAR(32)   NOT NULL,
     sale_price INTEGER         ,
     purchase_price INTEGER     ,
     regist_date DATE           ,
     PRIMARY KEY (product_id));

    -- 将商品表中的数据复制到商品复制表中
    INSERT INTO ProductCopy (product_id, product_name, product_type, sale_price, purchase_price, regist_date)
    SELECT product_id, product_name, product_type, sale_price, purchase_price, regist_date
      FROM Product;
    ```

- `INSERT ... SELECT` 中 `SELECT` 语句也可以使用 `WHERE` 子句或者 `GROUP BY` 子句等

    ```sql
    -- 根据商品种类进行汇总的表；
    CREATE TABLE ProductType
    (product_type VARCHAR(32)       NOT NULL,
     sum_sale_price INTEGER         ,
     sum_purchase_price INTEGER     ,
     PRIMARY KEY (product_type));

    INSERT INTO ProductType (product_type, sum_sale_price, sum_purchase_price)
    SELECT product_type, SUM(sale_price), SUM(purchase_price)
      FROM Product
     GROUP BY product_type;
    ```

    - 指定 `ORDER BY` 子句没有任何意义，因为无法保证表内部记录的排列顺序
# 删除
- `DROP TABLE` 语句可以将表完全删除
- `DELETE` 语句会留下表（容器）而删除表中的全部数据
    - `DELETE FROM <表名>;`
- 清空表

    ```sql
    DELETE FROM Product;
    ```

- `DELETE` 语句的删除对象并不是表或者列，而是记录（行）
- 搜索型 `DELETE`

    ```sql
    DELETE FROM <表名>
     WHERE <条件>;
    ```

- `DELETE` 语句中不能使用 `GROUP BY`、`HAVING` 和 `ORDER BY` 三类子句，只能使用 `WHERE` 子句
    - `GROUP BY` 和 `HAVING` 是从表中选取数据时用来改变抽取数据形式的，而 `ORDER BY` 是用来指定取得结果显示顺序的，因此在删除表中数据时它们都起不到什么作用
- 标准 SQL 中用来从表中删除数据的只有 `DELETE` 语句。很多数据库产品中还存在另外一种被称为 `TRUNCATE` 的语句
    - OracleSQLServer, PostgreSQL, MySQL, DB2
- `TRUNCATE` (舍弃)

    ```sql
    TRUNCATE <表名>;
    ```

    - `TRUNCATE` 只能删除表中的全部数据，而不能通过 `WHERE` 子句指定条件来删除部分数据
    - 因为它不能具体地控制删除对象，其处理速度比 `DELETE` 快得多
    - `实际上，DELETE` 语句在 DML 语句中也属于处理时间比较长的，因此需要删除全部数据行时，使用 `TRUNCATE` 可以缩短执行时间
    - > 在 Oracle 中把 `TRUNCATE` 定义为 DDL 而非 DML，因此，Oracle 中的 `TRUNCATE` 不能使用 `ROLLBACK`，执行 `TRUNCATE` 的同时会默认执行 `COMMIT` 操作