```shell
ls -al
ssh ubuntu@xx.xx.xx.xx 'mkdir -p /home/ubuntu/xxx/xxx-xxx/h5'
scp h5.zip ubuntu@xx.xx.xx.xx:/home/ubuntu/xxx/xxx-xxx/h5/
echo "transfer file success"
ssh ubuntu@xx.xx.xx.xx 'cd /home/ubuntu/xxx/xxx/h5/ && unzip h5.zip'
ssh ubuntu@xx.xx.xx.xx 'cp -rf /home/ubuntu/xxx/xxx-xxx/h5/h5/* /home/ubuntu/xxx/xxx-xxx/h5'
ssh ubuntu@xx.xx.xx.xx 'rm -rf /home/ubuntu/xxx/xxx-xxx/h5/h5'
echo "restart success"
curl 'https://oapi.dingtalk.com/robot/send?access_token=xxx' \
   -H 'Content-Type: application/json' \
   -d '
  {
    "msgtype": "link", 
    "link": {
        "title": "xxx项目名称",
        "text": "H5发布成功",
        "picUrl": "http://icons.iconarchive.com/icons/paomedia/small-n-flat/1024/sign-check-icon.png",
        "messageUrl": "可链接到jenkins任务详情"
     }
  }'
```