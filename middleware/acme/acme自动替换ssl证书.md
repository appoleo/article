#### 简介

acme.sh是github的开源项目，项目地址：https://github.com/acmesh-official/acme.sh 

该项目实现了从letsencrypt生成免费的证书并且将证书替换到nginx、Apache等服务器

 #### 安装

1.命令安装 

```shell
shell curl  https://get.acme.sh | sh
```

命令安装可能会失败，建议通过git安装

2.通过git安装 

```shell
shell git clone https://github.com/acmesh-official/acme.sh.git
cd ./acme.sh
./acme.sh --install
```

并创建 一个 bash 的 alias, 方便你的使用:  

```shell alias acme.sh=~/.acme.sh/acme.sh
alias acme.sh=~/.acme.sh/acme.sh
```

 安装完成后可以通过命令 `acme.sh --help`查看帮助文档

#### 生成证书

证书生成的过程中需要验证证书、官方文档提供了多个验证证书的方法，在此建议通过http方式验证

```shell
acme.sh --install-cert -d test-control.innjoysmart.com \
--key-file        /home/ubuntu/certificate/control.key  \
--fullchain-file /home/ubuntu/certificate/control.pem \
--reloadcmd     "sudo nginx -s reload"
```

-d：域名

--key-file：nginx ssl 证书指向地址

--fullchain-file：nginx ssl 证书指向地址

#### 更新证书

目前证书在 60 天以后会自动更新, 你无需任何操作. 今后有可能会缩短这个时间, 不过都是自动的, 你不用关心. 

#### 更新acme.sh

目前由于 acme 协议和 letsencrypt CA 都在频繁的更新, 因此 acme.sh 也经常更新以保持同步. 

```shell
shell acme.sh --upgrade
```

