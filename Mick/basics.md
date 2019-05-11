- DBMS (database management system) 主要通过数据的保存格式来进行分类
    1. 层次数据库（Hierarchical Database，HDB）
    2. 关系数据库（Relational Database，RDB）
        - 采用由行和列组成的二维表来管理数据
        - 使用专门的 SQL（Structured Query Language，结构化查询语言）对数据进行操作
        - 代表性的关系型数据库管理系统（Relational Database Management System，RDBMS）
            1. Oracle Database - 甲骨文
            2. SQL Server - 微软
            3. DB2 - IBM
            4. PostgreSQL - 开源
            5. MySQL - 开源
    3. 面向对象数据库（Object Oriented Database，OODB）
    4. XML数据库（XML Database，XMLDB）
    5. 键值存储系统（Key-Value Store，KVS）
- 关系数据库采用被称为数据库表的二维表来管理数据
- 表的列（垂直方向）称为**字段**，它代表了保存在表中的数据项目
- 表的行（水平方向）称为**记录**，它相当于一条数据
- 关系数据库必须**以行为单位**进行数据读写
- 行和列交汇的方格称为单元格，一个单元格中只能输入一个数据
- 一条 SQL 语句可以描述一个数据库操作
- SQL 用关键字、表名、列名等组合而成的一条语句（SQL 语句）来描述操作的内容
    - 关键字是指那些含义或使用方法已事先定义好的英语单词
- SQL 语句分为 3 类
    1. DDL（Data Definition Language，数据定义语言）
        - 用来创建或者删除存储数据用的数据库以及数据库中的表等对象
            1. `CREATE`：创建数据库和表等对象
            2. `DROP`：删除数据库和表等对象
            3. `ALTER`：修改数据库和表等对象的结构
    2. DML（Data Manipulation Language，数据操纵语言） 
        - 用来查询或者变更表中的记录
            1. `SELECT`：查询表中的数据
            2. `INSERT`：向表中插入新数据
            3. `UPDATE`：更新表中的数据
            4. `DELETE`：删除表中的数据
    3. DCL（Data Control Language，数据控制语言） 
        - 用来确认或者取消对数据库中数据的变更；还可以对 RDBMS 的用户是否有权限操作数据库中的对象（数据库表等）进行设定
            1. `COMMIT`：确认对数据库中的数据进行的变更
            2. `ROLLBACK`：取消对数据库中的数据进行的变更
            3. `GRANT`：赋予用户操作权限
            4. `REVOKE`：取消用户的操作权限
- 在 RDBMS 中，SQL 语句逐条执行
- SQL 基本规则
    - SQL 语句要以分号 `;` 结尾
    - SQL 关键字、表名、列名不区分大小写
        - 关键字大写，表名的首字母大写，其余（列名等）小写
    - 插入到表中的数据区分大小写
    - 常数
        - 字符串，日期要用**单引号** `'` 括起来
        - 数字可直接书写
    - 单词间需要用半角空格或换行来分隔
        - 插入空行（无任何字符的行）会造成执行错误
- 只能使用英文字母、数字、下划线作为数据库、表和列的名称；名称必须以英文字母开头
- 注释
    - 单行 `-- `
        - MySQL 中 `--`要跟空格
    - 多行 `/**/`




- 创建数据库
    - `CREATE DATABASE db_name;`
- 创建表

    ```sql
    CREATE TABLE <表名>
    (<列名1> <数据类型> <该列所需约束>，
     <列名2> <数据类型> <该列所需约束>，
     <列名3> <数据类型> <该列所需约束>，
     <列名4> <数据类型> <该列所需约束>，
     ...
     <该表的约束1>, <该表的约束2> ...);

    CREATE TABLE Product
    (product_id      CHAR(4)      NOT NULL,
    product_name    VARCHAR(100) NOT NULL,
    product_type    VARCHAR(32)  NOT NULL,
    sale_price      INTEGER ,
    purchase_price  INTEGER ,
    regist_date     DATE ,
    PRIMARY KEY (product_id));
    ```

    - 每一列的数据类型必须制定
    - 约束可以在定义列的时候设置，也可以在语句末尾进行设置
        - `NOT NULL`（不能为空）的约束只能以列为单位进行设置
    - 同一个数据库不能有两个相同名称的表，同一个表不能有两个名称相同的列
- 变更表名
    - `ALTER TABLE Product RENAME TO Products;`
- 数据类型
    - `INTEGER`
    - `CHAR`
        - 可以在括号中指定该列可以存储的字符串的最大长度，超长部分无法存入到该列中
        - 字符串以**定长**字符串的形式存储，列中存储的字符串长度未达到最大长度时，用**半角空格**进行补足
        - 表中存储的字符串区分大小写
    - `VARCHAR`
        - 该类型的列以可变长字符串的形式来保存字符串，不自动补足
    - `DATE`
- 约束
    - 对列中存储的数据进行限制或者追加条件的功能
- **键**是在指定特定数据时使用的列的组合
    - 有多种键
    - 主键（primary key）指可以指定特定的唯一一行数据的**列**
        - 把 `product_id` 指定为主键就可以通过该列取出特定的商品数据
        - 若 `product_id` 列中存在重复数据，则无法取出唯一的一行特定数据
        - 主键约束约束的是该列的数据不能重复

            ```
            ERROR:  duplicate key value violates unique constraint "product_pkey"
            DETAIL:  Key (product_id)=(0008) already exists.
            ```
            
- 删除表
    - `DROP TABLE table_name;`
    - 删除的表无法恢复
- 更新表的定义
    - 添加列

        ```sql
        ALTER TABLE table_name ADD COLUMN <列的定义>;

        ALTER TABLE Product ADD COLUMN product_name_pinyin VARCHAR(100);
        ```

    - 删除列

        ```sql
        ALTER TABLE table_name DROP COLUMN <列名>;

        ALTER TABLE Product DROP COLUMN product_name_pinyin;
        ```

    - `ALTER TABLE` 语句执行之后无法恢复，误添的列可以通过 `ALTER TABLE` 语句删除，或者将表全部删除之后重新再创建
- 插入数据

    ```sql
    -- DML ：插入数据
    BEGIN TRANSACTION;
    INSERT INTO Product VALUES ('0001', 'T恤衫', '衣服', 1000, 500, '2009-09-20');
    INSERT INTO Product VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
    INSERT INTO Product VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL);
    INSERT INTO Product VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');
    INSERT INTO Product VALUES ('0005', '高压锅', '厨房用具', 6800, 5000, '2009-01-15');
    INSERT INTO Product VALUES ('0006', '叉子', '厨房用具', 500, NULL, '2009-09-20');
    INSERT INTO Product VALUES ('0007', '擦菜板', '厨房用具', 880, 790, '2008-04-28');
    INSERT INTO Product VALUES ('0008', '圆珠笔', '办公用品', 100, NULL,'2009-11-11');
    COMMIT;
    -- SELECT * FROM Product;
    ```

- 子句的书写顺序  
`SELECT` 子句 → `FROM` 子句 → `WHERE` 子句 → `GROUP BY` 子句 → `HAVING` 子句 → `ORDER BY` 子句