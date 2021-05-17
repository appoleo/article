```shell
#!/bin/bash
app_name=xxx-xxx-$profile-SNAPSHOT.jar
dir_path=/home/ubuntu/xxx/xxx-xxx/

pid=`ps -ef | grep $app_name | grep -v grep | awk '{print $2}'`
if [ -n "$pid" ]
then
   echo "kill -9 pid:" $pid
   kill -9 $pid
fi
echo 重启项目...
source /etc/profile
BUILD_ID=DONTKILLME
cd $dir_path
nohup java -Xms200m -Xmx300m -jar -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=x-port $app_name >/dev/null &2>&1  &
echo 重启完成...
```

