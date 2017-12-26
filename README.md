
意见反馈SDK说明
==========================

## 修改记录

版本号  |   修改日期    |   修改者  |   修改内容
--------|---------------|-----------|-------------------------
v1.0.0  |   2017-12-26  |   庄冬冬  |   创建文档


# 目录
[1 功能描述](#功能描述)
[2 集成流程](#集成流程)
&nbsp;&nbsp;&nbsp;&nbsp;[2.1 前期准备](#前期准备)
&nbsp;&nbsp;&nbsp;&nbsp;[2.2 导入aar](#导入aar)
&nbsp;&nbsp;&nbsp;&nbsp;[2.3 配置 AndroidManifest.xml 文件](#%E9%85%8D%E7%BD%AEandroidmanifestxml%E6%96%87%E4%BB%B6) 
[3 接入流程](#接入流程)  
&nbsp;&nbsp;&nbsp;&nbsp;[3.1 初始化](#初始化)
&nbsp;&nbsp;&nbsp;&nbsp;[3.2 打开意见反馈页面](#打开意见反馈页面)
&nbsp;&nbsp;&nbsp;&nbsp;[3.3 请求是否有新回复](#请求是否有新回复)


## 功能描述
意见反馈 SDK ， 主要包括常见问题分类浏览和反馈消息发送两个部分 ，支持文字与图片两种形式的反馈消息发送

## 集成流程
### 前期准备
除游拍客户端外，其他应用需申请接入时需要的 `AppKey` 和 `SecrectKey`

### 导入aar
在 Android Studio 工程下，将 aar 文件放入 libs 文件夹下， 修改在主项目 app 文件夹下的 build.gradle，添加以下配置
``` 
repositories {  
    flatDir {  
        dirs 'libs'  
    }  
} 
```
然后，在 dependencies 内添加
``` 
compile(name: 'feedback-release-1.0.0', ext: 'aar')  
```

### 配置AndroidManifest.xml文件
- 添加所需的权限
``` xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.LOCAL_MAC_ADDRESS"/>
    <uses-permission android:name="android.permission.CAMERA"/>
```

- 解决FileProvider冲突
若接入 SDK 的项目使用了 FileProvider， 为了解决与 SDK 的冲突， 需要在 `<provider>` 标签下增加一行配置， `tools:replace="authorities"`， 示例如下
``` xml
    <provider
        android:name="android.support.v4.content.FileProvider"
        android:authorities="${applicationId}.provider"
        android:exported="false"
        android:grantUriPermissions="true"
        tools:replace="authorities">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/file_paths"/>
    </provider>
```

## 接入流程
### 初始化
推荐在进入意见反馈页面前才进行初始化。
```java
    // 意见反馈SDK配置
    FeedbackConfig config = new FeedbackConfig.Builder()
        .setAppKey("申请的AppKey")  // 除游拍客户端，其他应用必须配置
        .setSecrectKey("申请的SecrectKey") // 除游拍客户端，其他应用必须配置
        //.setServerType(ServerType.TEST)  // 服务器环境，默认线上
        //.setNickName("昵称")  // 用户昵称
        //.setUid("12354546546")  // 用户uid
        //.setVersionInfo("1.1.0.0")  // 客户端版本号
        //.setChannelId("4399Game")  // 客户端渠道
        //.setActiveId("131245")  // 活动id，用于游拍
        //.setActiveName("活动名")  // 活动名，用于游拍
        .build();
        
    // 传入配置进行初始化    
    FeedbackManager.getInstance().init(config);
```
上述注释掉的配置均为非必要配置， 根据接入方需求配置相应参数。
其中，`ActiveId` 与 `ActiveName` 的配置是为了对游拍反馈来源进行区分，其他应用无须配置。
`AppKey` 与 `SecrectKey` 默认为游拍客户端申请的 Key, 其他应用必须配置。

服务器环境说明

* ServerType.T2 : T2环境
* ServerType.TEST ： TEST环境
* ServerType.OT ： OT环境
* ServerType.ONLINE ： 线上环境（默认）

### 打开意见反馈页面
初始化完成后， 通过以下方法进入意见反馈页面
```java
   // 1、进入意见反馈主页面，即常见问题与反馈页面
   FeedbackManager.getInstance().openFeedbackMainPage(context);
   
   // 2、直接进入意见反馈消息记录页面
   FeedbackManager.getInstance().openFeedback(context);
```

### 请求是否有新回复
一般在 app 启动时或其他需要的地方调用，示例
```java
   FeedbackManager.getInstance().requestNewReply(context, new OnReplyListener()
   {
       @Override
       public void onReply(boolean hasNewReply)
       {
           if (hasNewReply)
           {
               // 有新回复
           }
           else
           {
               // 无新回复
           }
       }
   });
```
其中，方法 `onReply(boolean hasNewReply)` 在主线程中回调



