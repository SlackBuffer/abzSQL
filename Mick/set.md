- 集合运算指对满足同一规则的记录进行的加减等四则运算
- Combining Queries
# 表的四则运算

```sql
CREATE TABLE Product2
(product_id CHAR(4) NOT NULL,
 product_name VARCHAR(100) NOT NULL,
 product_type VARCHAR(32) NOT NULL,
 sale_price INTEGER,
 purchase_price INTEGER,
 regist_date DATE,
 PRIMARY KEY (product_id));

BEGIN TRANSACTION; 
INSERT INTO Product2 VALUES ('0001', 'T恤衫' ,'衣服', 1000, 500, '2008-09-20');
INSERT INTO Product2 VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
INSERT INTO Product2 VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL);
INSERT INTO Product2 VALUES ('0009', '手套', '衣服', 800, 500, NULL);
INSERT INTO Product2 VALUES ('0010', '水壶', '厨房用具', 2000, 1700, '2009-09-20');
COMMIT;
```

- `UNION`（加法）
	
    ```sql
    SELECT product_id, product_name
      FROM Product
    UNION
    SELECT product_id, product_name
      FROM Product2;
    ```

- **集合运算符会去除重复的记录**
- 集合运算注意事项
    - 作为运算对象的记录的**列数必须相同**
    	
        ```sql
        -- 列数不一致时会发生错误
        SELECT product_id, product_name
          FROM Product
        UNION
        SELECT product_id, product_name, sale_price
          FROM Product2;
        ```
    
    - 作为运算对象的记录中列的**类型必须一致**
    	
        ```sql
        -- 数据类型不一致时会发生错误
        SELECT product_id, sale_price
          FROM Product
        UNION
        SELECT product_id, regist_date
          FROM Product2;
        ```
    
        - 一定要使用不同数据类型的列时，可以使用 `CAST`
    - `ORDER BY` 子句只在**最后**使用**一次**
- 保留重复行的集合运算（`ALL` 选项）
	
    ```sql
    SELECT product_id, product_name
      FROM Product
    UNION ALL
    SELECT product_id, product_name
      FROM Product2;
    ```

- 表交集 `INTERSECT`    
	
    ```sql
    SELECT product_id, product_name
      FROM Product
    INTERSECT ALL
    SELECT product_id, product_name
      FROM Product2
    ORDER BY product_id;
    ```

    - [ ] 希望保留重复行时同样需要使用 `INTERSECT ALL`
- 记录的减法 `EXCEPT`
	
    ```sql
    SELECT product_id, product_name
      FROM Product
    EXCEPT
    SELECT product_id, product_name
      FROM Product2
    ORDER BY product_id;
    ```

    - 被减数和减数位置不同，得到的结果也不同
- 集合运算优先级
    - https://stackoverflow.com/a/56225576/6902525
- 以上集合运算以**行方向**为单位进行操作
# 联结（以列为单位对表进行联结）
- 联结 `JOIN` - 将其他表中的列添加过来，进行“添加列”的运算
    - 以 A 中的列作为桥梁，将 B 中满足同样条件的列汇集到同一结果之中
## 内联结 `INNER JOIN`

```sql
-- 两张表都存在的列 `product_id`
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
  FROM ShopProduct AS SP INNER JOIN Product AS P 
    ON SP.product_id = P.product_id;
```

- 进行联结时需要在 `FROM` 子句中使用多张表
    - 别名不是必须的，可以直接使用表的原名，但表名太长会影响可读性
    - `ON` 用来指定联结条件（联结两张表使用的列，即联结键），起到 `WHERE` 的作用
        - 联结条件可以使用 `=` 来记述，在语法上，还可以使用 `<=` 和 `BETWEEN` 等谓词
        - 需要指定多个条件时，可以使用 `AND`、`OR`
        - 进行内联结时必须使用 `ON` 子句，写在 `FROM` 和 `WHERE` 之间
    - 使用联结时 `SELECT` 子句中的列建议按照 `<表的别名>.<列名>` 的格式书写
        - 语法上来说，只有那些同时存在于两张表中的列才必须使用这样的书写方式，只存在于一张表中的键可以省略表的别名，但不建议省略
- 内联结和 `WHERE` 字句结合

    ```sql
    SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
      FROM ShopProduct AS SP INNER JOIN Product AS P 
        ON SP.product_id = P.product_id
     WHERE SP.shop_id = '000A';
    ```

    - 使用联结运算将满足相同规则的表联结起来后，`WHERE`、`GROUP BY`、`HAVING`、`ORDER BY` 等工具都可以正常使用（可以将联结之后的结果想象为新创建出来的一张临时表）
- **内联结只能选取出同时存在于两张表中的数据**
## 外联结 `OUTER JOIN`
	
```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
  FROM ShopProduct AS SP RIGHT OUTER JOIN Product AS P
    ON SP.product_id = P.product_id;
```

- 对于外联结来说，只要数据存在于某一张表当中，就能够读取出来
    - 在实际的业务中，例如想要生成固定行数的单据时，就需要使用外联结
        - 若使用内联结，根据 `SELECT` 语句执行时商店库存状况的不同，结果的行数也会发生改变，生成的单据的版式也会受到影响，而使用外联结能够得到固定行数的结果
    - 外联结结果中包含原表中不存在（在原表之外）的信息，用 `NULL` 表示
