#!groovy
@Library('jenkinslib') _

def tools = new org.devops.tools()

String workspace = "/Users/zhanghaifeng/zhf/study/jenkins/workspace"
pipeline {
    agent { node {  label "master"
                    customWorkspace "${workspace}"
            }
    }
    parameters { string (name: 'DEPLOY_ENV', defaultValue: 'staging', description: '')}
    options {
        timestamps() // 日志会有时间
        skipDefaultCheckout() // 删除隐式checkout scm语句
        disableConcurrentBuilds() // 禁止并行
        timeout(time: 1, unit: 'HOURS') // 流水线超时设置1h
    }
    stages {
        // 下载代码
        stage("GetCode") { // 阶段名称
            when {  environment name: 'test', value: 'abcd'}
            steps { // 步骤
                timeout(time:20, unit:"MINUTES") { // 步骤超时时间
                    script{ // 填写运行代码
                        println('获取代码')
                        println("${test}")
                        input id: 'Test', message: '我们是否要继续？', ok: '是', parameters: [choice(choices: ['a', 'b'], name: 'test1')], submitter: 'admin,zhf'
                    }
                }
            }
            
        }
        stage("01"){
            // 第一个失败，后面也失败
            failFast true
            parallel{
                // 构建
                stage("Build") {
                    steps {
                        timeout(time:20, unit:"MINUTES") {
                            script{
                                println('应用打包')
                                mvnHome = tool "maven3.6.2"
                                println(mvnHome)
                                sh "${mvnHome}/bin/mvn --version"
                            }
                        }
                    }
                }
                // 代码扫描
                stage("CodeScan") {
                    steps {
                        timeout(time:20, unit:"MINUTES") {
                            script{
                                println('代码扫描')
                                tools.PrintMes("test libs", 0)
                            }
                        }
                    }
                }
            }
        }
        
    }
    post {
        always {
            script{
                println("always")
            }
        }
        success {
            script{
                currentBuild.description == "\n 构建成功！"
            }
        }
        failure {
            script{
                currentBuild.description == "\n 构建失败！"
            }
        }
        aborted {
            script{
                currentBuild.description == "\n 构建取消！"
            }
        }
    }
}
