1.用户在设备的homescreen（主屏幕）或者App Deawer（应用抽屉）上点击应用图标，触发启动过程

2.设备上的Launcher应用（用户看到的homescreen管理器）会创建一个Intent对象。这个Intent对象通常包含ACTION_MAIN动作和CATEGORY_LAUNCHER类别，表示它是一个启动应用的Intent。

3.Lanucher应用会将这个Intent传递给Android的PackageManager服务。PackageManager负责管理设备上所有已安装的应用和它们的组件

4.PackageManager服务会解析应用的AndroidMainfest.xml文件，查找与Intent对象匹配的
**<intent-filter>**，具体来说它会寻找包含`<action android:name="android.intent.action.MAIN" />`和`<category android:name="android.intent.category.LAUNCHER" />`的Activity标签

5.PackageManager找到与Intent匹配的Activity后，就会确定这个Activity作为应用的入口，即主Activity

6.系统为即将启动的Activity创建一个新的任务栈Task，如果应用已经在运行，并且主Activity已经在任务栈中，系统会将这个任务栈Task带到前台，不会去创建新的

7.系统通过调用主Activity的onCreate方法来创建它的实例，然后依次调用onStart和onResume方法，使得Activity进入运行态，显示在屏幕上供用户交互

8.最终用户看到的主Activity界面，表示应用成功启动