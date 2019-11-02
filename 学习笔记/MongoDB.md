##  Mongodb

MongoDB的介绍与安装，安装的默认端口是27017

导入 import pymongo

创建一个数据库

（1）创建数据库需要使用MongoClient()，并且指定连接的URL地址和要创建的数据库名

注意：在M0ngoDB中，数据库只有在内容插入之后才会创建！就是说，数据库创建后要创建集合（数据表）并插入一个文档（记录），数据库才会真正被创建

此处是python与MongoDB的交互

以下是增加和查找数据

```yaml
import pymongo

# 调用MongoClient()对象，并传入参数'mongodb://192.168.1.1:27017'
myclient = pymongo.MongoClient('mongodb://192.168.1.1:27017')

# 创建指定数据库的名字
mydb = myclient['imooc']

# 如果没有插入记录，数据库和表是不会被创建的
mycollection = mydb['pymongo_test']

mycollection.insert_one({'name':'imooc','flag':1,'url':'http://www.imooc.com'})

# 调用insert_many(）插入多条数据
all_list = [
    {'name':'taobo','flag':100,'url':'https://taobao.com'},
    {'name':'zihu','flag':101,'url':'http://zihu.com'},
    {'name':'Github','flag':102,'url':'http://github.com'},
    {'name':'xiaomi','flag':103,'url':'http://xiaomi.com'},
]

# 通过find()方法，返回的是一个游标数据cursour.Cursor
result = my.collection.find()
for item in result:
	print(item)
	
# find_one(),默认返回数据库的第一条记录，一条就是数据不是游标
# 在find(),()里面加上限定条件。前面大括号表示：查询所有；后面大括号表示限定条件
# 0 表示不显示，1 表示显示
result = mycollection.find({},{'_id':0,'name':1,'url':1})

# 查询条件，支持聚合(查询flag大于等于100的记录)
result = mycollection.find({'name':{'$gte':100}})
# 查询条件，支持正则(查询name以 G 开头的记录)
result = mycollection.find({'name':{'$regex':'^G'}})
```

以下是更改和删除数据

```yaml
# 通过update_one来更新数据，第一个参数是查询的(要更改的字段)，第二个的目标字段(改成的)
# update_one() 默认只更改查询到的第一条（找到name以G开头，name改为jd）
mycollection.update_one({'name':{'$regex':'^G'}},{'$set':{'name':'jd'}})

# update_many()更改多条数据(将全集合的name改为imooc，flag改为200)
mycollection.update_many({},{'$set':{'name':'imooc'，'flag':200})

# delete_one()删除一条数据
# mycollection.delete_one({'url':'http://www.imooc.com'})
# for item in mycollection.find():
#     print(item)

# delete_many()删除多条数据
mycollection.delete_many({'url':{'$regex':'http://[zg]'}})
for item in mycollection.find():
    print(item)

# 删除表结构和内容，但是表还存在
mycollection.delete_many({})
```

------

### 1 MongoDB用户管理

```
# 首先进入MongoDB的bin目录下面  执行 mongo 命令
show dbs  #查看当前的数据库
use admin #使用admin数据库
db.createUser({
    user:'admin'  # 创建名为admin的管理员
    pwd:'123456'  # 设置管理员账户密码
    roles:[{role:'root',db:'admin'}]  # 具有root最高权限的账户
})
```

- 以下四个角色的权限仅限于某个逻辑库，不能管理其他逻辑库

| 序号 | 角色      | 权限                     |
| ---- | --------- | ------------------------ |
| 1    | Read      | 允许用户读取指定逻辑库   |
| 2    | readWrite | 允许用户读写指定逻辑库   |
| 3    | dbAdmin   | 可以管理指定的逻辑库     |
| 4    | userAdmin | 可以管理指定逻辑库的用户 |

以下四个角色只能创建在admin逻辑库中，可以管理其他逻辑库

| 序号 | 角色                 | 权限                                                     |
| ---- | -------------------- | -------------------------------------------------------- |
| 1    | readAnyDatabase      | 只可以把用户创建在admin逻辑库 中，允许读取任何逻辑库     |
| 2    | readWriteAnyDatabase | 只可以把用户创建在admin逻辑库 中，允许读取任何逻辑库     |
| 3    | dbAdminAnyDatabase   | 只可以把用户创建在admin逻辑库 中，允许管理任何逻辑库     |
| 4    | userAdminAnyDatabase | 只可以把用户创建在admin逻辑库 中，允许管理任何逻辑库用户 |

### 2 MongoDB的数据结构

- MongoDB用BSON(二进制json)来保存数据。一条记录就是一个BSON，被称作文档（Docement）
- 某些BSON聚集在一起，就形成了集合（Collection）,类似于mysql中的表

2.1 管理逻辑库

- 创建/切换逻辑库     use test  
- 查看逻辑库 show dbs
- 删除逻辑库  db.dropDatabase()

2.2 管理集合

- 创建集合    db.createCollection('student')
- 查看集合    show collections
- 删除集合     db.student.drop()

- 查看集合记录数量    db.student.count()
-  查看数据空间数量    db.student.dataSize()
- 重命名集合               db.student.renameCollection('stu')



