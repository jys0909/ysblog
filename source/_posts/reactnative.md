---
title: reactNative 打包测试
date: 2017-05-10 16:45:18
tags:
---
### 1、生成密钥
在项目根目录打开命令行（快捷方法：进入根目录win下按住shift键单击右键打开命令行，直接对应该目录）。然后输入
```
keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

```
按要求填写相关信息后生成的 ==my-release-key.keystore== 文件放到你工程中的==android/app==文件夹下

### 2、修改gradle.properties文件
在android目录下打开gradle.properties文件，添加如下代码：
```
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=*******  // 密钥口令
MYAPP_RELEASE_KEY_PASSWORD=******* // 密钥口令
```

### 3、修改build.gradle文件
在android/app目录下打开build.gradle文件，按如下格式修改:
```
signingConfigs {
    release {
        storeFile file(MYAPP_RELEASE_STORE_FILE)
        storePassword MYAPP_RELEASE_STORE_PASSWORD
        keyAlias MYAPP_RELEASE_KEY_ALIAS
        keyPassword MYAPP_RELEASE_KEY_PASSWORD
    }
}

buildTypes {
    release {
        minifyEnabled enableProguardInReleaseBuilds
        proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        signingConfig signingConfigs.release
    }
}
```

### 4、生成index.android.bundle文件
在android\app\src\main目录下新建名为assets的文件夹，然后在项目根目录的命令行下输入
```
react-native bundle --platform android --dev false --entry-file index.android.js \ --bundle-output android/app/src/main/assets/index.android.bundle \ --assets-dest android/app/src/main/res/
```

### 5、生成APK
在android目录命令行下输入==gradlew assembleRelease==，然后等待几分钟，在==android/app/build/outputs/apk==目录下找到APK文件。

> 注意事项:
> 1. 打包前最好删除app\build文件夹
> 2. src\main 下面的资源不能出现相同名称

### 其他可能出现的问题记录在此
***
1、==unable to process incoming event 'ProcessComplete'==
在/android/目录中执行==gradle assembleRelease==命令 如果出现此类错误，清理下，在==proguard-rules.pro==中加入
```
-keep class android.text {* ;}
-dontwarn android.text.*
```
