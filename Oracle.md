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


### 1.1.1 NUMBER ( P ) 表示整数

完整语法： NUMBER（precision , scale）

- 如果没有设置scale，则默认取值为0，即NUMBER( P )表示整数
- P表示数字的总位数，取值为1~38

用法：
一般用来在表中存放如编码、年龄、次数等用整数记录的数据

###  1.1.2 NUMBER ( P ， S ) 表示浮点数

- NUMBER（precision , scale）
- precision： NUMBER可以存储的最大数字长度（不包括左右两边的0）
- scale：在小数点右边的最大数字长度（包括左侧0）
- 指定了s但是没有指定p，则默认p为38，如：
  列名 NUMBER ( * , s )

用法 :
经常用来做表中存放金额，成绩等有小数位数的数据


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

- 第1字节：世纪+100
- 第2字节：年
- 第3字节：月
- 第4字节：天
- 第5字节：小时+1
- 第6字节：分+1
- 第7字节：秒+1

   ## 1.6 TimeStamp

   这是一个7字节或12字节的定宽日期/时间数据类型。它与DATE数据类型不同，因为TIMESTAMP可以包含小数秒，带小数秒的TIMESTAMP在小数点右边最多可以保留9位,TIMESTAMP是Oracle常用的日期类型。与DATE的区别是不仅可以保存日期和时间，还能保存小数秒，最高精度可以到ns（纳秒）

- 数据库内部用7或者11个字节存储，精度为0，用7字节存储，与DATE功能相同，精度大于0则用11字节存储。
- 格式为：
  第1字节~第7字节：和DATE相同
  第8~11字节：纳秒，采用4个字节存储，内部运算类型为整形。



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

### 4.1.4TRIM,LTRIM,RTRIM
去除当前字符串中两边的指定重复字符，LTRIM仅去除左侧的，RTRIM则仅去除右侧的。
```SQL
SELECT TRIM('A' FROM 'AAAALIVEAAAAA') FROM dual;
-- LIVE
 
SELECT LTRIM('AAAAALIVEAAAA','A') FROM dual;
-- LIVEAAAA

SELECT RTRIM('AAAALIVEAAAA','A') FROM dual;
-- AAAALIVE

SELECT RTRIM('LIVEDBAC','ABCD') FROM dual;
-- LIVE

SELECT LTRIM('BBDAACDCLIVE','ABCD') FROM dual;
-- LIVE

-- RTRIM、 LTRIM可以去掉字符串中指定的多个字符，凡是出现的给定的字符统统去掉（不是按照字符给定的顺序去掉的）。
```

