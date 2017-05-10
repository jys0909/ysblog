---
title: 搭建开发环境
date: 2017-05-10 16:56:29
tags: reactNative
categories: reactNative
---
zd### 1、安装必须的软件
Chocolatey是一个Windows上的包管理器，类似于linux上的yum和 apt-get。 你可以在其官方网站上查看具体的使用说明。一般的安装步骤应该是下面这样
```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

### 2、安装Python 2
安装了Chocolatey后就可以使用下面的命令了
```
choco install python2
```
注意目前不支持Python 3版本。

### 3、Node
Node环境是必须的，使用稳定版本，不要使用cnpm加速，可用如下加速镜像
```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

### 4、Yarn、React Native的命令行工具（react-native-cli）
Yarn是Facebook提供的替代npm的工具，可以加速node模块的下载。React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。
```
npm install -g yarn react-native-cli
```
安装完yarn后同理也要设置镜像源：
```
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

### 5、Android Studio
> 它不是必须的，主要是为了使用里面的SDK, 版本2.0以上
Android Studio需要Java Development Kit [JDK]
1.8或更高版本。你可以在命令行中输入 javac
-version来查看你当前安装的JDK版本。如果版本不合要求，则
可以到 [官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)上下载。


### 6、ANDROID_HOME环境变量
确保ANDROID_HOME环境变量正确地指向了你安装的Android SDK的路径。

打开控制面板 -> 系统和安全 -> 系统 -> 高级系统设置 -> 高级 -> 环境变量 -> 新建

![](https://nj01ct01.baidupcs.com/file/637e2a92aad5846d4f6b4eb70040b5e6?bkt=p3-1400637e2a92aad5846d4f6b4eb70040b5e61611869c0000000177d2&fid=1979867769-250528-141015104959765&time=1494407649&sign=FDTAXGERLBHS-DCb740ccc5511e5e8fedcff06b081203-YHl6kW10uqahhR%2BGh36VeoSrFew%3D&to=63&size=96210&sta_dx=96210&sta_cs=0&sta_ft=png&sta_ct=1&sta_mt=1&fm2=MH,Yangquan,Netizen-anywhere,,chongqing,ct&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=1400637e2a92aad5846d4f6b4eb70040b5e61611869c0000000177d2&sl=68747342&expires=8h&rt=pr&r=646792758&mlogid=3008530563370993115&vuk=1979867769&vbdid=2154786405&fin=01.png&fn=01.png&rtype=1&iv=0&dp-logid=3008530563370993115&dp-callid=0.1.1&hps=1&csl=198&csign=eG3iVTR0aVF4iUXm05ly5yGZreU%3D&by=themis)


### 7、配置Android Studio 参数

打开SDK Manager ， 配置如下几个参数
![](https://nj01ct01.baidupcs.com/file/1d955686195398d771c745de2eb50280?bkt=p3-14001d955686195398d771c745de2eb50280509797e5000000020905&fid=1979867769-250528-1000173850200426&time=1494407716&sign=FDTAXGERLBHS-DCb740ccc5511e5e8fedcff06b081203-OOO6cgO92fs%2BOBNdOkF2a2EKs8k%3D&to=63&size=133381&sta_dx=133381&sta_cs=0&sta_ft=png&sta_ct=1&sta_mt=1&fm2=MH,Yangquan,Netizen-anywhere,,chongqing,ct&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=14001d955686195398d771c745de2eb50280509797e5000000020905&sl=83034191&expires=8h&rt=pr&r=352051014&mlogid=3008548389652492840&vuk=1979867769&vbdid=2154786405&fin=02.png&fn=02.png&rtype=1&iv=0&dp-logid=3008548389652492840&dp-callid=0.1.1&hps=1&csl=300&csign=iKaTqNuw3o2bUlmt%2BFCaEPfYq4A%3D&by=themis)
![](https://nj01ct01.baidupcs.com/file/30e4d27a09e1ab7db1539d73d9e139fa?bkt=p3-140030e4d27a09e1ab7db1539d73d9e139fabd71b37d000000012557&fid=1979867769-250528-294736820030105&time=1494407699&sign=FDTAXGERLBHS-DCb740ccc5511e5e8fedcff06b081203-l4JCNpWMsYoqKZi63lOxZVvuh1A%3D&to=63&size=75095&sta_dx=75095&sta_cs=0&sta_ft=png&sta_ct=1&sta_mt=1&fm2=MH,Yangquan,Netizen-anywhere,,chongqing,ct&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=140030e4d27a09e1ab7db1539d73d9e139fabd71b37d000000012557&sl=68747342&expires=8h&rt=pr&r=237522945&mlogid=3008544038235193483&vuk=1979867769&vbdid=2154786405&fin=03.png&fn=03.png&rtype=1&iv=0&dp-logid=3008544038235193483&dp-callid=0.1.1&hps=1&csl=198&csign=eG3iVTR0aVF4iUXm05ly5yGZreU%3D&by=themis)
![](https://nj01ct01.baidupcs.com/file/f0a282194618d00aa7706466996e18e9?bkt=p3-1400f0a282194618d00aa7706466996e18e9c54e852900000001857d&fid=1979867769-250528-443093520888608&time=1494407733&sign=FDTAXGERLBHS-DCb740ccc5511e5e8fedcff06b081203-rDvYU6KQIbliRCORN47fqgTDhI8%3D&to=63&size=99709&sta_dx=99709&sta_cs=0&sta_ft=png&sta_ct=1&sta_mt=1&fm2=MH,Yangquan,Netizen-anywhere,,chongqing,ct&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=1400f0a282194618d00aa7706466996e18e9c54e852900000001857d&sl=83034191&expires=8h&rt=pr&r=256416776&mlogid=3008553000848704118&vuk=1979867769&vbdid=2154786405&fin=04.png&fn=04.png&rtype=1&iv=0&dp-logid=3008553000848704118&dp-callid=0.1.1&hps=1&csl=300&csign=iKaTqNuw3o2bUlmt%2BFCaEPfYq4A%3D&by=themis)

### 8、安装模拟器 Genymotion
比起Android Studio自带的原装模拟器，Genymotion是一个性能更好的选择，但它只对个人用户免费。
> 1. 下载和安装[Genymotion](https://www.genymotion.com/download)（genymotion需要依赖VirtualBox虚拟机，下载选项中提供了包含VirtualBox和不包含的选项，请按需选择）。
> 2. 打开Genymotion。如果你还没有安装VirtualBox，则此时会提示你安装。
> 3. 创建一个新模拟器并启动。
> 4. 启动React Native应用后，可以按下F1来打开开发者菜单。

选择合适的下载安装


### 9、测试安装程序
先打开模拟器， 当然使用真机也是可以的。真机的话需要打开USB调试模式
```
react-native init HelloWord
cd HelloWord
react-native run-android
```
使用` adb devices ` 查看当前设备