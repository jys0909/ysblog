---
title: reactNative 热更新方法
date: 2017-05-13 12:48:01
tags: reactNative
categories: reactNative
---
### 1、安装code-push
```
npm install -g code-push-cli

```

新用户`code-push register`会要求用户登录github账户或 微软账户、登录成功后会返回一个key, 输入这个key的值
> 相关命令:
> - code-push login 登陆 
> - code-push loout 注销 
> - code-push access-key ls 列出登陆的token
> - code-push access-key rm <accessKye> 删除某个 key值


### 2、在CodePush上注册app
```
code-push app add <appName>
```
返回如下表格
Name | Deployment Key
---|---
Production | SeGH3j6xEzTU54TWEDMbmFVkJuTRE1IE1U50M
Staging |  VfzbGk_YDjTyV8DOtGR3PE1yQ46VE1IE1U50M

相关命令:
```
add       Add a new app to your account
remove    Remove an app from your account
rm        Remove an app from your account
rename    Rename an existing app
list      Lists the apps associated with your account
ls        Lists the apps associated with your account
transfer  Transfer the ownership of an app to another account

Options:
  -v, --version  Show version number  [boolean]
```
  
### 3、项目中添加code-push
```
npm install --save react-native-code-push@latest
rnpm link react-native-code-push
```

检查 `android/app/build.gradle`, 添加
```
apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
```

### 4、添加部署key
查看部署的key 
```
code-push deployment  ls <appName> -k
```
默认的部署名是 staging，所以部署秘钥就是staging
在`android`目录下新建`local.properties`文件
```
code_push_key_production=SeGH3j6xEzTU54TWEDMbmFVkJuTRE1IE1U50M
code_push_key_staging=VfzbGk_YDjTyV8DOtGR3PE1yQ46VE1IE1U50M
```

配置 `android/app/build.gradle`
```
buildTypes {
    ...
    releaseStaging {
        ...
        buildConfigField "String", "CODEPUSH_KEY", '"'+properties.getProperty("code_push_key_production")+'"'
        ...
    }
     
     release {
        ...
        buildConfigField "String", "CODEPUSH_KEY", '"'+properties.getProperty("code_push_key_staging")+'"'
        ...
    }
}    
      
 ```
更新 `MainApplication.java `文件
```
return Arrays.<ReactPackage>asList(
    new MainReactPackage(),
     new CodePush(BuildConfig.CODEPUSH_KEY, MainApplication.this, BuildConfig.DEBUG)
);
 ```

### 5、修改versionName
在 `android/app/build.gradle` 中有个 `android.defaultConfig.versionName`属性，我们需要把 应用版本改成 1.0.0（默认是1.0，但是codepush需要三位数）

至此Code-Push 集成完毕。
进入 `android`目录执行`gradlew assembleRelease`进行一次打包， 看下是否会存在错误， 若出现
> No signature of method: java.util.LinkedHashMap.getProperty() is applicable for argument types: (java.lang.String) values: 
[code_push_key_production]

是因react-native 不支持`local.properties`文件方式保存，修改`build.gradle`
```
buildTypes {
    releaseStaging {
      buildConfigField "String", "CODEPUSH_KEY", '"SeGH3j6xEzTU54TWEDMbmFVkJuTRE1IE1U50M"'
    }
    release {
        minifyEnabled enableProguardInReleaseBuilds
        proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        signingConfig signingConfigs.release
        buildConfigField "String", "CODEPUSH_KEY", '"VfzbGk_YDjTyV8DOtGR3PE1yQ46VE1IE1U50M"'
    }
}
```    
注意`'"SeGH3j6xEzTU54TWEDMbmFVkJuTRE1IE1U50M"'`中的单引号不能去掉， 否则会报错
```
错误: 找不到符号
  public static final String CODEPUSH_KEY = VfzbGk_YDjTyV8DOtGR3PE1yQ46VE1IE1U50M;
                                            ^
  符号:   变量 VfzbGk_YDjTyV8DOtGR3PE1yQ46VE1IE1U50M
  位置: 类 BuildConfig
```


### 6、添加更新监听
部署完code-push后接下来有两步要做
> 1. 什么时候检查更新 （在APP启动的时候？在设置页面添加一个检查更新按钮？）
> 2. 什么时候可以更新，如何将更新呈现给终端用户？


