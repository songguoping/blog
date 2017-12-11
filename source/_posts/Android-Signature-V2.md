---
title: Android Signature V2 下的多渠道打包
date: 2017-12-04 19:49:51
description: 本文主要解决 v2 签名方案下的多渠道打包问题，利用 Walle 批量打可以自定义文件名的 apk
tags: 
    Android
---
## 相关背景
Android7.0 引入一项新的应用签名方案 APK Signnature Scheme v2(以下简称v2),这种新机制使得 apk 的签名方式更加安全，默认情况下,Android Studio2.2 和 Gradle2.2会使用v2签名方案来签名apk。如果使用v2这套签名方案就使得之前用的美团点评多渠道打包方案没用了，当然，美团点评在此基础上推出了新的解决方案 Walle。

## 需求
* 客户端需要传一个 Channel 字段给服务器；
* 在发包时需要使用360加固或者其他的加固方式给 apk 加固；
* apk命名需要自定义；
* 批量打包；

<!-- more -->
## 解决方案
### 使用 v1 的签名方案
禁用掉 v2，使用 v1。打开**模块级 build.gradle** 文件，配置如下： 
```java
android{
    signingConfigs{
        release{
             storeFile file("releasekey.keystore")
             storePassword "password"
             keyAlias "ReleaseKey"
             keyPassword "password"
             v2SigningEnabled false
        }
    }
}
```
### 使用 v2 的签名方案
之前的美团点评的多渠道打包方案无效，我们使用新的解决方案 [Walle](https://github.com/Meituan-Dianping/walle)。如果不使用加固方案的话，直接按照 Walle 的 readme 操作即可，但是使用加固方案后，只能先加固，后签名，在通过 Walle 对这个签名后的 apk 进行多渠道打包，具体操作如下：
1. 先在项目中引用 Walle
    在位于**项目的根目录 build.gradle** 文件中添加 Walle Gradle 插件的依赖
    ```java
    buildscript{
        dependencies{
            classpath 'com.meituan.android.walle:plugin:1.1.5'
        }
    }
    ```
    并在**模块级 build.gradle** 文件中apply这个插件，并添加上用于读取渠道号的 AAR
    ```java
    apply plugin: 'walle'
        
    dependencies {
        compile 'com.meituan.android.walle:library:1.1.5'
    }
    ```
    通过以下方式获取渠道信息，并上传给服务器
    ```java
    ChannelInfo channelInfo= WalleChannelReader.getChannelInfo(this.getApplicationContext());
    if (channelInfo != null) {
        String channel = channelInfo.getChannel();
    }
    ```
    至此，在项目中的配置就算完成了，如果想插入一些额外的信息或者要对 Walle 进行更多的配置，请参考 Walle 的 readme。配置完成后，通过 assembleRelease 命令生成使用v2签名方式的 apk。

2. 拿这个 apk 去加固**生成未签名的加固包**。之后使用python工具去批量打自定义名称的渠道包。

    **channel.txt**：渠道信息

    **config.py**：配置签名信息以及 Android sdk 路径。
        ```
        #Windows 下路径分割线请注意使用\\转义
        keystorePath = ""
        keyAlias = ""
        keystorePassword = ""
        keyPassword = ""
        
        #Android SDK buidtools path , please use above 25.0+
        sdkBuildToolPath = ""
        ```
    **MultiChannelBuildTool.py**：基本思路就是遍历放置在同文件夹内的 apk 文件(将生成的未签名的加固包放置此处)。使用 v2 签名该 apk，然后将签名成功的 apk 通过 Walle 的命令行工具写入渠道等信息。
    ```
    #!/usr/bin/python  
    # -*-coding:utf-8-*-
    import zipfile
    import shutil
    import os
    import sys
    import platform
    import config
    
    
    # 获取脚本文件的当前路径
    def curFileDir():
        # 获取脚本路径
        path = sys.path[0]
        # 判断为脚本文件还是py2exe编译后的文件，
        # 如果是脚本文件，则返回的是脚本的目录，
        # 如果是编译后的文件，则返回的是编译后的文件路径
        if os.path.isdir(path):
            return path
        elif os.path.isfile(path):
            return os.path.dirname(path)
    
    
    # 判断当前系统
    def isWindows():
        sysstr = platform.system()
        if ("Windows" in sysstr):
            return 1
        else:
            return 0
    
    
    # 兼容不同系统的路径分隔符
    def getBackslash():
        if (isWindows() == 1):
            return "\\"
        else:
            return "/"
    
    
    # 获取当前目录中所有的apk源包
    src_apks = []
    # python3 : os.listdir()即可，这里使用兼容Python2的os.listdir('.')
    for file in os.listdir('.'):
        if os.path.isfile(file):
            if ".apk" in file:
                src_apks.append(file)
    
    # 获取渠道列表
    channel_file = 'channel.txt'
    f = open(channel_file)
    lines = f.readlines()
    f.close()
    
    # 当前脚本文件所在目录
    parentPath = curFileDir() + getBackslash()
    # config
    libPath = parentPath + "lib" + getBackslash()
    buildToolsPath = config.sdkBuildToolPath + getBackslash()
    checkAndroidV2SignaturePath = libPath + "CheckAndroidV2Signature.jar"
    walleChannelWritterPath = libPath + "walle-cli-all.jar"
    keystorePath = config.keystorePath
    keyAlias = config.keyAlias
    keystorePassword = config.keystorePassword
    keyPassword = config.keyPassword
    
    for src_apk in src_apks:
        # 原包路径
        protectedSourceApkPath = parentPath + src_apk

    zipalignedApkPath = protectedSourceApkPath[0: -4] + "_aligned.apk"
    signedApkPath = zipalignedApkPath[0: -4] + "_signed.apk"

    # 对齐
    zipalignShell = buildToolsPath + "zipalign -v 4 " + protectedSourceApkPath + " " + zipalignedApkPath
    os.system(zipalignShell)

    # 签名
    signShell = buildToolsPath + "apksigner sign --ks " + keystorePath + " --ks-key-alias " + keyAlias + " --ks-pass pass:" + keystorePassword + " --key-pass pass:" + keyPassword + " --out " + signedApkPath + " " + zipalignedApkPath
    os.system(signShell)

    # 检查V2签名是否正确
    checkV2Shell = "java -jar " + checkAndroidV2SignaturePath + " " + signedApkPath
    os.system(checkV2Shell)

    # file name (with extension)
    src_apk_file_name = os.path.basename(src_apk)
    # 分割文件名与后缀
    temp_list = os.path.splitext(src_apk_file_name)
    # name without extension
    src_apk_name = temp_list[0]
    # 后缀名".apk "
    src_apk_extension = temp_list[1]
    # 创建生成目录,与文件名相关
    output_dir = src_apk_name + 'android_' + "1-{channels}".format(channels=len(lines)) + '/'
    # 目录不存在则创建
    if not os.path.exists(output_dir):
        os.mkdir(output_dir)

    # 遍历渠道号并创建对应渠道号的apk文件
    for line in lines:
        # 获取当前渠道号，因为从渠道文件中获得带有\n,所有strip一下
        target_channel = line.strip()
        # 拼接对应渠道号的apk
        target_apk = output_dir + src_apk_name + src_apk_extension
        # 将签名后的apk拷贝建立新apk
        shutil.copy(signedApkPath, target_apk)
        writeChannelShell = "java -jar " + walleChannelWritterPath + " put -c " + target_channel + " " + target_apk
        os.system(writeChannelShell)
    ```
    
    
## 参考
>Walle: https://github.com/Meituan-Dianping/walle
>Jay-Goo: https://github.com/Jay-Goo/ProtectedApkResignerForWalle

    
    
    
    





