---
title: 找回iphone 访问限制密码
date: 2017-06-22 22:50:40
tags: 个人整理
categories: 个人整理
---
最近不小心把 iphone 5s的访问限制密码忘记了， 下面记录找回密码方法:

# 1. 下载 iBackupBot for iTunes
iBackupBot官方版是一个为iPad，iPhone和iPod Touch等苹果设备专用的备份管理器软件。iBackupBot可以直接读取备份文件资料，不论是通讯录、短信内容、通话记录、还是照片，都能在其中找到，你只需选择正确的备份文件然后搜索响应的目录直接就能导入导出，甚至还能恢复游戏记录。
单独恢复通讯录只需用手机连接电脑，在iBackupBot中打开iTunes备份，选中AddressBook.sqlitedb和AddressBookImages.sqlitedb这实际上就是通讯录的数据库文件备份，点击上方恢复到手机就行了，之后手机就如同在itunes下做恢复一样，不同的是你只恢复了通讯录，非常好用。
[点击下载](http://xiazai.xiazaiba.com/Soft/I/iBackupBot_5.4.2_XiaZaiBa.zip?pcid=27567&filename=iBackupBot_5.4.2_XiaZaiBa.zip&downloadtype=xiazaiba_seo)
# 2. 安装iTunes, 
安装好后打开iTunes, 找到资料， 然后进行备份

# 3. 运行iBackupBot
找到 `System Files/HomeDomain/Library/Preferences` 目录下`com.apple.restrictionspassword.plist`文件， 并打开， 找到下面的代码
```
<dict>
    <key>RestrictionsPasswordKey</key>
     <data>
     HxfREedr37oisGRd+lduyLHkw==
     </data>
    <key>RestrictionsPasswordSalt</key>
   <data>
      B7hsGA==
   </data>
  </dict>
```

# 4. 打开解密网站  http://ios7hash.derson.us/
对应输入 RestrictionsPasswordKey 和 RestrictionsPasswordSalt 的值 `HxfREedr37oisGRd+lduyLHkw==`
和 
`B7hsGA==` 
后， 点击 `Search for Code`按钮， 速度很快， 最多20来分钟就可完成解密

# 5. 另外一个方法
在第3步中同一目录中找到`COM.APPLE.SPRINGBOARD.PLIST`文件
打开文件， 找到如下代码：
```
<key>countryCode</key>
　<string>cn</string>
</dict>
```
在这代码下加入下面代码
```
<key>SBParentalControlsPIN</key>
<string>1234</string>
```
1234就是你的新访问限制密码

保存，并退出iBackupBot

然后在`iturns`里恢复刚修改过的备份到你的设备