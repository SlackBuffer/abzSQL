# 函数
- 函数大致分类
    - 算术函数（用来进行数值计算的函数）
    - 字符串函数（用来进行字符串操作的函数）
    - 日期函数（用来进行日期操作的函数）
    - 转换函数（用来转换数据类型和值的函数）
    - 聚合函数（用来进行数据聚合的函数）
- 绝大多数函数对 `NULL` 都返回 `NULL`
## 算数函数

```sql
CREATE TABLE SampleMath
(m NUMERIC (10, 3),
 n INTEGER,
 p INTEGER);

BEGIN TRANSACTION; 
INSERT INTO SampleMath(m, n, p) VALUES (500, 0, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (-180, 0, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, NULL, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 7, 3);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 5, 2);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 4, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (8, NULL, 3);
INSERT INTO SampleMath(m, n, p) VALUES (2.27, 1, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (5.555, 2, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 1, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (8.76, NULL, NULL);
COMMIT;
```

- 算数运算符：`+`, `-`, `*`, `/`
- `ABS(数值)`

    ```sql
    SELECT m, ABS(m) AS abs_col FROM SampleMath;
    ```

    - ABS 函数的参数为 `NULL` 时，结果也是 `NULL`
- `MOD(被除数, 除数)`
	
    ```sql
    SELECT n, p, MOD(n, p) AS mod_col FROM SampleMath;
    ```

    - SQL Server 用 `%`
- `ROUND(对象数值, 保留小数位数)`
	
    ```sql
    SELECT m, n, ROUND(m, n) AS round_col FROM SampleMath;
    ```

## 字符串函数

```sql
CREATE TABLE SampleStr
(str1 VARCHAR(40),
 str2 VARCHAR(40),
 str3 VARCHAR(40));

BEGIN TRANSACTION;
INSERT INTO SampleStr (str1, str2, str3) VALUES ('opx', 'rt', NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc', 'def', NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('山田', '太郎', '是我');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aaa', NULL, NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES (NULL, 'xyz', NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('@!#$%', NULL, NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('ABC', NULL, NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aBC', NULL, NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc太郎', 'abc', 'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abcdefabc', 'abc', 'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('micmic', 'i', 'I');
COMMIT;
```

- `str1 || str2 || ...`
	
    ```sql
    SELECT str1, str2, str1 || str2 AS str_concat FROM SampleStr;
    ```

    - 字符串拼接时，如果其中包含 `NULL`，那么得到的结果也是 `NULL`
    - SQL Server 用 `+`，MySql 使用 `CONCAT`
- `LENGTH(字符串)`
    - SQL Serve 用 `LEN`
    - **注意多字节字符**
- `LOW(字符串)`, `UPPER(字符串)`
	
    ```sql
    SELECT str1, LOWER(str1) AS low_str FROM SampleStr WHERE str1 IN ('ABC', 'aBC', 'abc', '山田');
    ```

- `REPLACE(操作对象, 被替换, 替换者)`
    - 将字符串的一部分替换为其他的字符串
	
    ```sql
    SELECT str1, str2, str3, REPLACE(str1, str2, str3) AS rep_str FROM SampleStr;
    ```

- `SUBSTRING(对象字符串 FROM 截取的起始位置 FOR 截取的字符数)`
    - **注意多字节字符**
	
    ```sql
    SELECT str1, SUBSTRING(str1 FROM 3 FOR 2) AS sub_str FROM SampleStr;
    ```

## 日期函数
- DBMS 对日期函数的实现多不相同
- `CURRENT_DATE`: `SELECT CURRENT_DATE;` 
- `CURRENT_TIME`
- `CURRENT_TIMESTAMP`: 当前日期和时间
- `EXTRACT`
    - `EXTRACT(日期元素 FROM 日期)`
    - 返回值是数值类型

    ```sql
    SELECT CURRENT_TIMESTAMP,
           EXTRACT(YEAR FROM CURRENT_TIMESTAMP) AS year,
           EXTRACT(MONTH FROM CURRENT_TIMESTAMP) AS month,
           EXTRACT(DAY FROM CURRENT_TIMESTAMP) AS day,
           EXTRACT(HOUR FROM CURRENT_TIMESTAMP) AS hour,
           EXTRACT(MINUTE FROM CURRENT_TIMESTAMP) AS minute,
           EXTRACT(SECOND FROM CURRENT_TIMESTAMP) AS second;
    ```

