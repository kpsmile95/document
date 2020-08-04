
## 1. @Id
主键，不可重复，自带索引，可以在定义的列名上标注，需要自己生成并维护不重复的约束。如果自己不设置@Id主键，mongo会自动生成一个唯一主键，并且插入时效率远高于自己设置主键。原因可参考上一篇mongo和mysql的性能对比。 
在实际业务中不建议自己设置主键，应交给mongo自己生成，自己可以设置一个业务id，如int型字段，用自己设置的业务id来维护相关联的表。

## 2.@Document
标注在实体类上，类似于hibernate的entity注解，标明由mongo来维护该表。
```java
org.springframework.data.mongodb.core.mapping.Document.class
把一个java类声明为mongodb的文档，可以通过collection参数指定这个类对应的文档。
@Document(collection="mongodb 对应 collection 名")      

// 若未加 @Document ，该 bean save 到 mongo 的 user collection
// 若添加 @Document ，则 save 到 reUser collection
@Document(collection="reUser") 
public class User{
}
```

## 3.@Indexed
声明该字段需要加索引，加索引后以该字段为条件检索将大大提高速度。 
唯一索引的话是@Indexed(unique = true)。 
也可以对数组进行索引，如果被索引的列是数组时，MongoDB会索引这个数组中的每一个元素。 
也可以对整个Document进行索引，排序是预定义的按插入BSON数据的先后升序排列。 
也可以对关联的对象的字段进行索引，譬如User对关联的address.city进行索引。（注解怎么写还不清楚，待查）

## 4.@CompoundIndex
复合索引，加复合索引后通过复合索引字段查询将大大提高速度。
```java
@Document
@CompoundIndexes({
    @CompoundIndex(name = "age_idx", def = "{'lastName': 1, 'age': -1}")
})
public class Person<T extends Address> {
}
```

写法如上，lastName和age将作为复合索引，数字参数指定索引的方向，1为正序，-1为倒序。方向对单键索引和随机存不要紧，但如果你要执行分组和排序操作的时候，它就非常重要了。

## 5.@Field
代表一个字段，可以不加，不加的话默认以参数名为列名。

给映射存储到 mongodb 的字段取别名
在 java bean 中字段名为 firstName，存储到 mongo 中 key 为 fName
```java
  @Field("fName")
  private String firstName; 
```
## 6.@Transient
被该注解标注的，将不会被录入到数据库中。只作为普通的javaBean属性。

## 7.@DBRef
关联另一个document对象。类似于mysql的表关联，但并不一样，mongo不会做级联的操作。 
 先来看一下不加DBRef时，mongo保存数据的情况: 
 Article类有String title，List pictureList，两个属性，Picture有一个url，一个desc属性。新建数个Picture对象，并赋值给Article的list，执行Article的insert操作，mongo保存的结果如图： 




 list会作为普通的数据存到article里，并不会为Picture建表，这一点是区别于mysql的级联存储的。

在Article里给list加上DBRef注解后就不同了 




 再次执行添加Article操作后，看结果 




 发现就不再是直接显示的Picture的各个属性了，而是只保存了Picture的id和namespace，同时仍然没有创建Picture的collection（等同于mysql的表）。 
 如此此时查询该Article，会发现list为空，并没有关联上Picture的值。其实上一步已经发现了，系统并没有去创建Picture的表。那即便Article关联了PictureList的id，也是无法查询的。 
 官方解释： 
 The mapping framework does not handle cascading saves. If you change an Account object that is referenced by a Person object, you must save the Account object separately. Calling save on the Person object will not automatically save the Account objects in the property accounts.意思就是不会处理级联保存，你必须单独处理关联的对象。 
 现在修改一下添加Article的代码，先做Picture的持久化操作。 



 再看结果，发现Picture已经被持久化，再次查询该Article时，相应的Picture也全部查了出来。 
 如果在Article里删除关联的list，set为null并保存，系统只会删掉Article里关联的list，而Picture对象本身的数据是不会被删除的。 
 从上面看来，貌似DBRef比较鸡肋，而且甚至有时还会带着误导的性质，譬如Article关联了两个空的Picture时在Article还能看到2个对象的引用，然后2个对象并不存在，是查询不出来的。 
 那么这个标签存在的意义何在？官方的说法是： 
 In short,the best time to use DBRefs are when you’re storing heterogeneous references to documents in different collections.like when you want to take advantage of some additional DBRef-specific 
  functionality in a driver or tool. 
 实际使用中，感觉貌似作用是在不同的表做划分吧，有点模拟mysql外键的意思。免得数据都落到一个大表的，不便于做关联的表的查询。





https://cloud.tencent.com/developer/article/1384034