### 4.1.5 LPAD,RPAD补位函数
```SQL
-- 向左侧以指定字符进行补位
SELECT LPAD(800,5,'$') FROM dual;

-- 向右侧以指定字符进行补位
SELECT RPAD(800,5,'$') FROM dual;
```
![LPAD函数](https://img-blog.csdnimg.cn/20190528163343974.PNG)

![RPAD函数](https://img-blog.csdnimg.cn/20190528163800792.PNG)

### 4.1.6 SUBSTR截取字符串

- SUBSTR（char , [m[,n]]）
  用于获取字符串的子串，返回char中从m位开始取n个字符
- 如果m = 0，则从首字符开始，如果m取负数，则从尾部开始
- 如果没有设置n，或者n的长度超过了char的长度，则取到字符串末尾为止。

❤️ **注意：**
（1） 数据库中的下标都是从1开始的
（2）截取的位置可以是负数，若是则表示从倒数第几个字符开始截取

```SQL
SELECT SUBSTR('THINKING IN JAVA',13，4) FROM dual;
-- JAVA

SELECT SUBSTR('THINKING IN JAVA',-4)FROM dual;
-- JAVA
```
### 4.1.7 INSTR
- INSTR（char1,char2[,n,m]）函数
- 查找char2在char1中的位置
n为从第几个字符开始检索
m为第几次出现
n,m不写则默认都是1

```SQL
SELECT INSTR('THINKING IN JAVA','IN',4，1) FROM dual;
-- 6
```

## 4.2 数值函数
### 4.2.1 ROUND

- ROUND(n [ , m ]) ：用于四舍五入
- 参数中的n可以是任何数字，指要被处理的数字
- m必须是整数
- m取正数则四舍五入到小数点后第m位
- m取0值则四舍五入到整数位
- m取负数，则四舍五入到小数点前m为
- m缺省，默认值是0

```sql

SELECT ROUND(45.678，2) FROM dual;
-- 45.68

SELECT ROUND(45.678，0) FROM dual;
-- 46

SELECT ROUND(55.678，-2) FROM dual
-- 100
```
### 4.2.2 TRUNC
- TRUNC ( n [ , m ]) ：用于截取数字
- m取正数则截取到小数点后第m位
- m取0值则截取到整数位
- m取负数则截取到小数点前m为
- m缺省默认值是0

```sql
SELECT ROUND(45.68，1) FROM dual;
-- 45.6

SELECT ROUND(45.68) FROM dual;
-- 45

SELECT ROUND(45.68，-1) FROM dual;
-- 40
```

### 4.2.3 MOD
- MOD(m,n)求余数
```sql
select mod(100,3) from dual;
-- 1
```

❤️ **注意：**
如果n为0 则直接返回m

### 4.2.4 CEIL,FLOOR
向上取整，向下取整
```sql
 SELECT CEIL(45.678) FROM dual;
 -- 46

select floor(45.678) from dual;
-- 45
```

## 4.3 日期转换函数
### 4.3.1 日期和字符转换函数用法（to_date,to_char）
- TO_DATE(char[,fmt [, nlsparams]])：将字符串按照定制格式转换为日期类型。
- char要转换的字符串
- fmt：格式
- nlsparams：指定日期语言

## 4.4 空值函数
### 4.4.1 NVL()
空值函数NVL（arg1,arg2）
当arg1为NULL，函数返回arg2的值，若不为NULL，则返回arg1本身。
所以该函数的作用是将NULL值替换为一个非NULL值。
```SQL
select NVL(NULL,0) FROM DUAL;
-- 0
```

### 4.4.1 NVL2()
NVL2（arg1,arg2,arg3）
当arg1不为NULL，则函数返回arg2
当arg1位NULL，则函数返回arg3
该函数是根据一个值是否为NULL来返回两个不同结果
```sql
select NVL2(null, '有值', '无值') from dual;
```

## 4.5 聚合函数


# 5 序列

## 5.1 什么是序列

- 序列（SEQUENCE）是一种用来生成唯一数字值的数据库对象
- 序列的值由Oracle程序按递增或递减顺序自动生成，通过用来自动产生表的主键值，是一种高效率获得唯一键值的途径
- 序列是独立的数据库对象，和表是独立的对象，序列并不依附于表
- 通常情况下，一个序列为一个表提供主键值，但一个序列也可以为多个表提供主键值

### 5.1.1 序列创建语法
```sql
create sequence SEQ_LOG_ID
minvalue 1  --增长最小值
maxvalue 9999999999999999999999999999  --增长最大值,也可以设置NOMAXvalue -- 不设置最大值
start with 101  --从101开始计数
increment by 1  --自增步长为1
cache 50  --设置缓存cache个序列，如果系统down掉了或者其它情况将会导致序列不连续，也可以设置为---NOCACHE防止跳号
cycle;  --循环,当达到最大值时,不是从start with设置的值开始循环。而是从1开始循环
```
### 5.1.1 序列使用语法
```sql
insert into 表名(id,name)values(seqtest.Nextval,'sequence 插入测试');
/*
CurrVal：返回 sequence的当前值 NextVal：增加sequence的值，然后返回 增加后sequence值
注意：第一次NEXTVAL返回的是初始值；随后的NEXTVAL会自动增加你定义的INCREMENT BY值，然后返回增加后的值。
CURRVAL 总是返回当前SEQUENCE的值，但是在第一次NEXTVAL初始化之后才能使用CURRVAL，否则会出错。
一次NEXTVAL会增加一次 SEQUENCE的值，所以如果你在同一个语句里面使用多个NEXTVAL，其值就是不一样的。- 如果指定CACHE值，ORACLE就可以预先在内存里面放置一些sequence，这样存取的快些。cache里面的取完后，oracle自动再取一组 到cache。 使用cache或许会跳号， 比如数据库突然不正常down掉（shutdown abort),cache中的sequence就会丢失. 所以可以在create sequence的时候用nocache防止这种情况。
*/
```