## 转换函数    
- `CAST`
    - 类型转换，`CAST(转换前的值 AS 想要转换的数据类型)`
    - > 之所以需要进行类型转换，是因为可能会插入与表中数据类型不匹配的数据，或者在进行运算时由于数据类型不一致发生了错误，又或者是进行自动类型转换会造成处理速度低下
    	
        ```sql
        -- 字符串类型转成数值类型
        SELECT CAST('0001' AS INTEGER) AS int_col;
        -- 字符串类型转成日期类型
        SELECT CAST('2009-12-14' AS DATE) AS date_col;
        ```
    
- `COALESCE`
    - `NULL` 转换为其他值，`COALESCE(数据1, 数据2, 数据3...)`
    - 返回**可变参数**中左侧开始第 1 个不是 `NULL` 的值
    	
        ```sql
        SELECT COALESCE(NULL, 1) AS col_1,
               COALESCE(NULL, 'test', NULL) AS col_2,
               COALESCE(NULL, NULL, '2009-11-01') AS col_3;

        SELECT COALESCE(str2, 'NULL') FROM SampleStr;
        ```
    
        - 若包含 `NULL` 的列，通过 `COALESCE` 函数转换为其他值后再应用到其它函数或者运算中，这样结果就不再是 `NULL`
# 谓词（predicate）
- 谓词就是返回值为真值的函数
    - 比较运算符的正式名称是比较谓词
## `LIKE`
- 字符串部分一致性查询
- 部分一致大体可以分为前方一致、中间一致和后方一致三种类型
	
```sql
CREATE TABLE SampleLike
(strcol VARCHAR(6) NOT NULL,
 PRIMARY KEY (strcol));

BEGIN TRANSACTION;
INSERT INTO SampleLike (strcol) VALUES ('abcddd');
INSERT INTO SampleLike (strcol) VALUES ('dddabc');
INSERT INTO SampleLike (strcol) VALUES ('abdddc');
INSERT INTO SampleLike (strcol) VALUES ('abcdd');
INSERT INTO SampleLike (strcol) VALUES ('ddabc');
INSERT INTO SampleLike (strcol) VALUES ('abddc');
COMMIT;
```

- 中间一致同时包含前方一致和后方一致的查询结果
	
    ```sql
    -- 前方一致性
    SELECT *
      FROM SampleLike
     WHERE strcol LIKE 'ddd%';
    -- 中间一致性 
    SELECT *
      FROM SampleLike
     WHERE strcol LIKE '%ddd%';
    -- 后方一致性     
    SELECT *
      FROM SampleLike
     WHERE strcol LIKE '%ddd';
    ```

- `%` 代表**大于等于** 0 个字符
- `_` 代表任意 1 个字符
## `BETWEEN`
- 范围查询
- 包含边界值
	
```sql
SELECT product_name, sale_price
  FROM Product
 WHERE sale_price BETWEEN 100 AND 1000;
```

## `IS NULL`, `IS NOT NULL`
## `IN`, `NOT IN`
- `IN` 和 `NOT IN` 无法选出 `NULL` 数据
	
    ```sql
    SELECT product_name, purchase_price
      FROM Product
     WHERE purchase_price NOT IN (320, 500, 5000);
    ```

- `IN`, `NOT IN` 谓词具有其他谓词所没有的用法，可以使用**子查询作为参数**

```sql
CREATE TABLE ShopProduct
(shop_id CHAR(4) NOT NULL,
 shop_name VARCHAR(200) NOT NULL,
 product_id CHAR(4) NOT NULL,
 quantity INTEGER NOT NULL,
 PRIMARY KEY (shop_id, product_id)); -- 指定 2 列作为主键

BEGIN TRANSACTION;
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A', '东京', '0001', 30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A', '东京', '0002', 50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A', '东京', '0003', 15);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B', '名古屋', '0002', 30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B', '名古屋', '0003', 120);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B', '名古屋', '0004', 20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B', '名古屋', '0006', 10);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B', '名古屋', '0007', 40);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C', '大阪', '0003', 20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C', '大阪', '0004', 50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C', '大阪', '0006', 90);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C', '大阪', '0007', 70);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000D', '福冈', '0001', 100);
COMMIT;
```

- 子查询作为 `IN` 参数
	
    ```sql
    -- 取得“在大阪店销售的商品的销售单价”
    SELECT product_name, sale_price
      FROM Product
     WHERE product_id IN (SELECT product_id
                            FROM ShopProduct
                           WHERE shop_id = '000C');
    ```

## `EXIST`
- 判断是否存在满足某种条件的**记录**
    - 存在返回 `TRUE`，不存在返回 `FALSE`
