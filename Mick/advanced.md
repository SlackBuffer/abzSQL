# 窗口函数
- 窗口函数也称为 OLAP 函数
    - OnLine Analytical Processing，即对数据库数据进行实时分析处理
    - 在 Oracle 和 SQL Server 中称为分析函数
    - 截至 2016 年 5 月，Oracle、SQL Server、DB2、PostgreSQL 的最新版本都已经支持该功能，MySQL 的最新版本 5.7 还不支持该功能

    ```sql
    -- [] 中的内容可省略
    <窗口函数> OVER ([PARTITION BY <列清单>]
                ORDER BY <排序用列清单>)
    ```

- 窗口函数大体可分为 2 种
    1. 能够作为窗口函数的聚合函数（`SUM`、`AVG`、`COUNT`、`MAX`、`MIN`）
    2. `RANK`、`DENSE_RANK`、`ROW_NUMBER` 等专用窗口函数
- 根据商品种类（`product_type`），按照销售单（`sale_price`）从低到高的顺序排序
	
    ```sql
    SELECT product_name, product_type, sale_price,
            RANK () OVER (PARTITION BY product_type
                ORDER BY sale_price) AS ranking
      FROM Product;
    ```

    - `PARTITION BY` 设定排序的对象范围
    - `ORDER BY` 省略 `ASC`/`DESC` 默认按 `ASC` 排
    - `PARTITION BY` 在横向上对表进行**分组**，而 `ORDER BY` 决定纵向**排序**的规则
- `PARTITION BY` 子句不具备 `GROUP BY` 子句的汇总功能，因此使用 `RANK` 函数并不会减少原表中记录的行数
- 通过 `PARTITION BY` 分组后的记录集合称为窗口，表示范围
- 各个窗口在定义上绝对不会包含共通的部分，这与通过 `GROUP BY` 子句分割后的集合具有相同的特征