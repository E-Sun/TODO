# TODO

监听系统开机广播，实现开机自启  
```java
<receiver android:name=".receiver.BootCompletedBroadcastReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
public class BootCompletedBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (TextUtils.equals(intent.getAction(), Intent.ACTION_BOOT_COMPLETED)) {
            LogUtils.i("接收到开机广播");
            context.startActivity(Objects.requireNonNull(context.getPackageManager().getLaunchIntentForPackage(context.getPackageName())).addFlags(Intent.FLAG_ACTIVITY_NEW_TASK));
        }
    }
}
```
监听应用更新广播，实现更新自启  
```java
<!-- 应用更新广播接收器 -->
<receiver android:name=".receiver.PackageReplacedBroadcastReceiver">
    <intent-filter>
        <action android:name="android.intent.action.PACKAGE_REPLACED" />
        <data android:scheme="package" />
    </intent-filter>
</receiver>
public class PackageReplacedBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (Objects.equals(intent.getAction(), Intent.ACTION_PACKAGE_REPLACED)) {
            context.startActivity(Objects.requireNonNull(context.getPackageManager().getLaunchIntentForPackage(context.getPackageName())).addFlags(Intent.FLAG_ACTIVITY_NEW_TASK));
        }
    }
}
```
