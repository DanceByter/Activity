[TOC]

#### Task Stack管理Activity

Android系统用任务栈Task Stack管理多个Activity，当我们启动一个应用时，Android系统就会为之创建一个任务栈，然后启动这个应用的主Activity。当栈中无Activity时，系统就会回收任务栈。

出现的问题：当任务栈中有多个Activity时，用户需要点击多次返回才可以把程序退出，用户体验感差；
当栈中的Activity过多时，会造成数据冗余，重复数据太多，导致内存溢出。

为了解决出现的问题，我们引入了Activity的启动模式，每一个Activity都可以设定自己的启动模式。

------

#### Activity的启动模式

##### 1.standred标准模式

每启动一个Activity，系统都会创建一个新的实例（栈顶同样）并放入栈顶

使用场景：大多数情况

##### 2.singleTop栈顶复用模式

如果目标Activity已经在栈顶，不会创建新实例，而是会调用这个Activity的onPause、onNewIntent、onResume方法；如果目标Activity不在栈顶，则会创建实例

##### 3.singleTask栈内复用模式

当打开一个Activity时，系统会首先寻找是否存在所需的任务栈（Activity自己可以通过TaskAffinity参数指定想要的任务栈的名字，默认情况下以应用的包名作为任务栈的名字）

如果任务栈S1中的情况是ABC，此时Activity D以singleTask请求启动，它所需要的任务栈是S2，由于S2和D的实例均不存在，所以系统会先创建任务栈S2，然后创建实例D放到S2的栈顶

如果D所需的任务栈是S1，系统会直接创建D的实例放到S1栈顶

如果D所需的任务栈为S1，但是S1中的情况是ADBC，此时不会创建D的实例，而是把D的实例放到栈顶并调用onNewIntent方法，B和C会全部出栈，相当于clearTop

##### 4.singleInstance单实例模式

singleTask的加强版，具有singleTask的所有特性，并且此种模式下的Activity只能单独存在于一个栈中

加入Activity A是singleInstance模式，当A启动后，系统会创建一个新的任务栈，然后A独自在这个新的任务栈中，由于栈内复用的特性，后续的请求均不会创建新的Activity，除非这个栈被销毁了

------

#### 设置Activity的启动模式

##### 1.在AndroidManifest中设置

```xml
<activity>
  android:launchMode="standred(默认)/singleTop/singleTask/singleInstance"
</activity>
```

##### 2.通过Intent设置标识位

```kotlin
val intent=Intent(ActivityA.this,ActivityB.class)
intent.addFlags(Intent,启动模式)
startActivity(intent)

FLAG_ACTIVITY_SINGLE_TOP：singleTop模式
FLAG_ACTIVITY_NEW_TASK：singleTask模式
FLAG_ACTIVITY_CLEAR_TOP：所有位于其上层的Activity都移除
FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS：具有该标记的Activity不会出现在历史Activity的列表中，故无法通过历史列表回到该Activity上
```

##### 二者的区别

优先级：Intent>Manifest
Manifest无法设定FLAG_ACTIVITY_CLEAR_TOP，Intent无法设置singleInstance模式

------

#### 启动模式的应用场景

##### 1.singleTask

最常见的场景就是希望每个Activity仅有一个实例

##### 2.singleTop

假设在当前的Activity中又要启动该Activity，能够降低Activity的创建，节省内存

##### 3.singleInstance

会涉及到很多情况，比较复杂

##### 4.standred

适用于大多数场景

------

#### onNewIntent(Intent intent)方法

当Activity设置了singleTop/singleTask/singleInstance模式后，会出现Activity复用的情况，此时不会执行onCreate方法，而onCreate方法中会进行页面的数据初始化/UI初始化，假设页面展示的数据与页面跳转传递的参数无关则不必担心该问题，若页面展示的数据是通过getIntent()方法获取的，而getIntent()获取的数据一直都是旧的，根本无法接受跳转时传送的新数据。

这时我们要调用另一个回调方法onNewIntent(Intent intent)，该方法会传入最新的intent，这样我们就能够解决数据更新问题。这里建议是再次调用setIntent()以及初始化数据initData()和初始化UI界面initView

```kotlin
override fun onNewIntent(intent:Intent){
  super.onNewIntent(intent)
  setIntent(intent)
  initData()
  initView()
}
```


