ps:https://blog.csdn.net/qq_43437465/category_8992459.html
# 1. 数据类型

##   1.1 number 

   NUMBER表示数字类型。经常被定义成NUMBER（P，S）形式，其中：
   P：表示数字的总位数
   S：表示小数点后面的位数

   eg:
   	sal NUMBER(6,2)
   表示sal列中的数据，整数位最大为4位，小数位最大为2位，也就是最大取值：9999.99

| Actual Data | Specified As | Stored As         |
| ----------- | ------------ | ----------------- |
| 123.89      | NUMBER       | 123.89            |
| 123.89      | NUMBER(3)    | 124               |
| 123.89      | NUMBER(6,2)  | 123.89            |
| 123.89      | NUMBER(6,1)  | 123.9             |
| 123.89      | NUMBER(3)    | 124               |
| 123.89      | NUMBER(4,2)  | exceeds precision |
| 123.89      | NUMBER(6,-2) | 100               |
| .01234      | NUMBER(4,5)  | .01234            |
| .00012      | NUMBER(4,5)  | .00012            |
| .000127     | NUMBER(4,5)  | .00013            |
| .0000012    | NUMBER(2,7)  | .0000012          |
| .00000123   | NUMBER(2,7)  | .0000012          |
| 1.2e-4      | NUMBER(2,5)  | 0.00012           |
| 1.2e-5      | NUMBER(2,5)  | 0.00001           |

   INTEGER类型

   ## 1.2 CHAR

   表示固定长度的字符类型。经常被定义成CHAR（N）形式，N表示占用的字节数。最大长度是2000字节。

   eg：
   	ename CHAR(20)
   表示ename列中最多可储存20个字节的字符串，并且占用的空间是固定的20个字节。

   

   ## 1.3 VARCHAR2（Oracle特有的数据类型）

   表示变长的字符类型。定义格式是VARCHAR2（N），N表示最多可占用的字节数。
   最大长度是4000字节。

   eg:
   	job VARCHAR2(100)
   表示job列中最多可存储长度为100个字节的字符串。根据其中保存的数据长度，占用的空间是变化的，最大占用空间为100个字节。

   

   ## 1.4 LONG 和 CLOB类型

   - LONG：VARCHAR2加长版，存储变长字符串，最多可达2GB的字符数据

   LONG有诸多限制：
   （1）每个表只能有一个LONG类型列
   （2）不能作为主键
   （3）不能建立索引
   （4）不能出现在查询条件中

   - CLOB：存储定长或变长字符串，最多可达4GB的字符串数据
     ❤️ **Oracle建议开发中使用CLOB替代LONG类型**

   

   ## 1.5 DATE

   DATE是最常用的数据类型，日期数据类型存储日期和时间信息。虽然可以用字符或数字类型表示日期和时间信息，但是日期数据类型具有特殊关联的属性。为每个日期值，Oracle 存储以下信息： 世纪、 年、 月、 日期、 小时、 分钟和秒。一般占用7个字节的存储空间。

   

   ## 1.6 TimeStamp

   这是一个7字节或12字节的定宽日期/时间数据类型。它与DATE数据类型不同，因为TIMESTAMP可以包含小数秒，带小数秒的TIMESTAMP在小数点右边最多可以保留9位

# 2. DDL语句

   ## 2.1 创建表

   ​	create（同MySQL）

   ## 2.2 查看表

   ​	DESC（同MySQL）

   ## 2.3 修改表名

   ​	RENAME tableName TO newName

   ## 2.4 增加字段

   ​	ALTER TABLE tableName ADD ( fieldName FIELDTYPE DEFAULT VALUE)

   ​	eg:

   ​		ALTER TABLE employs ADD(hiredate DATE DEFAULT sysdate);

   ​	❤️ 注意：列只能增加在最后，不能插入到现有的列中。

   ## 2.5 修改现有字段

   ​	ALTER TABLE tableName MODIFY ( fieldName FIELDTYPE DEFAULT VALUE)；

   ​	eg:

   ​		ALTER TABLE employs MODIFY ( job varchar2(40) DEFAULT 'CLERK' );

   ​	❤️ 注意：修改表结构都应当避免在表中有数据以后进行，若表中有数据，修改表中字段时尽量不要修改类型，若修改长度尽量增大避免缩小，否则可能导致失败。

   ## 2.6 删除现有字段

   ​	ALTER TABLE tableName DROP( fieldName )

   ​	eg:

   ​		ALTER TABLE employs DROP( hiredate )

   ​	❤️ 注意：
   ​			删除字段需要从每行中删掉该字段占据的长度和数据，并释放在数据块中占据的空间，如果表记录比较大，删除字段可能需要比较长的时间。



# 3. DML

   (同MySQL)

# 4. 函数

## 4.1 字符串函数：
### 4.1.1	CONCAT 和 “||”
- CONCAT（char1，char2）
返回两个字符串连接后的结果，两个参数char1，char2是要连接的两个字符串。

- 等价操作：连接操作符“||”
   如果char1，char2任何一个为NULL，相当于连接了一个空格
   
```sql
/*
字符串函数
CONCAT()函数，用来连接字符串
*/
SELECT CONCAT(ename,sal) FROM emp
    
SELECT CONCAT( CONCAT(ename,','),sal) FROM emp
 	
SELECT ename||','||sal FROM emp
```

### 4.1.2 LENGTH
LENGTH(char)
用于返回字符串的长度

如果字符类型是VARCHAR2，返回字符的实际长度，如果字符类型是CHAR，长度还要包括后补的空格。
```SQL
--LENGTH函数，查看字符串长度
 SELECT ename,LENGTH(ename) FROM emp
```
### 4.1.3 UPPER,LOWER,INITCAP
大小写转换函数，用来转换字符的大小写

UPPER(char)：用于将字符转换为大写形式
LOWER(char)：用于将字符转换为小写形式
INITCAP(char)：用于将字符串中每个单词的首字母大写，其他字符小写
❤️ 注意：

（1）对于INITCAP而言，可以使用空格隔开多个单词，那么每个单词首字母都会大写。

（2）如果输入的参数是NULL值，仍然返回NULL值。
```SQL
SELECT UPPER('helloword'),LOWER('HELLOWORD'),INITCAP('HELLOWORD') FROM  dual

此处的dual是伪表的意思
伪表：dual
 当查询的内容不和任何表中数据有关系时，可以使用伪表，伪表只会查询出一条记录。
```

### TRIM,LTRIM,RTRIM
去除当前字符串中两边的指定重复字符，LTRIM仅去除左侧的，RTRIM则仅去除右侧的。
```SQL
 SELECT TRIM('A' FROM 'AAAALIVEAAAAA') FROM dual
```