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
    1) 数据库结构，包含五张表：
    
   a. User表
  
  字段名称 | 字段类型 | 字段说明 | 约束 |
  ---|--- | --- | ---
  id | int | 用户id |PRIMARY KEY、AUTO_INCREMENT
  name | varchar(50) |用户名 | NOT NULL, UNIQUE
  password | varchar(50) | 用户密码 |NOT NULL
  state | enum('online','offline') |当前登录状态 | DEFAULT 'offline'
    
    
    
  
### 1. include

# 2. 系统基本信息介绍
## 网络服务基于muduo网络库实现

## 编译
执行./build.sh