如果有发布热更新时 mandatory 则 Code Push 会根据 mandatory 是 true 或false 来控制应用是否强制更新。默认情况下 mandatory 为 false 即不强制更新。mandatory 为 false时以下三种设置方法才有效
```
// 第一种:
codePush.sync();

// 第二种:
codePush.sync({
    updateDialog: false,
    installMode: codePush.InstallMode.IMMEDIATE
});

// 第三种:
CodePush.sync({
    deploymentKey: 'deployment-key-here',
    updateDialog: {
        optionalIgnoreButtonLabel: '稍后',
        optionalInstallButtonLabel: '后台更新',
        optionalUpdateMessage: '有新版本了，是否更新？',
        title: '更新提示'
    },
    installMode: CodePush.InstallMode.IMMEDIATE
});
```
三种更新的策略: 配置到installMode: 之后即可生效
- IMMEDIATE 立即更新APP
- ON_NEXT_RESTART 到下一次启动应用时
- ON_NEXT_RESUME 当应用从后台返回时


### 7、打包发布
方法1: 只打包js文件, 
创建一个bundles文件夹
```
打包命令: 
react-native bundle --platform 平台 --entry-file 启动文件 --bundle-output 打包js输出文件 --assets-dest 资源输出目录 --dev 是否调试 
```
eg:

```
react-native bundle --platform android --entry-file index.android.js --bundle-output ./bundles/index.android.bundle --dev false
```

发布更新

发布命令: 
```
ode-push release <应用名称> <Bundles所在目录> <对应的应用版本> --deploymentName 更新环境 --description 更新描述 --mandatory 是否强制更新
```
eg:
```
code-push release DemoApp ./bundles/index.android.bundle 1.0.0 --deploymentName Production --description "第1次更新" --mandatory true
```

方法2： 打包js + 图片资源


```
react-native bundle --platform android --entry-file index.android.js --bundle-output ./bundles/index.android.bundle --assets-dest ./bundles --dev false
```
–assets-dest 后就是放图片的文件夹路径

push bundles文件
```
code-push <release/debug> <projectName(与注册的app同名)><bundle文件名> <版本号>
```
eg : 
```
code-push release appName ./bundles 1.0.0
```


### 8、更新规则
你APP内plist文件写的版本号可能是1.0.0，所以你的reactjs打包上传的版本也要是1.0.0（而不是1.0.1这样递增），你需要和APP保持一致，然后服务器会根据你最新上传的且和APP一样的版本作为最新版。

### 9、修改更新
```
Usage: code-push patch <appName> <deploymentName> [--label <label>] [--description <description>] [--disabled] [--mandatory] [--rollout <rolloutPercentage>]

选项：
  --label, -l           指定标签版本更新，默认最新版本 [string] [默认值: null]
  --description, --des  描述  [string] [默认值: null]
  --disabled, -x        是否禁用该更新  [boolean] [默认值: null]
  --mandatory, -m       是否强制更新  [boolean] [默认值: null]
  --rollout, -r         此更新推送用户的百分比，此值仅可以从先前的值增加。  [string] [默认值: null]

示例：
  code-push patch MyApp Production --des "Updated description" -r 50         修改"MyApp"的"Production"部署中最新更新的描述 ，并且更新推送范围为50％
  code-push patch MyApp Production -l v3 --des "Updated description for v3"  修改"MyApp"的"Production"部署中标签为v3的更新的描述
```

### 10、升级环境
```
Usage: code-push promote <appName> <sourceDeploymentName> <destDeploymentName> [--description <description>] [--mandatory] [--rollout <rolloutPercentage>]

选项：
  --description, --des  描述  [string] [默认值: null]
  --disabled, -x        是否禁用该更新  [boolean] [默认值: null]
  --mandatory, -m       是否强制更新  [boolean] [默认值: null]
  --rollout, -r         此促进更新推送用户的百分比  [string] [默认值: null]

示例：
  code-push promote MyApp Staging Production                                   "MyApp"中"Staging"部署的最新更新发布到"Production"部署中
  code-push promote MyApp Staging Production --des "Production rollout" -r 25  "MyApp"中"Staging"部署的最新更新发布到"Production"部署中, 并且只推送25%的用户
  
  ```
  ### 11、回滚更新
```
Usage: code-push rollback <appName> <deploymentName> [--targetRelease <releaseLabel>]

选项：
  --targetRelease, -r  指定回归到哪个标签，默认是回滚到上一个更新  [string] [默认值: null]

示例：
  code-push rollback MyApp Production                     "MyApp"中"Production"部署执行回滚
  code-push rollback MyApp Production --targetRelease v4  "MyApp"中"Production"部署执行回滚，回滚到v4这个标签版本
  
 ```
 
 > 注： 本博文来自网络
  
  
