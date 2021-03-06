尽管很消极，但是程序的crash是很难被避免的。如果能够知道程序crash的原因，就可以修复问题。

在发生crash时，应该提供一个更友好的界面让程序重新再启动，同时也应该存在一个错误上报的机制收集错误日志帮助我们修复问题。

Android提供了处理这类问题的方法，当crash发生时，系统会调用`UncaughtExceptionHandler`的`uncaughtException`方法。

### 实现Thread.UncaughtExceptionHandler接口拦截异常全局异常
```java
public class CrashHandler implements UncaughtExceptionHandler {
    private static CrashHandler instance;

    public static CrashHandler getInstance() {
        if (instance == null) {
            instance = new CrashHandler();
        }
        return instance;
    }

    public void init(Context ctx) {
        Thread.setDefaultUncaughtExceptionHandler(this);
    }

    /**
     * 核心方法，当程序crash 会回调此方法， Throwable中存放这错误日志
     */
    @Override
    public void uncaughtException(Thread arg0, Throwable arg1) {
        String logPath;
        if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            logPath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + File.separator + "log";
            File file = new File(logPath);
            if (!file.exists()) {
                file.mkdirs();
            }
            try {
                FileWriter fw = new FileWriter(logPath + File.separator + "errorlog.log", true);
                fw.write(new Date() + "\n");
                // 错误信息
                // 这里还可以加上当前的系统版本，机型型号 等等信息
                StackTraceElement[] stackTrace = arg1.getStackTrace();
                fw.write(arg1.getMessage() + "\n");
                for (int i = 0; i < stackTrace.length; i++) {
                    fw.write("file:" + stackTrace[i].getFileName() + " class:" + stackTrace[i].getClassName() + " method:" + stackTrace[i].getMethodName() + " line:" + stackTrace[i].getLineNumber() + "\n");
                }
                fw.write("\n");
                fw.close();
                // 上传错误信息到服务器
                // uploadToServer();
            } catch (IOException e) {
                Log.e("crash handler", "load file failed...", e.getCause());
            }
        }
        arg1.printStackTrace();
        android.os.Process.killProcess(android.os.Process.myPid());
    }
}
```

### 在Application初始化时设置给线程
```java
CrashHandler crashHandler = CrashHandler.getInstance(); 
crashHandler.init(getApplicationContext());
```