```yaml 
# 2.3 添加记录
db.student.save([
    {name:'李亮',sex:'男',birthday:'1998-04-02'},
    {name:'纪树青',sex:'男',birthday:'未知'},
    {name:'曹伟宇',sex:'男',birthday:'未知'},
])

# 2.4  主键值
- 在集合中，文档都是松散的，没有统一的字段约束。为了标识文档的唯一性，MongoDB为每一个文档都添加了主键字段（_id）  与mysql的（id） 类似
- Objectid是一个12字节的BSON类型字符串

# 2.5   时间戳
- MongoDB存储日期会自动转换成格林尼治时区
-Objectid字段里面包含了时间戳，所以我们可以提取出记录保存的时间
Objectid('----时间戳数值----').getTimestamp()

# 2.6 查询记录
find函数可以从集合中提取记录,参数为查询条件
db.student.find()   # 查询集合student中的所有记录
db.student.find({name:'李亮'})
db.student.find({sex:'女',age:{$gte:20}})  # 查询集合student中性别为女，年龄大于20岁

# 表达式用 $ 开头表达  $in包括  $nin不包括   $ne不等于
$all全部（字段为元组形式）    $not取反   $or或(两个条件的或运算)    $exists含有某字段 1
```

分页查找数据

```yaml
分页查找数据
# 可以利用skip()和limit()实现分页查询
# skip 起始位置  limt 偏移量
db.student.find().limit(10)
db.student.find().skip(20).limit(10)

# sort()函数可以用来对结果集排序   1代表升序，-1代表降序
db.student.find().sort({name:1})  
sb.student.find().sort(name:-1).skip(5).limit(10)  # 名字降序之后从第6个开始取，取10个

# distinct()函数代替find()函数查找不重复的记录
db.student.distinct('name')  # 查找name字段不重复的值
# 查找name字段不重复的记录  然后倒序(得到的结果集为单个字段的数组)
db.student.distinct('name').sort(function(){return -1})
# 查找name字段不重复的记录，取前五个
db.student.distinct('name').slice(0,5)
```

修改记录 （增，删） 

```yaml
1 update()和updateMany()函数
# db.collection.updateMany({ condition },{$set:{ data }})
# 修改name为李强的记录 age改为26，classno改为2-6
db.student.update({name:'李强'},{$set:{age:26,classno:'2-6'}})
# 找到sex为男，age大于25的记录。classno改为2-6
db.student.updateMany({sex:'男',age:{$gte:25}},{$set:{classno:'2-6'}})

2 $unset:可以删除记录中的字段
# 删除student集合中 city，tel字段
db.student.updateMany({},{$unset:{city:1,tel:1}})

3 $inc: 对某个字段做加法运算
# 对年龄字段做age +2 运算
db.student.updateMany({},{$inc:{age:2}})

4 $push:可以向数组属性添加元素
  $pull:可以删除数组属性的元素
  # 给name为jack添加role属性 教务主任
db.teacher.update({name:'Jack'},{$push:{role:'教务主任'}})
  # 给name为jack删除role属性 副校长
db.eacher.update({name:'Jack'},{$pull:{role:'副校长'}})

5 remove()函数可以删除记录
db.student.remove({})  #删除集合内容，结构。此时为空集合
db.student.remove({class:'6-2',sex:'男'})   
 
```

### 3 索引机制

1. (原则)  数量很大的集合必须创建索引，相反则不需要创建索引
2. ​	   集合的数据读多过写，则需要创建索引
3. ​	   给经常被当做查询条件的字段设置索引

因为MongoDB中存放了大量的数据，所以为了加快数据检索速度，需要为集合设置索引

db.collection.createIndex({ keys:1}, options )

因为创建索引的过程会阻塞MongoDB，影响其他增删改查操作，所以background参数代表在空闲的时候创建索引

```
db.student.createIndex({name:1})
db.student.dropIndexes()
db.student.createIndex({name:1},{background:true})
db.student.getIndexes()

```

3.1  唯一性索引

唯一性索引只能创建在每个记录都含有的公共字段上，在非公共字段上是不能创建唯一性索引的

创建sid字段，并且不能重复才能创建索引

db.student.createIndex({ sid:1 },{background:true, unique:true })

### 4 数据导入导出

```
# mongoexport命令可以把一个集合的数据导出成json或者csv格式的文件
# josn文件包较大，可读性较强；csv文件包小，可读性差

mongoexport --host=localhost --port=27017 -u admin -p 123456 --authenticationDatabase=admin -d school -c student -f '选择导出集合中的哪些字段'
-o D:/data/student.json

# mongoimport命令可以把json或者csv文件的数据导入到某个集合当中
mongoimport --host=localhost --port=27017 -u admin -p 123456 --
authenticationDatabase=admin -d test -c student -file D:/data/student/json

# 导出某个逻辑库
mongodump --host=localhost --port=27017 -u admin -p 123456 --
authenticationDatabase=admin -d test -o D:/data

#导入某个逻辑库的数据
mongorestore --host=localhost --port=27017 -u admin -p 123456 --
authenticationDatabase=admin --drop -d test -o D:/data
```

