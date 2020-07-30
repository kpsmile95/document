## 2.概念



| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| :----------- | :--------------- | :---------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |

## 3.命令
### 3.1 基础命令
```shell
show dbs; 		#展示所有数据库
use dbname;		#切换数据库
db;				#展示当前数据库名称
```

### 3.2 数据库操作
```shell
 db.collectionName.insert({records});	#向不存在的库中新建一个集合插入一条数据即可新建一个数据库
 
 db.dropDatabase();						#在要删除的库下面执行该命令,即可删除该库
```

### 3.3 集合操作
#### 3.3.1 创建集合
```shell
db.createCollection(name, options)		#创建集合
例如：
db.createCollection("mycol", { capped : true, autoIndexId : true, size : 6142800, max : 10000 } )
```

参数说明：

- name: 要创建的集合名称
- options: 可选参数, 指定有关内存大小及索引的选项

options 可以是如下参数：

| 字段        | 类型 | 描述                                                         |
| :---------- | :--- | :----------------------------------------------------------- |
| capped      | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。** |
| autoIndexId | 布尔 | 3.2 之后不再支持该参数。（可选）如为 true，自动在 _id 字段创建索引。默认为 false。 |
| size        | 数值 | （可选）为固定集合指定一个最大值，以千字节计（KB）。 **如果 capped 为 true，也需要指定该字段。** |
| max         | 数值 | （可选）指定固定集合中包含文档的最大数量。                   |

在插入文档时，MongoDB 首先检查固定集合的 size 字段，然后检查 max 字段。

-------------
当插入一些文档时，MongoDB 会自动创建集合
```shell
 db.mycol2.insert({"name" : "菜鸟教程"})
```
#### 3.3.2 删除集合
```shell
db.collectionName.drop()
```