- `EXIST` 通常会使用关联子查询作为参数
- `EXIST` 替代 `IN`	

    ```sql
    -- 大阪店在售商品的销售单价
    SELECT product_name, sale_price
      FROM Product AS P
     WHERE EXISTS (SELECT *
                     FROM ShopProduct AS SP
                    WHERE SP.shop_id = '000C'
                      AND SP.product_id = P.product_id);
    ```

    - `SELECT *`: `EXIST` 只关心记录是否存在，因此返回哪些列都没有关系
- `NOT EXIST` 替代 `NOT IN`	
# `CASE` 表达式
- `CASE` 表达式的语法分为简单 `CASE` 表达式和搜索 `CASE` 表达式
    - 搜索 `CASE` 表达式包含简单 `CASE` 表达式的全部功能
	
    ```sql
    CASE WHEN <求值表达式> THEN <表达式>
         WHEN <求值表达式> THEN <表达式>
         WHEN <求值表达式> THEN <表达式>
         ...
         ELSE <表达式>
    END
    ```

    - 求值表达式值为真值（`TRUE/FALSE/UNKNOWN`）
    - 依次执行，有一个 case 为 `TRUE` 就停止执行；所有 `WHEN` 执行完无 TRUE，则返回 `ELSE` 中的表达式，执行终止
    - `END` 不能省略，`ELSE` 可以省略但不建议省略
- 通过 CASE 表达式为商品种类的值添加前缀
	
    ```sql
    SELECT product_name,
           CASE WHEN product_type = '衣服'
                THEN 'A ：' || product_type
                WHEN product_type = '办公用品'
                THEN 'B ：' || product_type
                WHEN product_type = '厨房用具'
                THEN 'C ：' || product_type
                ELSE NULL
           END AS abc_product_type
    FROM Product;
    ```

- 使用 `CASE` 表达式进行行列转换
	
    ```sql
    SELECT SUM(CASE WHEN product_type = '衣服'
                    THEN sale_price ELSE 0 END) AS sum_price_clothes,
           SUM(CASE WHEN product_type = '厨房用具'
                    THEN sale_price ELSE 0 END) AS sum_price_kitchen,
           SUM(CASE WHEN product_type = '办公用品'
                    THEN sale_price ELSE 0 END) AS sum_price_office
           FROM Product;

    -- 未转换
    SELECT product_type,
           SUM(sale_price) AS sum_price
      FROM Product
     GROUP BY product_type;
    ```

- `CASE` 表达式适用于对 `SELECT` 结果进行编辑
- 简单 `CASE` 表达式
	
    ```sql
    CASE <表达式>
        WHEN <表达式> THEN <表达式>
        WHEN <表达式> THEN <表达式>
        WHEN <表达式> THEN <表达式>
        ...
        ELSE <表达式>
    END
    ```

- 对比
	
    ```sql
    -- 使用搜索 `CASE` 表达式
    SELECT product_name,
        CASE WHEN product_type = '衣服'
             THEN 'A ：' | |product_type
             WHEN product_type = '办公用品'
             THEN 'B ：' | |product_type
             WHEN product_type = '厨房用具'
             THEN 'C ：' | |product_type
             ELSE NULL
         END AS abc_product_type
    FROM Product;

    -- 使用简单 `CASE` 表达式
    SELECT product_name,
        CASE product_type
             WHEN '衣服' THEN 'A ：' | | product_type
             WHEN '办公用品' THEN 'B ：' | | product_type
             WHEN '厨房用具' THEN 'C ：' | | product_type
             ELSE NULL
         END AS abc_product_type
    FROM Product;
    ```

    - 简单 `CASE` 表达式无法在 `WHEN` 子句中指定和 `CASE` 中不同的列
# 习题
- `NOT IN` 的参数中包含 `NULL` 时结果通常会为空；使用子查询作为 `NOT IN` 的参数时，该子查询的返回值也不能是 `NULL`
	
    ```sql
    -- 6.1
    SELECT product_name, purchase_price
      FROM Product
     WHERE purchase_price NOT IN (NULL);    
    ```

- 6.2
	
    ```sql
    -- 可以用 `BETWEEN`
    SELECT SUM(CASE WHEN sale_price <= 1000 THEN 1 ELSE 0 END) AS low_price,
           SUM(CASE WHEN sale_price > 1000 AND sale_price <= 3000 THEN 1 ELSE 0 END) AS mid_price,
           SUM(CASE WHEN sale_price > 3000 THEN 1 ELSE 0 END) AS high_price  
           FROM Product; 
    ```
