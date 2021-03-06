# 同步与并发

## 同步与异步

### 是什么

在我理解看来, 同步和异步是多个任务间的两种协作方式.

打个比方, 有任务A, B. 这两个任务任务执行的顺序, 是先执行A, 再执行B.

- 同步: 就是A 执行完成之后, 才轮到B 执行, 如果A 执行的时间很长, B就会阻塞住, 一直等到A 执行完了才执行.

- 异步: 就是A 只要一开始执行, 不管A是否执行完, B都立马执行.

### 场景

这种场景在工作中也很常见;

- 同步: 比如在服务端编程中, 经常需要先查询数据库, 再对查询结果做筛选和计算, 这两步是顺序进行的, 只有当从数据库查回数据后, 才进行下一步对数据的操作, 如果sql语句写的很烂, 查询很耗时, 任务就会阻塞住, 知道查回数据后才开始做筛选和计算.

- 异步: 拿我理解的前端开发来说, 当一个页面需要渲染时, 可能会需要先从多个数据源获取信息, 然后把这些数据汇总, 然后才是渲染, 这是数据包括本地缓存的, 多个接口返回的. 这个时候获取数据本身这件事就可以分为好几个步骤

获取本地缓存 -> 调用接口A 获取数据 -> 调用接口 B 获取数据. 如果按照最原始的写法, 依次调用, 等每一步执行完之后执行下一步, 可能在用户看来, 应用就卡在这里了, 因为用这种最原始的方式得到的结果是三个任务累加的时间. 但是完全可以在获取本地缓存的方法一开始调用, 不等待它的返回结果, 立马去调用接口A, 接口B, 彼此间不要等待. 最后他们的结果都返回的时候, 就开始做汇总. 这种情况下需要消耗的时间就远比原先累加的时间要小


## 并发与并行

### 是什么

#### 并发

大学知识:

并发是随着多任务系统的诞生而诞生的.

在单任务系统时代, 操作系统一下只能执行一个任务, 只能干等着任务A执行完毕后, 才能执行任务B.

后来前辈们觉得单任务系统太low, 一下子只做一件事怎么行, 我们要便听歌, 边敲代码. 然后就有大神研究出了多任务系统, 操作系统可以同时做好几件事了, 他是怎么做到的. 其实用的是骗术, cpu其实一下子也只能做一件事, 大神觉得人类对时间不敏感, 有点延时也感觉不到, 于是就把时间一个个很小的时间片, 时间片1 先分给任务A, 等到时间时间片用完了, 就把任务A的状态缓存起来, 然后在时间片2里执行任务B, 然后到时间了给任务C, 然后时间片用完了继续分配.

在操作系统看来, 他是把单核cpu的时间分给各个任务依次执行的, 但是在人类看来, 这些任务其实是妥妥的一块执行了, 因为我们感觉不到系统任务的切换, 在我们看来, 这些任务确实是一起执行的. 

这就是传统意义上的单核并发执行, 按时间片分配的方式依次执行, 也称为串行.

#### 并行

后来硬件慢慢进步, 有了多核计算机, 每个cpu执行一个任务, 多个cpu 可以同时运算, 真正开始实现了多个任务同时执行, 称为并行.

#### 分布式环境下的并发, 并行

并发（concurrency）和并行（parallellism）是：

解释一：并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔发生。
解释二：并行是在不同实体上的多个事件，并发是在同一实体上的多个事件。
解释三：在一台处理器上“同时”处理多个任务，在多台处理器上同时处理多个任务。如hadoop分布式集群

### 场景

#### 并发

多个线程同时查询redis数据库, 对redis数据库而言, 就是并发

#### 并行

单台服务器的多个核心同时处理多个用户请求; 

多台服务器的多个线程同时处理多个用户请求

## 并发并行带来的问题.

由于并行的存在, 就会存在多个任务同时访问并修改同一个资源的情况, 就容易引发问题.

这个资源可能是内存, 可能是文件, 可能是数据库里的某条记录, 可能是某个第三方的服务接口

说几个工作中遇到的问题

### 部署

打个比方说, 向我们经常会部署测试服, 部署这个动作是同时只能有一个人进行的, 如果A 部署了, B想部署, 就必须等A部署完后才能部署, 这个时候部署资源是冲突的, 如果的部署脚本写的不够强壮, 在A 部署的时候B 同时部署, 就会出现问题, 比方说在A 的部署行为中, 正在将构建好的包复制到对应目录, 刚好这个时候在B 的行为中正在将新生成的 包生成到构建目录, 这个时候, A的部署行为就会失败, 因为复制的可能是一个B正在生成的缺失的包. 

### Session 失效

### 用户同时开通多个快捷支付

### 连接池耗尽

## 解决并发问题的手段 - 同步/异步

### 同步锁

#### synchronized 关键字

比如java 的synchronized 关键字, 保证单机环境下某个对象的某一个方法同时只有一个线程在执行

#### 数据库的读锁写锁

在某个执行写操作的, 会先获取写锁, 再做写操作, 再释放锁, 这个过程中是其他客户端是无法获取到锁的, 必须等写锁释放后才能获取到锁, 开始读写.

#### redis实现的同步锁

给予redis实现的同步锁. 关于某些先查询, 再更新的的接口, 要保证分布式环境下该接口同时只能有用户的一个请求在执行, 比如说注册, 开通快捷.

#### 文件锁

### [生产者, 消费者模式](http://www.jianshu.com/p/0d1c950e6614)

游戏排队, 购物排队.

生产者线程

缓冲队列

消费者线程

对消费者而言, 从队列里面获取到任务的执行可以是同步的, 也可以是异步的.

对生产者和消费者两个步骤而言, 彼此之间又是异步的了.

[单用户秒杀限购](http://www.awaimai.com/348.html)

游戏房间排队


### 其他

