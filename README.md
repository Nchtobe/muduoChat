# 1. muduoChat
工作在nginx tcp负载均衡环境中的集群聊天，基于muduo网络库和redis实现

# 2. 项目基本技术介绍
  项目开发遵循“高内聚、低耦合”的思想，将网络层面、业务层面和数据层面的服务分离开来，依据基于对象的方式进行开发。
### 项目介绍：
  1. 网络服务部分：
    底层使用muduo网络库实现：
    1) 包含TcpServer对象和EventLoop对象；
    2) 注册三个事件：因为始使低流量的业务（基于聊天的），没有考虑粘包问题，所以只需要关注服务器的连接、断开和读事件发生的回调函数。setConnectionCallback注册连接和断开的回调函数，setMessageCallback注册读事件发生的会带函数。如果存在粘包问题，就需要通过setWriteCompleteCallback注册相应的回调函数。这都结合muduo网络库来实现；
    3) 设置线程数量，如果不设置，默认是单线程工作。
  2. 数据库操作的类封装：
    1) mysql数据库结构，包含五张表：
    
   a. user表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  id | int | 用户id |PRIMARY KEY、AUTO_INCREMENT
  name | varchar(50) |用户名 | NOT NULL, UNIQUE
  password | varchar(50) | 用户密码 |NOT NULL
  state | enum('online','offline') |当前登录状态 | DEFAULT 'offline'
  
  b. friend表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  userid | int | 用户id |NOT NULL、联合主键
  friendid | int | 好友id | NOT NULL、联合主键
  
  c. allgroup表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  id | int | 组id |PRIMARY KEY、AUTO_INCREMENT
  groupname | varchar(50) |组名称 | NOT NULL, UNIQUE
  groupdesc | varchar(100) | 组功能描述 |DEFAULT ''
  
  d. groupuser表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  groupid | int | 组id |NOT NULL、联合主键
  userid | int |组员id | NOT NULL, UNIQUE
  grouprole | ENUM('creator', 'normal') | 组内角色 |色DEFAULT ‘normal’
  
  e. offlinemessage表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  userid | int | 用户id |NOT NULL
  message | VARCHAR(500) | 离线消息（存储Json字符串） | NOT NULL
  
  2) 数据库类封装
    封装了MySQL类，实现数据的连接，更新和查询操作
    
  3) 数据库操作类封装：
    -User、UserModel：user表映射和表操作的类
    -FriendModel：较为简单，friend操作类
    -OfflineMsgModel：离线消息存储类(当对方用户不在线时，先将发送的消息存储在数据库中，等对方上线后先进行检查，然后清空)
    -Group、GroupModel：针对群组业务表映射和操作的类
    -GroupUser继承自User：针对群组中的用户，增加了其在群组的角色信息
  
  3. 业务实现部分：
  
  chatservice.hpp和chatservice.cpp
  
  类的设计采用单例模式(Singleton Pattern)，保证一个类仅有一个实例，并提供一个访问它的全局访问点，该实例被所有程序模块共享。具体业务有：
  
  1) instance：提供接口，用于获取单例对象
  2) 数据成员：
  
  msgHandlerMap：存储消息和其对应的业务处理方法；
  
  userConnMap：存储用户id和其对应的Tcp连接TcpConnectionPtr
  
  connMutex：互斥锁
  
  针对数据库操作的四个对象：UserModel、OfflineMsgModel、FriendModel和GroupModel对象
  
  对后面集群服务的redis封装类
  
  3) ctor中：采用类似于muduo网络库中事件回调的方式注册针对各种消息的回调函数，当有该消息id的事件到来时，调用对应的函数进行处理
  
    
    
    
  
### 1. include

# 2. 系统基本信息介绍
## 网络服务基于muduo网络库实现

## 编译
执行./build.sh



