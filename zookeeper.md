paxos site:douban.com

百度搜索：关键字 site:网址

只从该域名下搜索关键字

# zookeeper认知

## 认知

- 不要把zookeeper当数据库用
- zookeeper分布式协调、扩展、可靠性、时序性
- 客户端可以随便链接任意一个server,但是写操作会被转移到leader上执行，读操作任意follower上都可以执行
- zookeeper状态
  - 可用状态 
  - 不可用状态（leader挂掉） 

- zookeeper是一个目录树结构，每个节点1MB。节点分为持久节点和临时节点（session）
- 临时节点（session）可以作为分布式锁，redis做分布式锁需要设置过期时间，并且需要单独监控线程监控锁的状态。zookeeper临时节点不需要。
- leader下线，follower选举新leader
- zookeeper特性
  - 顺序一致性：一台服务器上消息a在消息b前发布，则在所有的Server上消息a都将在消息b前被发布
  - 原子性：要么成功要么失败没有中间状态
  - 单系统镜像：无论客户端连到哪个服务器，客户端都看到相同的服务视图
  - 可靠性：一旦服务端成功的应用了某一个事务，并完成对客户端的响应，那么该事务所引起的服务端状态变更将会一直保留下来，除非有另一个事务对其进行了改变
  - 及时性 ：系统的客户视图保证在特定时间范围内是最新的
- zookeeper集群
  - 扩展性
    - 角色：leader、follower、observer
    - 读写分离，leader可写、follower可查
    - 只有follower能参与选举，observer没有投票权、observer放大查询能力
  - 可靠性
    - 快速恢复leader
    - 数据可靠、可用、一致性
      - 选举新leader，同步数据过程中，节点不对外提供服务
  - 选举新leader条件
    - Zxid最新（最大）
    - myid大的
  - 三台才可以构成集群，产生选举
  - ZK选举过程
    - 3888端口造成两两通信
    - 只要任何人投票，都会出发那个准leader发起自己的投票
    - 推选制：先比较zxid，再比较myid

## 用途

- 统一配置管理 ：每个节点1M数据
- 分组管理 ：path结构
- 统一命名 ：sequential
- 同步：临时节点
- 分布式锁 临时节点；锁依托一个父节点具备-s代表父节点下可以有很多把锁（需要客户端自己实现代码逻辑） 

## paxos

paxos：分布式一致性算法，基于消息传递的一致性算法。

## ZAB

zab(原子广播协议)：作用在可用状态，有leader时。

## watch(监控)



## API

## callback

 # 安装Zookeeper

## 安装四节点server

- 确保安装了JDK

- 下载安装包tar格式

- 解压

- 移动到安装目录

-  拷贝配置文件zoo_sample.cfg  zoo.cfg

- 修改zoo.cfg参数

  - dataDir 最好不要用虚拟目录

  - 增加集群选举配置 2888leader接收write请求、3888选主投票用

    ```properties
    server.1=node1:2888:3888
    server.2=node2:2888:3888
    server.3=node3:2888:3888
    server.4=node4:2888:3888
    
    //可以配置观察者observer
    server.4=node4:2888:3888:observer
    ```

- cd dataDir 

- vi myid 输入1-4编号

- 分发到其他机器 注意创建dataDir目录和修改myid值

  ```shell
  #分发文件到其他机器
  scp -r 目录  shell名：'pwd'
  ```

- 修改配置文件 /etc/profile

  ```shell
  export ZOOKEEPER_HOME=安装目录
  export PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin
  ```

- source /etc/profile 

- 启动 zkServer.sh start-foreground （start-foreground前台阻塞启动）可以查看日志

## 启动客户端、测试

- zkCli.sh 启动客户端

- help查看支持的命令

- 测试

  ```shell
  //创建节点 每个节点可存1MB数据 二进制安全
  create /ooxx "" 
  // 获取节点数据
  get /ooxx
  //设置节点数据
  set /ooxx "hello"
  //创建临时节点 依托session 
  create -e /tempooxx "data"
  //防止覆盖
  create -s /tempooxxx "data"
  ```

  - cZxid：当前事务id
  - mZxid：修改事务id
  - pZxid：该节点的子节点列表最后一次被修改时的时间，子节点内容变更不会变更pZxid
  - ephemeralOwner:节点归属 临时节点时指向的sessionid

  ```shell
  //查询当前节点和其他节点连接状态
  netstat -natp | egrep '(2888|3888)'
  ```



# 



