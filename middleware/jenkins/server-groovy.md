```groovy
#!groovy

remoteServerIp = "172.27.0.2"
// 服务名称
jarBaseDirName = "xxx-xxx"
// JAR文件名称
jarFileName = "xxx-xxx.jar"
// 远程地址
remoteServerAddr = "ubuntu@${remoteServerIp}"
// 远程服务目录
remoteServerPath = "/home/ubuntu/xxx/xxx-xxx/"
// 重启脚本
restartShell = "restart.sh"
// gitlab地址，建议选择ssh协议的地址
gitUrl = "xxx.git"
// 代码分支
branch = "master"
// 拉取代码使用的账号，凭据管理
credentialsId = "xxx"
// 钉钉群消息推送的地址
dingTalkUrl = "https://oapi.dingtalk.com/robot/send?access_token=xxx"
// 编译结果是否成功
buildResult = "SUCCESS"

node {
    try {
        timestamps {
            stage("Checkout") {
                checkout([$class                           : "GitSCM",
                          branches                         : [[name: "*/" + branch]],
                          doGenerateSubmoduleConfigurations: false,
                          extensions                       : [],
                          submoduleCfg                     : [],
                          userRemoteConfigs                : [[credentialsId: credentialsId, url: gitUrl]]])
                echo "checkout success"
            }
            stage("Package") {
                sh "mvn clean package -U"
                echo "package success"
            }
            stage("Transfer") {
                sh "ssh " + remoteServerAddr + " " + "mkdir -p " + remoteServerPath
                sh "scp " + jarBaseDirName + "/target/" + jarFileName + " " + remoteServerAddr + ":" + remoteServerPath
                sh "scp " + "scripts/" + restartShell + " " + remoteServerAddr + ":" + remoteServerPath
                echo "transfer file success"
            }
            stage("Restart") {
                sh "ssh " + remoteServerAddr + " 'cd " + remoteServerPath + "&&chmod a+x ./" + restartShell + "&& ./" + restartShell + " dev 0'"
                echo "restart success"
            }
        }
    } catch (e) {
        // 记录为编译失败
        buildResult = 'FAILURE'
        echo "Build failed"
        echo e.toString()
        throw e
    }
    finally {
        // 发送消息到钉钉
        stage('Send Message') {
            // 组装消息内容
            def ret = getGitChanges()
            // 成功的图片
            def picUrl = "http://icons.iconarchive.com/icons/paomedia/small-n-flat/1024/sign-check-icon.png"
            if (buildResult != 'SUCCESS') {
                // 失败的图片
                picUrl = "http://www.iconsdb.com/icons/preview/soylent-red/x-mark-3-xxl.png"
            }
            def msgData = """
            {
                "msgtype": "link",
                "link": {
                     "title":"${BUILD_TAG}  ${buildResult}",
                     "text": "$ret",
                     "picUrl": "$picUrl",
                     "messageUrl": "${BUILD_URL}"
                 }
            }
            """
            // 发送消息到钉钉群
            def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON_UTF8', httpMode: 'POST', requestBody: msgData, url: dingTalkUrl
        }
    }
}

@NonCPS
def getGitChanges() {
    def ret = ""
    def changeLogSets = currentBuild.rawBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            ret += "Branch: ${branch} \nAuthor: ${entry.author} \nComment: ${entry.msg} \n"
        }
    }
    return ret
}
```

