- 集合运算指对满足同一规则的记录进行的加减等四则运算
# 表的加减法

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

- `UNION`
	
    ```sql
    SELECT product_id, product_name
      FROM Product
    UNION
    SELECT product_id, product_name
      FROM Product2;
    ```

- 集合运算符会去除重复的记录
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
    - 别名不是必须的，可以直接使用表的原名（表名太长会影响可读性）