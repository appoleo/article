摘自：https://github.com/alibaba/canal/wiki/QuickStart

------

## **canal 服务端的安装部署**



- 对于自建 MySQL , 需要先开启 Binlog 写入功能，配置 binlog-format 为 ROW 模式，my.cnf 中配置如下

  `[mysqld] log-bin=mysql-bin # 开启 binlog binlog-format=ROW # 选择 ROW 模式 server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复`

  注意：针对阿里云 RDS for MySQL , 默认打开了 binlog , 并且账号默认具有 binlog dump 权限 , 不需要任何权限或者 binlog 设置,可以直接跳过这一步

- 授权 canal 链接 MySQL 账号具有作为 MySQL slave 的权限, 如果已有账户可直接 grant

  `CREATE USER canal IDENTIFIED BY 'canal';  GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%'; -- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ; FLUSH PRIVILEGES;`

- 下载 canal, 访问 [release 页面](https://github.com/alibaba/canal/releases), 选择需要的包下载, 如以 1.0.17 版本为例

  `wget https://github.com/alibaba/canal/releases/download/canal-1.0.17/canal.deployer-1.1.4.tar.gz`

- 解压缩

  `mkdir /tmp/canal tar zxvf canal.deployer-$version.tar.gz  -C /tmp/canal`

- 修改配置

  `vi conf/example/instance.properties # table meta tsdb info canal.instance.tsdb.enable=true canal.instance.tsdb.url=jdbc:mysql://127.0.0.1:3306/mysql canal.instance.tsdb.dbUsername=root canal.instance.tsdb.dbPassword=root`

- 启动
  执行bin目录下的脚本文件启动、停止服务

## **canal 客户端的安装部署**

- 添加依赖

  `<dependency> `

  ​    `<groupId>com.alibaba.otter</groupId>    `

  ​    `<artifactId>canal.client</artifactId>    `

  ​	`<version>1.1.0</version> `

  `</dependency>`

- 创建链接，获取变更数据

  `// 创建链接（port和destination等参数和canal的服务端配置保持一致）`

  `/tmp/canal/conf CanalConnector connector = CanalConnectors.newSingleConnector(new InetSocketAddress("10.7.16.105", 11111), "example", "canal", "canal"); `

  `// 打开链接 `

   `connector.connect(); `

  `// 订阅消息，指定订阅的数据库表，可通配(".*\\..*")，双反斜杠之前为库名，之后为表名 connector.subscribe("test\\..*"); `

  `// 回滚到未进行 {@link #ack} 的地方，下次fetch的时候，可以从最后一个没有 {@link #ack} 的地方开始拿 connector.rollback(); `

  `// 收到消息 `

  `Message message = connector.getWithoutAck(batchSize); `

  `// 本次消息的唯一id `

   `long batchId = message.getId(); `

  `// 提交确认 `

  `connector.ack(batchId);`

  

  