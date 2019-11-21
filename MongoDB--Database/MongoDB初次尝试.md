# MongoDB初步尝试   
#### 作者:coresu   
#### 时间:2019-11-14   



MongoDB数据库以易用性和在海量数据下的良好表现著称，在功能和复杂性之间取得了很好的平衡，将任务大大简化。具备支撑web主流应用的关键功能：索引、复制、分片、丰富的查询语法，灵活的数据模型。 
【注意】mongoDB数据库虽然支持JavaScript语法，但是确实使用C语言写成的，而不是由JavaScript（js）写成的！

##### 丰富的数据模型
* 丰富的数据模型 MongoDB的基本思路就是讲原来的”行“（row）的概念转换为更加灵活的”文档“（document）
* MongoDB没有模式schema，文档的键不会实现定义也不会固定不便。

##### 高扩展性   
随着数据量的增大，app所需要存储的数据量不断增长，想在T级的数据很常见。此时，便面临该如何扩展他的数据库，一般有两种选择升级或扩展。

* 升级意味着买更好的机器，但是价格昂贵
* 扩展意味着把数据分散到很多机器上，扩展更加经济且可以持续横向扩充。   

##### 丰富的功能   

* 索引
* 存储JavaScript
* 聚合
* 固定集合
* 文件存储

##### 卓越的性能   
MongoDB性能问题是重要目标，采用自己的传输协议与服务其进行交互，比其他协议有更大的开销（HTTP/REST）   


##### 简便的管理  
MongoDB尽力让服务器自制简化数据库管理。在集群环境下MongoDB会尽可能让服务器自动集成、自动配置。   

##### 安装mongoDB
为便于学习mongo采用了简便的docker安装，docker采用了容器技术将每个应用的运行环境独立了起来。喜欢的朋友可以了解一下docker技术。

```
docker run --name mymongo -d -p 27017:27017  mongo --auth
```
使用docker运行一个mongo以mymongo为名字的容器。


```
docker exec -it mymongo mongo admin
```
进入到容器内部操作mongo。

```
db.createUser({ user: 'message', pwd: 'hubhub', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] })
```
为mongo配置一个管理用户。   





### 未完待续。。。













