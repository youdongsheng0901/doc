# elasticsearch

## 初识elasticsearch

### 倒排索引

​	利用ik分词，根据分词建立索引。例如{"name":"机械智能手表"}，分词为“机械”、“智能”、“手表”，然后建立类似

机械：{"name":"机械智能手表"}

智能：{"name":"机械智能手表"}

手表：{"name":"机械智能手表"}

这样的关系。

### 存储概念

- index 相当于mysql 《db》
- type 相当于mysql 《table》
- Document 相当于mysql 《row》
- Field 相当于mysql 《field》

### 分片

​	es默认五个分片（shard），可以在搭建时设置，但是设置之后不能修改。数据平均分配到分片上，所有分片一起构成整体全量数据。

​	分片P表示，副本分片R表示，搭建集群时，P和对应的所有副本分片R不能在一台机器上。避免机器挂掉，不能提供完整数据服务。