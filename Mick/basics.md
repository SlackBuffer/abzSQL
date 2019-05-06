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
        - 字符串，日期要用单引号 `'` 括起来
        - 数字可直接书写
    - 单词间需要用半角空格或换行来分隔
    
















- `CREATE DATABASE shop;`
- `\l` - lists databases
- `\c shop` - 从 `psql` 切到 `shop`
    - `docker exec -it learn-sql psql -U hofungkoeng -d shop`
- `\q` - 退出 postgres
- `\d` - checks all tables in the database
- `\d+ <table_name>` - checks the details of a table
