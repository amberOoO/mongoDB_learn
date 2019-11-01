# chapter 2 创建、更新、删除文档

### 3.1 插入、保存文档

### 3.2 删除文档

①

```javascript
db.user.remove()	//删除所有的user集合下的文档，但不删除集合本身
db.mailling.list.remove({"opt-out":true})	//删除所有opt-out为true的人
```

删除为永久性的，不能删除

②删除速度

```javascript
//删除所有集合下的元素和之间删除集合的速度差异十分巨大
//一个个删除
db = Connection().foo
collection = db.bar
collection.remove()

//直接删除整个集合
db.drop_collection("bar")
```

### 3.4 更新文档

#### 3.3.1 文档替换

```javascript
var joe = db.users.findOne({"name":"joe"});
joe.relationships = {"friends": joe.friends}
db.users.update({"name":"joe"}, joe)
```

这种情况得_id值互不相同

#### 3.3.2 使用修改器

```javascript
//根据前边的查询定位器，将查到的文档中的pageviews键+increasNum
db.collection.update({"identifier": "value"}, {"$inc": {"pageviews":increaseNum}})
```

1. $set，$unset修改器

```javascript
//区分下边两种例子
//将名字为joe的作者的年龄修改为18
db.coll.update({"author.name": "joe"}, {"$set":{"author.age": 18}})
//将名字为joe的文档替换为{"author.age": 18}
db.coll.update({"author.name": "joe"}, {"author.age": 18})

```

2. $inc

   只能用于数字类型的数据

```javascript
db.games.update({"game": "pinball", "user": "joe"}, {"$inc": {"score": 50}})
```

3. 数组修改器$push, $addToSet, $pop, $pull
   * push向数组中添加数据
   * addToSet向数组中不重复添加数据
   * pop从两头删除

```javascript
//两种情况
db.coll.update({"name": "joe"}, {"$pop":{key: 1}})//从数组末尾删除，栈式删除
db.coll.update({"name": "joe"}, {"$pop":{key: -1}})//从数组开头删除，队列删除
```

​                pull指定元素删除

4. 数组的定位修改器

   ps：有个定位操作符$

#### 3.3.3 upsert

和普通的insert相比，在没有此元素时，创建后进行相关操作。将查询操作和修改操作变为一个原子操作

将update的第三个参数改为true即可

```javascript
db.analytics.update({"url": "/blog"}, {"$inc": {"visits": 1}}, true)
```

#### 3.3.4 更新多个文档

update默认只更新第一个匹配到的文档，若要全部修改，则需要将update第4个参数改为true

若想知道更新了多少文档，使用getLastError命令可获得

```javascript
db.runCommand({"getLastError": 1})

/*返回
{
	"connectionId" : 11,
	"n" : 0,				//此处n显示修改文档的个数
	"syncMillis" : 0,
	"writtenTo" : null,
	"err" : null,
	"ok" : 1
}

*/
```

#### 3.3.5 返回已经更新的文档

使用findAndModify命令

```javascript
db.runCommand({"findAndModify": "collectionName",
              "query":{"status":"READY"},
              "sort":{"priority": -1},
              "update": {"$set": {"status": "RUNNING"})
```

* 参数列表
  * findAndModify：字符串、集合名字
  * query：文档检索的条件
  * sort：排序结果方式。
  * remove：表示是否删除文档
  * new：表示返回的时更新前还是更新后的文档、

### 3.4 getLastError

mongodb不会抛错，得主动使用getLastError命令才能够得到错误