# chapter 2

### 2.1 文档

* 文档中的键值对是有序的

* 文档中的值

  * 双引号的字符串
  * 可以使用几乎所有的UTF8字符串
  * 键不能含有\0（空字符）。这个字符表示键的结尾
  * .和$有特别的含义，保留字
  * _也是保留字

* 区分类型、区分大小写

* ```javascript
{"foo":"3"}
  {"foo":3}
  {"FOo":3}
  ```
  
* 不能有重复的键

  * ```javascript
    {"greeting":"Hello world", "greeting":"Hello, MongoDB!"}
    ```

### 2.2 集合

集合就是一组文档。

用关系型数据库对比：

​		文档——行/记录

​		集合——表

#### 2.2.1 无模式

* 集合是无模式的——一个集合中的文档可以是各不相同的
* 那为什么要划分不同的集合呢：
  * 各式各样搞得文档混在一个集合里，查询起来会十分复杂，管理也很复杂
  * 在一个集合里，速度上不划算。如文档中有个标注类型的键，按类型分集合显然比在一个集合里查询快
  * 对磁盘寿命有好处
  * 创建索引的时候，文档有附加结构。索引时按照集合来定义的。把同种类型的文档放入同一个集合里边，可以使索引更加有效。

#### 2.2.2 命名

* 集合名字时满足下面条件的任意UTF8字符串
  * 集合名不能是空字符串
  * 集合名不能有空字符（\0）
  * 集合名不能以"system"开头，这是为系统集合保留的前缀。如system.users这个集合保存这数据库的用户信息，system.namespace保留着所有数据库集合的信息
  * 用户创建的名字不能含有保留字$。
* 子集合
  * 为了更好的分层
  * 比如blog.posts和blog.author。posts和author两个集合为blog的子集合，但blog集合其实不需要存在。

### 2.3 数据库

mongodb多个文档组成集合，多个集合则组成数据库。

* 一个mongodb可以有多个数据库，他们之间完全独立。
* 每个数据库都有独立的权限控制，在磁盘上，每个数据库存放在不同的文件中。
* 数据库命名规范
  * 不能时空字符串
  * 不能含有【空格 . $ / \ \0】
  * 全部<strong>小写</strong>
  * 最多64字节
* 保留的数据库名
  * admin——root数据库
  * local
  * config

### 2.4 启动mongodb

### 2.5 MongoDB客户端

#### 2.5.3 基本操作

1、创建

```javascript
//创建文档
post = {"title":"My Blog Post", "content":"Here's my blog content", "date":new Date()}
//插入文档到blog集合
db.blog.insert(post)
//调用集合的fing方法，查看文档
db.blog.find()
```

会发现，新创建的文档中会多出一个_id属性

2、读取

```javascript
db.blog.findOne() //查找一条文档
db.blog.find() //最多自动匹配20个文档
```

3、更新

第一步修改变量post，增加comments键

```javascript
post.comment = []
```

然后执行update操作

```javascript
db.blog.update({title:"My Blog Post"}, post)
```

最后查看可以发现新的键

4、删除

```javascript
db.blog.remove({title:"My Blog Post"})
```

#### 2.5.4 使用shell的窍门

### 2.6 数据类型

#### 2.6.1 基本数据类型

* null
* bool
* 32/64位整数（js只有64位浮点数）、64位浮点数
* 字符串
* 对象id
* 日期
* 正则表达式
* javascript代码
* 二进制数据
* 最大、小值
* 未定义undefined
* 数组
* 内嵌文档

#### 2.6.2 数字

#### 2.6.3 日期

#### 2.6.4 数组

#### 2.6.5 内嵌文档

内嵌文档就是把整个Mongodb文档作为另一个文档中键的一个值。这样数据可以立体化存储。

```javascript
{
    "name":"John Doe",
    "address":{
        "street":"123 Park Street",
        "city":"Anytown",
        "state":"NY"
    }
}
```

这样可以使信息表示更加自然

然而可能会造成很多的重复数据

#### 2.6.6 _id和ObjectId

每个集合里，_id唯一

1. ObjectId

   1. _id的默认类型。这种类型是为分布式设计的。在不同的机器上都能够生成全局唯一的值。使用12字节空间。

   2. ```javascript
      0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
      时间戳　　　　  ｜机器　　　　 |PID 　 |计数器
      ```

2. 自动生成_id

   1. 可以由服务器生成，也可以由客户端进行

