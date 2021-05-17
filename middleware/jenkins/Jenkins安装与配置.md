#### 1. 安装jenkins

```shell
sudo apt-get update
sudo apt-get install jenkins
```

配置jenkins依赖的java

```shell
sudo vim /etc/init.d/jenkins 
PATH=/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/lib/jdk1.8.0_101/bin
```

#### 2. 启动jenkins

```shell
sudo systemctl daemon-reload
sudo systemctl restart jenkins
sudo systemctl status jenkins
```

#### 3. 安装插件

查看密码

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

访问jenkins网页

[http://localhost:8080](http://148.70.174.40:8080/)

选择推荐的插件，使用admin用户

Extended choice paramter & Http Request & role base auth

#### 4. 配置凭证与ssh

拉取代码平台与发包服务器配置jenkins用户的公钥

```shell
sudo su - jenkins
ssh-keygen
vim .ssh/id_rsa.pub
```

#### 5. 配置java与maven

配置jdk环境与maven环境

配置maven配置文件settings.xml，添加访问仓库的用户名与密码

```xml
sudo vim /usr/local/lib/apache-maven-3.6.3/conf/settings.xml 
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    
    <pluginGroups>
        <pluginGroup>com.your.plugins</pluginGroup>
        <pluginGroup>maven-eclipse-plugin</pluginGroup>
    </pluginGroups>

    <proxies>
    </proxies>

    <servers>
        <server>
            <id>xxx</id>
            <username>xxx</username>
            <password>xxx</password>
        </server>
    </servers>

    <mirrors>
    </mirrors>
    
    <profiles>
    </profiles>

    <activeProfiles>
        <activeProfile>xxx</activeProfile>
        <activeProfile>nexus</activeProfile>
    </activeProfiles>
</settings>
```

