# # 代码混淆

###基本配置
```java
-optimizationpasses 5           #指定代码的压缩级别
-dontusemixedcaseclassnames     #是否使用大小写混合
-dontpreverify                  #混淆时是否做预校验
-verbose                        #混淆时是否记录日志
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/*        #混淆时所采用的算法
-ignorewarnings
```

###Android不能被混淆的内容
```java
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class com.android.vending.licensing.ILicensingService
-keep class **.R$** { *; }
```
###保持枚举类型
```java
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}
```

###序列化不被混淆
```java
-keep class * implements android.os.Parcelable { # 保持 Parcelable 不被混淆
    public static final android.os.Parcelable$Creator *;
}
```
###注解
```java
-keepattributes *Annotation*
-keep class * extends java.lang.annotation.Annotation { *; }
```
###自定义widget
```java
-keepclasseswithmembers class * {
    public <init>(android.content.Context);
}

-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int, int);
}
```
##其他第三方类混淆

###百度地图
```java
-keep class com.baidu.** {*; }
-keep class assets.** {*; }
```

###Rx
```java
-keep class rx.android.** { *; }
-keep class rx.** { *; }
```

###ButterKnife
```java
-keep class butterknife.** { *; }
-dontwarn butterknife.internal.**
-keep class **$$ViewBinder { *; }
-keepclasseswithmembernames class * {
    @butterknife.* <fields>;
}
-keepclasseswithmembernames class * {
    @butterknife.* <methods>;
}
```
###Retrofit2
```java
-dontwarn retrofit2.**
-keep class retrofit2.** { *; }
```
###GreenDao
```java
-keepclassmembers class * extends org.greenrobot.greendao.AbstractDao {
    public static java.lang.String TABLENAME;
}
-keep class **$Properties
-keep class com.baonahao.parents.api.dao.** { *; }
```

