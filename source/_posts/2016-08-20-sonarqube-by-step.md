---
title: 代码质量管理SonarQube
date: 2016-08-20 22:23:48
category: Platforms
tags: [Code Quality, Sonar Qube]
description: SonarQube是一个开源的代码质量管理平台。
published: true
---

## Introduction
SonarQube是一个开源的代码质量管理平台，能够快速分析并定位代码中明显或潜在错误信息。目前支持[20+种语言](http://docs.sonarqube.org/display/PLUG/Plugin+Library)，并且有很多[插件Plugins](http://docs.sonarqube.org/display/PLUG/SonarSource+Plugins)可以集成。接下来将大致讲解SonarQube的安装、配置及使用。

## Installation
以下所有操作均在CentOS下进行，其他系统的过程基本类似，只需要替换成对应命令即可，假设CentOS分配的IP地址为`192.168.56.105`。

#### Install the SonarQube
首先到[SonarQube Download](http://www.sonarqube.org/downloads/)页面下载压缩包，然后解压到`~/sonarqube`文件夹，最后运行命令启动服务，需要Java8运行环境支持。
```
$ ~/sonarqube/bin/linux-x86-64/sonar.sh start  # 启动SonarQube服务

Usage: ./sonar.sh { console | start | stop | restart | status | dump }
```

然后访问[http://192.168.56.105:9000](http://192.168.56.105:9000)将能够看到SonarQube页面，这里需要替换成你的IP地址。
![](/assets/sonarqube-by-step/sonarqube_home.png)

其中，默认的管理员登录用户名和密码为：
* Login: `admin`
* Password: `admin`

虽然可以访问了，但当前SonarQube使用的是Embedded数据库，只能用于评估目的，不支持扩展和升级，更不支持数据迁移，因此强烈建议安装一款数据库引擎，不过别着急，稍候会涉及到配置Mysql的操作过程。

#### Install the Scanner
为了实现扫描项目代码并上传到SonarQube Server的目的，需要再到[SonarQube Scanner Download](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)页面下载压缩包并解压，如解压到`~/sonar-scanner`。
```
$ ~/sonar-scanner/bin/sonar-scanner --version

INFO: Scanner configuration file: ~/sonar-scanner/conf/sonar-scanner.properties
INFO: Project root configuration file: NONE
INFO: SonarQube Scanner 2.6.1
INFO: Java 1.8.0_66 Oracle Corporation (64-bit)
INFO: Linux 3.10.0-327.18.2.el7.x86_64 amd64
```

## Configuration
#### Config Database





## Analyzing with Scanner
#### Runner
通常可以直接运行Runner实现对支持语言的项目代码进行扫描。假设Sonar Scanner被解压到`~/sonar-scanner`文件夹中，随意找一个项目代码，并在其中添加`sonar-project.properties`配置文件，针对Java项目的配置如下：
```
# Must be unique in a given SonarQube instance
sonar.projectKey=twee:qas-service

# This is the name displayed in the SonarQube UI
sonar.projectName=QAS Service
sonar.projectVersion=1.0

# Comma-separated paths to directories with sources
# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
# Since SonarQube 4.2, this property is optional if sonar.modules is set. 
# If not set, SonarQube starts looking for source code from the directory containing 
# the sonar-project.properties file.
sonar.sources=src

# Language
sonar.language=java

# Encoding of the source code. Default is default system encoding
sonar.sourceEncoding=UTF-8
```

如果暂时自己没有适合的代码，也可以使用官方提供的[Project Samples](https://github.com/SonarSource/sonar-examples/archive/master.zip)，然后在项目代码目录下运行如下命令进行Sonar Scanner扫描代码。
```
~/sonar-scanner/bin/sonar-scanner  # 当然也可以直接把bin目录加入环境变量中
```

#### Gradle
SonarQube支持Gradle 2.0以上的版本，以下来将配置`build.gradle`文件，使得支持SonarQube任务。
```
// Uses DSL plugins resolution introduced in Gradle 2.1
plugins {
  id "org.sonarqube" version "2.0.1"
}

sonarqube {
    properties {
        property "sonar.projectName", "QAS Service"
        property "sonar.projectKey", "tw.wee:qas-service"
        property "sonar.jacoco.reportPath", "${project.buildDir}/jacoco/test.exec"
    }
}
```


SonarQube扫描还可以与Jenkins集成，有兴趣可以参阅[Analyzing with SonarQube Scanner for Jenkins](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Jenkins)实现步骤。

----
References
[SonarQube官网](http://www.sonarqube.org/)
[Installing the Server](http://docs.sonarqube.org/display/SONAR/Installing+the+Server)
[Analyzing with SonarQube Scanner](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)
[Analyzing with SonarQube Scanner for Gradle](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Gradle)