- 外联结需指定**主表**（`LEFT`, `RIGHT`），最终的结果会包含主表的所有数据
    - 非主表不满足联结条件的数据不会包含在最终结果中
    - 使用 `LEFT` 时 `FROM` 子句中写在左侧的表是主表，使用 `RIGHT` 时右侧的表是主表
## 2 张以上表的联结

```sql
CREATE TABLE InventoryProduct
(inventory_id CHAR(4) NOT NULL,
 product_id CHAR(4) NOT NULL,
 inventory_quantity INTEGER NOT NULL,
 PRIMARY KEY (inventory_id, product_id));

BEGIN TRANSACTION; 
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0001', 0);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0002', 120);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0003', 200);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0004', 3);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0005', 0);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0006', 99);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0007', 999);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P001', '0008', 200);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0001', 10);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0002', 25);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0003', 34);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0004', 19);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0005', 99);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0006', 0);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0007', 0);
INSERT INTO InventoryProduct (inventory_id, product_id, inventory_quantity) 
VALUES ('P002', '0008', 18);
COMMIT;
```

- 3 表联结
	
    ```sql
    SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price, IP.inventory_quantity
      FROM ShopProduct AS SP INNER JOIN Product AS P
        ON SP.product_id = P.product_id
            INNER JOIN InventoryProduct AS IP
              ON SP.product_id = IP.product_id
    WHERE IP.inventory_id = 'P001';
    ```

    - `Product` 表和 `ShopProduct` 表已联结，无需再对 `Product` 表和`InventoryProduct` 表进行联结（也可以进行联结，但结果不变）
## 交叉联结 `CROSS JOIN`（笛卡尔积）
- 交叉联结是所有联结运算的基础

  ```sql
  SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name
    FROM ShopProduct AS SP CROSS JOIN Product AS P;
  ```

- 交叉联结是对两张表中的全部记录进行交叉组合，因此结果中的记录数通常是两张表中**行数的乘积**
- 实际业务中并不会使用
- 联结的特定语法和过时语法
	
  ```sql
  -- 过时内联结语法
  SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
    FROM ShopProduct SP, Product P
   WHERE SP.product_id = P.product_id
     AND SP.shop_id = '000A';
  ```

  - 不建议使用
      - 无法马上判断出到底是内联结还是外联结（又或者是其他种类的联结）
      - 由于联结条件都写在 `WHERE` 子句之中，因此无法在短时间内分辨出哪部分是联结条件，哪部分是用来选取记录的限制条件
      - 不知何时会被放弃支持过时语法
# 除法（[ ] 重要）
	
```sql
CREATE TABLE Skills
(skill VARCHAR(32),
 PRIMARY KEY(skill));

CREATE TABLE EmpSkills
(emp VARCHAR(32),
 skill VARCHAR(32),
 PRIMARY KEY(emp, skill));

BEGIN TRANSACTION;
INSERT INTO Skills VALUES('Oracle');
INSERT INTO Skills VALUES('UNIX');
INSERT INTO Skills VALUES('Java');
INSERT INTO EmpSkills VALUES('相田', 'Oracle');
INSERT INTO EmpSkills VALUES('相田', 'UNIX');
INSERT INTO EmpSkills VALUES('相田', 'Java');
INSERT INTO EmpSkills VALUES('相田', 'C#');
INSERT INTO EmpSkills VALUES('神崎', 'Oracle');
INSERT INTO EmpSkills VALUES('神崎', 'UNIX');
INSERT INTO EmpSkills VALUES('神崎', 'Java');
INSERT INTO EmpSkills VALUES('平井', 'UNIX');
INSERT INTO EmpSkills VALUES('平井', 'Oracle');
INSERT INTO EmpSkills VALUES('平井', 'PHP');
INSERT INTO EmpSkills VALUES('平井', 'Perl');
INSERT INTO EmpSkills VALUES('平井', 'C++');
INSERT INTO EmpSkills VALUES('若田部', 'Perl');
INSERT INTO EmpSkills VALUES('渡来', 'Oracle');
COMMIT;

-- 选出掌握所有 3 个领域的技术的员工
SELECT DISTINCT emp
  FROM EmpSkills ES1
 WHERE NOT EXISTS
        (SELECT skill
           FROM Skills
         EXCEPT
         SELECT skill
           FROM EmpSkills ES2
          WHERE ES1.emp = ES2.emp);
```

# 习题
7.2

```sql
SELECT CASE WHEN SP.shop_id IS NULL
            THEN '不确定'
            ELSE SP.shop_id
       END,
SP.shop_name, SP.product_id, P.product_name, P.sale_price
  FROM ShopProduct AS SP RIGHT OUTER JOIN Product AS P
    ON SP.product_id = P.product_id;

SELECT COALESCE(SP.shop_id, '不确定') AS shop_id,
       COALESCE(SP.shop_name, '不确定') AS shop_name,
       P.product_id,
       P.product_name,
       P.sale_price
  FROM ShopProduct SP RIGHT OUTER JOIN Product P
    ON SP.product_id = P.product_id
ORDER BY shop_id;
```