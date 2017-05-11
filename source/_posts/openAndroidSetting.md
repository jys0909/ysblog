---
title: ReactNative 打开手机WiFi设置
date: 2017-05-11 14:03:16
tags: reactNative
categories: reactNative
---

### 安装包
```
npm install react-native-android-settings-library --save
```

### 配置
1. 打开 `android/settings.gradle` 添加如下代码
```
include ':react-native-android-settings-library'
project(':react-native-android-settings-library').projectDir = new File(rootProject.projectDir, 	'../node_modules/react-native-android-settings-library/android')
```

2. 打开 `android/app/build.gradle` 添加如下代码
```
dependencies {
    // add here
    compile project(':react-native-android-settings-library')
}

```

3. 打开 `MainApplication.java` 添加如下代码;
```
import com.reactlibrary.androidsettings.RNANAndroidSettingsLibraryPackage;

// 主体
new RNANAndroidSettingsLibraryPackage()
```


### 引入包
```
import RNANAndroidSettingsLibrary from 'react-native-android-settings-library';

RNANAndroidSettingsLibrary.open('ACTION_WIFI_SETTINGS');

```
open参数如下：
- ACTION_SETTINGS
- ACTION_WIRELESS_SETTINGS
- ACTION_AIRPLANE_MODE_SETTINGS
- ACTION_WIFI_SETTINGS // 打开wifi 开关
- ACTION_APN_SETTINGS
- ACTION_BLUETOOTH_SETTINGS
- ACTION_DATE_SETTINGS
- ACTION_LOCALE_SETTINGS
- ACTION_INPUT_METHOD_SETTINGS
- ACTION_DISPLAY_SETTINGS
- ACTION_SECURITY_SETTINGS
- ACTION_LOCATION_SOURCE_SETTINGS
- ACTION_INTERNAL_STORAGE_SETTINGS
- ACTION_MEMORY_CARD_SETTINGS
- ACTION_APPLICATION_DETAILS_SETTINGS

