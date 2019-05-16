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