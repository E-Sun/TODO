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
Logger日志库：https://github.com/orhanobut/logger  
奔溃日志收集：http://www.testplus.cn  
运行ADB模式（需ROOT权限）  
```java
/**
 * 运行ADB模式
 * @return true已进入ADB模式，false无法运行ADB模式
 */
private boolean runADBModel() {
    LogUtils.i("运行ADB模式");
    try {
        Runtime runtime = Runtime.getRuntime();
        runtime.exec("su");
        runtime.exec("setprop service.adb.tcp.port 5555");
        runtime.exec("stop adbd");
        runtime.exec("start adbd");
    } catch (IOException e) {
        e.printStackTrace();
        LogUtils.e(e.getMessage());
        return false;
    }
    return true;
}
```
TTS模块
```java
private TextToSpeech mTTS;// 文字转语音模块
/**
 * 初始化TTS
 */
private void initTTS() {
    LogUtils.i("初始化TTS");
    mTTS = new TextToSpeech(this, new TextToSpeech.OnInitListener() {
        @Override
        public void onInit(int status) {
            if (status == TextToSpeech.SUCCESS) {
                int result = mTTS.setLanguage(Locale.CHINA);
                if (result == TextToSpeech.LANG_MISSING_DATA || result == TextToSpeech.LANG_NOT_SUPPORTED) {
                    LogUtils.e(getString(R.string.error_tts_unavailable));
                    mTTS.setLanguage(Locale.US);
                }
            }
        }
    });
    mTTS.setPitch(1.0f);
    mTTS.setSpeechRate(1.0f);
}
/**
 * 关闭TTS
 */
private void shutdownTTS() {
    if (mTTS != null) {
        LogUtils.i("关闭TTS");
        mTTS.shutdown();
        mTTS = null;
    }
}
/**
 * 播报语音
 * @param content 语音内容
 */
public void speak(String content) {
    if (TextUtils.isEmpty(content)) {
        return;
    }
    LogUtils.i("播报语音\n" + content);
    if (mTTS == null) {
        LogUtils.e(getString(R.string.error_tts_unavailable));
        return;
    }
    mTTS.speak(content, TextToSpeech.QUEUE_ADD, null, null);
}

/**
 * 停止播报语音
 */
public void stopSpeak() {
    if (mTTS != null) {
        mTTS.stop();
    }
}
```
