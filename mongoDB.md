## 1.介绍及spring boot操作
**介绍：**https://blog.csdn.net/yanpenglei/article/details/79261875
**操作：**https://www.jianshu.com/p/fdd7e303dff1
**spring boot注解介绍：**https://cloud.tencent.com/developer/article/1384034

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
show dbs; 					#展示所有数据库
use dbname;					#切换数据库
db;							#展示当前数据库名称
show collections;			#展示当前数据库所有的集合名称
db.collectionName.find();	#展示collectionName集合下所有记录
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
### 3.4 文档操作
#### 3.4.1 插入文档
```shell
db.COLLECTION_NAME.insert(document)	
#若插入的数据主键已经存在，则会抛 org.springframework.dao.DuplicateKeyException 异常，提示主键重复，不保存当前数据。

db.COLLECTION_NAME.save(document)	
#已废弃，新版本可使用insertOne()或者replaceOne()
```

----

**3.2 版本之后新增**
```shell
#向集合插入一个新文档
db.collection.insertOne(
   <document>,
   {
      writeConcern: <document>
   }
)

#向集合插入一个多个文档
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```

```shell
#操作

#  插入单条数据
> var document = db.collection.insertOne({"a": 3})
> document
{
        "acknowledged" : true,
        "insertedId" : ObjectId("571a218011a82a1d94c02333")
}

#  插入多条数据
> var res = db.collection.insertMany([{"b": 3}, {'c': 4}])
> res
{
        "acknowledged" : true,
        "insertedIds" : [
                ObjectId("571a22a911a82a1d94c02337"),
                ObjectId("571a22a911a82a1d94c02338")
        ]
}
```



#### 3.4.2 修改操作

** update()方法 **
```shell
# 将匹配到的值都进行修改
# <multi>属性默认是false(不写就是默认),即每次只修改扫描到的第一条数据；
# 当为true时，修改所有
db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}},{multi:true})
```

** save()方法 **
```shell
# 将匹配到的值都进行修改
# <multi>属性默认是false(不写就是默认),即每次只修改扫描到的第一条数据；
# 当为true时，修改所有
db.col.save({
    "_id" : ObjectId("5f2374ebb38cdd2108824376"),
    "title" : "MongoDB",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "Runoob",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "NoSQL"
    ],
    "likes" : 110
})
```
