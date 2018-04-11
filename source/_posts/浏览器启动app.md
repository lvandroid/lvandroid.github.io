---
title: 浏览器启动app
date: 2017-06-02 
tags:
---

##  配置manifest.xml

    <activity android:name=".MainActivity">
      <!-- 正常启动 -->
      <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
      </intent-filter>
      <!--URL Scheme启动-->
      <intent-filter>
        <!-- 必有项 -->
        <action android:name="android.intent.action.VIEW"/>
        <!-- 应用可以通过浏览器的链接启动-->
        <category android:name="android.intent.category.BROWSABLE"/>
        <!--可以被隐式调用(必须加)-->
        <category android:name="android.intent.category.DEFAULT"/>
        <!-- 协议部分 -->
        <data android:host="za_auth_activity"
              android:scheme="zaurlscheme"/>
      </intent-filter>
    </activity>


## 启动App

    /**
     * App内启动
     */

    Uri uri = Uri.parse("zaurlscheme://za_auth_activity");
    Intent intent = new Intent(Intent.ACTION_VIEW, uri);
    //保证新启动的app有单独的堆栈，如果希望启动的app和原有的app同一个堆栈则不用写
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    try{
        startActivityForResult(intent,RESULT_OK);
      }catch(Exception e){
        e.printStackTrace();
        Toast.makeText(MainActivity.this,"没有匹配的APP，请下载安装",Toast.LENGTH_SHORT).show();
    }

    <!-- 网页启动 -->
    <a href="zaurlschemel://za_auth_activity">打开新的应用</a>

    <!--  js调用-->

    window.location = "zaurlschemel://za_auth_activity";


> 判断URL Scheme是否有效

    boolean checkUrlScheme(Intent intent){
      PackageManager packageManager = getPackageManager();
      List<ResolveInfo> activities = packageManager.queryIntentActivities(intent, 0);
      return !activities.isEmpty();
    }
