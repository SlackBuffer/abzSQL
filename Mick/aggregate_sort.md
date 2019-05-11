# 聚合
- 聚合：将多行汇总成一行
- 聚合函数 - 用于汇总的函数
    - `COUNT`
    - `SUM`
    - `AVG`
    - `MAX`
    - `MIN`
- **除 `COUNT` 外的聚合函数会把 `NULL` 数据排除在外且不能使用 `*`**
- `SUM`, `AVG` 只适用于数值类型的列，`MAX`, `MIN` 原则上可以适用于任何数据类型的列
- 聚合函数的参数中使用 `DISTINCT` 可以删除重复数据
- 只有 `SELECT` 子句，`HAVING` 子句，和 `ORDER BY` 子句中能够使用聚合函数
- 计算行数

    ```sql
    -- 全部行数（包括 `NULL`）
    SELECT COUNT(*)
      FROM Product;

    -- `purchase_price` 列非空行数
    SELECT COUNT(purchase_price)
      FROM Product;
    ```

    - `COUNT(*)` 会得到包括 `NULL` 在内的数据行数；`COUNT(<列名>)` 会得到该列的除 `NULL` 之外的数据行数
- 求和

    ```sql
    SELECT SUM(sale_price), SUM(purchase_price)
      FROM Product;
    ```

    - `NULL` 数据在求和之前已被剔除
- 求平均

    ```sql
    SELECT AVG(sale_price), AVG(purchase_price)
      FROM Product;
    ```

- 最值

    ```sql
    SELECT MAX(sale_price), MIN(purchase_price)
      FROM Product;

    SELECT MAX(regist_date), MIN(regist_date)
      FROM Product;
    ```

- 删除重复值

    ```sql
    SELECT COUNT(DISTINCT product_type)
      FROM Product;
    ```

    - `DISTINCT` 要在括号中
# 分组（`GROUP BY` 子句）
- 先把表分成几组，然后再对分好组的数据进行汇总处理（不是直接对表中的所有数据进行处理）
![](src/group.jpg)

```sql
SELECT <列名1>, <列名2>, <列名3>, ...
  FROM <表名>
 GROUP BY <列名1>, <列名2>, <列名3>, ...;
```

```sql
SELECT product_type, COUNT(*)
  FROM Product
 GROUP BY product_type;
```

- `GROUP BY` 子句中指定的列称为**聚合键**或者分组列
- `GROUP BY` 子句可以通过逗号分隔指定多列
- `GROUP BY` 子句一定要写在 `FROM` 语句之后；如果有 `WHERE` 子句则要写在 `WHERE` 子句之后

    ```sql
    SELECT purchase_price, COUNT(*)
    FROM Product
    WHERE product_type = '衣服'
    GROUP BY purchase_price;
    ```

- 聚合键中包含 `NULL` 

    ```sql
    SELECT purchase_price, COUNT(*)
    FROM Product
    GROUP BY purchase_price;
    ```

    - 聚合键中包含 `NULL` 时，也会将 `NULL` 作为一组特定数据，在结果中会以“不确定”行（空行）的形式表现出来
- 使用聚合函数时 `SELECT` 子句中只能存在以下三种元素
    1. 常数
    2. 聚合函数
    3. `GROUP BY` 子句中指定的列名（即聚合键）
    - > 通过某个聚合键将表分组之后，结果中的一行数据就代表一组。使用进货单价将表进行分组之后，一行就代表了一个进货单价。但是聚合键和商品名并不一定是一对一的。例如，进货单价是 2800 日元的商品有“运动 T 恤”和“菜刀”两种，没有规则可以决定 2800 日元这一行应该与那个商品对应
- `GROUP BY` 子句中不能使用 `SELECT` 子句中定义的别名
    - `SELECT` 子句在 `GROUP BY` 子句之后执行，在执行 `GROUP BY` 子句时，`SELECT` 子句中定义的别名 DBMS 还并不知道
- `GROUP BY` 子句的结果是随机排序的
- `DISTINCT` 和 `GROUP BY` 能够实现相同的功能，都会把 `NULL` 作为一个独立的结果返回，对多列使用时也会得到完全相同的结果，执行速度也相当（都是数据的内部处理，都是通过排序处理来实现）

    ```sql
    SELECT purchase_price, product_type
      FROM Product
     GROUP BY purchase_price, product_type;

    SELECT DISTINCT purchase_price, product_type
      FROM Product;
    ```

    - 选择的标准是“想要删除选择结果中的重复记录”时使用 `DISTINCT`，在“想要计算汇总结果”时使用 `GROUP BY`
        - 不使用 `COUNT` 等聚合函数而只使用 `GROUP BY` 子句的 `SELECT` 语句，会让人觉得非常奇怪，使人产生“到底为什么要对表进行分组呢？这样做有何必要？”等疑问
