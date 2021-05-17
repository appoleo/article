摘自：https://github.com/alibaba/canal（阿里巴巴日志增量订阅和消费中间件 canal）

​      [https://juejin.im/post/6850037271233331208](https://juejin.im/post/6850037271233331208#heading-41)（mysql精华总结）

------

**canal [kə'næl]**，译意为水道/管道/沟渠，主要用途是基于 mysql 数据库增量日志解析，提供增量数据订阅和消费

早期阿里巴巴因为杭州和美国双机房部署，存在跨机房同步的业务需求，实现方式主要是基于业务 trigger 获取增量变更。从 2010 年开始，业务逐步尝试数据库日志解析获取增量变更进行同步，由此衍生出了大量的数据库增量订阅和消费业务。

基于日志增量订阅和消费的业务包括：

- 数据库镜像
- 数据库实时备份
- 索引构建和实时维护(拆分异构索引、倒排索引等)
- 业务 cache 刷新
- 带业务逻辑的增量数据处理

当前的 canal 支持源端 mysql 版本包括 5.1.x、5.5.x、5.6.x、5.7.x、8.0.x

## **canal 工作原理**

- canal 模拟 mysql slave 与 mysql master 的交互协议，伪装自己是一个 mysql slave，向 mysql master 发送 dump 协议
- mysql master收到 mysql slave（canal）发送的 dump 请求，开始推送 binlog 增量日志给 slave（也就是 canal）
- mysql slave（canal 伪装的）收到 binlog 增量日志后，就可以对这部分日志进行解析，获取主库的结构及数据变更

## **mysql 主备复制原理**



- mysql master 将数据变更写入二进制日志（binary log，其中记录叫做二进制日志事件 binary log events，可以通过 show binlog events 进行查看）
- mysql slave 将 master 的 binary log events 拷贝到它的中继日志（relay log）
- mysql slave 重放 relay log 中事件，将数据变更反映它自己的数据

## **关于 mysql 的日志**

- **错误日志**：记录出错信息，也记录一些警告信息或者正确的信息。
- **查询日志**：记录所有对数据库请求的信息，不论这些请求是否得到了正确的执行。
- **慢查询日志**：设置一个阈值，将运行时间超过该值的所有 SQL 语句都记录到慢查询的日志文件中。
- **二进制日志**：记录对数据库执行更改的所有操作。
- **中继日志**：中继日志也是二进制日志，用来给 slave 库恢复
- **事务日志**：重做日志 redo（实现持久化和原子性） 和回滚日志 undo（实现一致性）