# 001 - 从0到开始编写Redis客户端

### 说在前面的话

redis火得一塌糊涂，它的应用场景非常广泛。做为一名IT工程师，做为攻城狮的我们，若不深入了解它,那么我们跟咸鱼有什么区别，在这里让我们一起走进
redis客户端发现之旅</br>
Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。

### 官方文档

https://redis.io

### 初步了解RESP协议

RESP协议是Redis客户端和服务端之间的通讯协议</br>
史上最详细的协议文档 https://redis.io/topics/protocol

### 协议说明

1、客户端和服务端通过TCP进行数据交互
2、 传输的数据遵循RESP协议，如下：
每一段数据通过 进行分隔，每段数据通过一个特殊标志作为开头
* 代表数组
$ 多行字符串
+ 单行信息
- 错误信息
: 整型数字
### 程序代码如下(java版)
5类：
Connection.java  连接
Constant.java    常量
JiBaoJedisClient.java  客户端
Protocol.java 协议
TestJiBaoJedis.java  测试

##### Connection.java

##### Constant.java

##### JiBaoJedisClient.java

##### Protocol.java

##### TestJiBaoJedis.java