# 为聚合结果指定条件
- `WHERE` 子句只能指定记录（行）的条件，而不能用来指定组的条件
- `HAVING`

    ```sql
    SELECT <列名1>, <列名2>, <列名3>, ...
      FROM <表名>
     GROUP BY <列名1>, <列名2>, <列名3>, ...
    HAVING <分组结果对应的条件>
    ```

    - `HAVING` 子句要写在 `GROUP BY` 子句之后

```sql
SELECT product_type, COUNT(*)
  FROM Product
 GROUP BY product_type
HAVING COUNT(*) = 2;

SELECT product_type, AVG(sale_price)
  FROM Product
 GROUP BY product_type
HAVING AVG(sale_price) >= 2500;
```

- `HAVING` 子句能够使用 3 中要素
    1. 常数
    2. 聚合函数
    3. `GROUP BY` 中指定的列名（即聚合键）
        - 把 `GROUP BY` 汇总后的结果作为 `HAVING` 语句的起点，得到的汇总表里不再包含除作为 `GROUP BY` 参数以外的列
- 聚合键所对应的条件既可以写在 `HAVING` 子句中，也可写在 `WHERE` 子句中

    ```sql
    SELECT product_type, COUNT(*)
      FROM Product
     GROUP BY product_type
    HAVING product_type = '衣服';

    SELECT product_type, COUNT(*)
      FROM Product
     WHERE product_type = '衣服'
    GROUP BY product_type;  
    ```

    - **`WHERE` 子句中指定行所对应的条件**
    - **`HAVING` 子句中指定组所对应的条件**
    - 聚合键所对应的条件不应该书写在 `HAVING` 子句当中，而应该书写在 `WHERE` 子句当中
- 使用 `COUNT` 等函数对表中的数据进行聚合操作时，DBMS 内部会进行排序或 hash 处理，会大大增加机器的负荷
- 通过 `WHERE` 子句指定条件时，由于排序之前就对数据进行了过滤，减少了排序的数据量；`HAVING` 子句是在**排序之后**才对数据进行分组的，需要排序的数据量就会多得多
    - `HAVING` 子句中不能使用别名
- 可以对 `WHERE` 子句指定条件所对应的列**创建索引**，这样也可以大幅提高处理速度
    - 创建索引是一种非常普遍的提高 DBMS 性能的方法，效果也十分明显
# 排序
- 从表中抽取数据时，如果没有特别指定顺序，最终排列顺序不确定
- `ORDER BY`

    ```sql
    SELECT <列名1>, <列名2>, <列名3>, ...
      FROM <表名>
     ORDER BY <排序基准列1>, <排序基准列2>, ...
    ```

```sql
SELECT product_id, product_name, sale_price, purchase_price
  FROM Product
 ORDER BY sale_price;
```

- 不论何种情况，`ORDER BY` 子句都需要写在 `SELECT` 语句的末尾
    - 对数据行进行排序的操作必须在结果即将返回时执行
- `ORDER BY` 子句中书写的列名称为**排序键**
- 降序排列

    ```sql
    SELECT product_id, product_name, sale_price, purchase_price
      FROM Product
     ORDER BY sale_price DESC;
    ```

- 不指定顺序时默认使用升序（`ASC`）排列
- `ASC` 和 `DESC` 这两个关键字**以列为单位**进行指定，因此可以同时指定一个列为升序，指定其他列为降序

    ```sql
    SELECT product_id, product_name, sale_price, purchase_price
      FROM Product
     ORDER BY sale_price ASC, product_id ASC;
    ```

- 使用含有 `NULL` 数据的列作为排序键时，`NULL` 会在结果的开头或末尾汇总显示
    - 某些 DBMS 中可以指定 `NULL` 在开头或末尾显示
- 在排序键中可以使用 `SELECT` 子句中定义的别名
- **`SELECT` 子句的执行顺序在 `GROUP BY` 子句之后，`ORDER BY` 子句之前**
- `ORDER BY` 子句中也可以使用存在于表中、但并不包含在 `SELECT` 子句之中的列

    ```sql
    SELECT product_name, sale_price, purchase_price
      FROM Product
     ORDER BY product_id;
    ```

- `ORDER BY` 子句中可以使用聚合函数

    ```sql
    SELECT product_type, COUNT(*)
      FROM Product
     GROUP BY product_type
    ORDER BY COUNT(*);
    ```

- 列编号是指 `SELECT` 子句中的列按照从左到右的顺序进行排列所对应的编号（1, 2, 3, ...）

    ```sql
    -- 通过列名指定
    SELECT product_id, product_name, sale_price, purchase_price
      FROM Product
    ORDER BY sale_price DESC, product_id;
    -- 通过列编号指定
    SELECT product_id, product_name, sale_price, purchase_price
      FROM Product
    ORDER BY 3 DESC, 1;
    ```

- 不建议使用列编号
    1. 不易于阅读
    2. SQL-92A 中明确指出该排序功能将来会被删除