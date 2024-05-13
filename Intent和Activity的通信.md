[TOC]

#### Intent简介

Intent是一个消息传递的对象，可以使用Intent对象请求Android系统执行特定操作。

通常可以使用Intent启动Activity/Service/Broadcast，还可以用于在不同组件之间传递数据。

------

#### 使用Intent在Activity之间传递数据

1.创建Intent对象，并指定目标Activity
2.使用putExtra()方法将数据以键值对形式添加到Intent中
3.调用startActivity()方法并将Intent作为参数传递

```kotlin
//假设有一个MainActivity，想要启动一个SecondActivity并在两者之间传递数据

//在MainActivity中
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 当用户点击按钮时，启动SecondActivity
        val button: Button = findViewById(R.id.button)
        button.setOnClickListener {
            val intent = Intent(this, SecondActivity::class.java)
            intent.putExtra("key", "value")
            startActivity(intent)
        }
    }
}

//在SecondActivity中，可以使用onCreate()方法接收这些数据
class SecondActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_second)

        // 获取Intent并从中提取数据
        val intent = intent
        val value = intent.getStringExtra("key")

        // 接收到数据后的处理
        // 例如，将数据设置为TextView的文本
        val textView: TextView = findViewById(R.id.textView)
        textView.text = value
    }
}
```

------

#### 显示Intent和隐式Intent

##### 显示Intent

```kotlin
//显示Intent用于明确指定要启动的Activity，当知道要启动的Activity的名字时，应该使用显示显示Intent

// 创建一个显式Intent
val intent = Intent(this, TargetActivity::class.java)

// 可以添加数据到Intent
intent.putExtra("key", "value")

// 启动Activity
startActivity(intent)
```

##### 隐式Intent

```kotlin
//隐式Intent不会明确指定要启动的Activity，而是声明要执行的操作，让系统去找到合适的Activity来响应该Intent。当想要让其他应用中的Activity响应你的Intent时，应该使用隐式Intent

// 创建一个隐式Intent
val intent = Intent(Intent.ACTION_VIEW)

// 设置数据类型
intent.data = Uri.parse("http://www.example.com")

// 启动Activity
startActivity(intent)

//在上述隐式Intent例子中，我们请求了一个可以查看网页的操作，并且指定了一个网页的URL作为数据，如果有多个Activity可以响应这个Intent，系统会显示一个对话框让用户选择使用哪个应用来打开这个URL
```

##### 隐式Intent的过滤条件

```xml
为了让你的Activity能够响应隐式intent，你需要在AndroidManifest.xml的Activity中指定它可以响应的动作、类别和数据类型。
<activity android:name=".TargetActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="http" />
    </intent-filter>
</activity>
在该例子中，TargetActivity声明了它可以响应VIEW动作，并且可以处理http协议的数据。
```

显示Intent通常用于应用内部Activity之间的通信
隐式Intent常用于启动其他应用或让用户选择如何处理某个操作

------

#### 使用Intent过滤器来响应不同的Intent

在Android应用开发中，`Intent`是一个用于请求某种操作的消息对象。而`Intent`过滤器则是一种机制，允许您的应用组件（如`Activity`、`Service`、`BroadcastReceiver`）声明它们能处理的`Intent`类型。使用`Intent`过滤器，可以让您的应用能够响应不同类型的`Intent`，无论是显式还是隐式的。
以下是使用`Intent`过滤器来响应不同`Intent`的几个步骤：

##### 1. 声明Activity和Intent过滤器
在您的应用的`AndroidManifest.xml`文件中，您可以声明一个`Activity`，并为它添加一个或多个`<intent-filter>`元素，以指定它能够响应的`Intent`类型。
例如，一个用于打开网页的`Activity`可以这样声明：
```xml
<activity android:name=".WebViewActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="http" />
        <data android:scheme="https" />
    </intent-filter>
</activity>
```
这个`Activity`将会响应带有`ACTION_VIEW`动作，并且数据 scheme 为`http`或`https`的`Intent`。
##### 2. 添加Category
`Category`是一个附加信息，可以提供关于`Intent`的额外信息。例如，`CATEGORY_DEFAULT`是一个默认的类别，通常应该包含在所有通过的`Intent`过滤器中。
##### 3. 指定Data
`Data`元素指定了`Intent`处理的数据类型。您可以通过指定`scheme`、`host`、`port`、`path`等来定义数据的详细类型。
##### 4. 在代码中使用Intent
在您的代码中，您可以使用`Intent`来启动其他`Activity`，或者通过`startActivity()`、`startService()`、`sendBroadcast()`等方法来执行操作。
例如，启动一个用于打开网页的`Activity`：
```java
Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("http://www.example.com"));
startActivity(intent);
```
##### 5. 接收隐式Intent
如果您希望您的`Activity`能够响应来自其他应用的隐式`Intent`，您需要在`AndroidManifest.xml`中正确声明`Intent`过滤器，并确保`Activity`能够处理这些`Intent`。
##### 6. 处理Intent
在您的`Activity`或`Service`中，您可以通过覆盖`onNewIntent(Intent intent)`方法（对于`Activity`）或`onStartCommand(Intent intent, int flags, int startId)`方法（对于`Service`）来处理接收到的`Intent`。
使用`Intent`过滤器可以让您的应用更加灵活，能够响应多种类型的`Intent`，从而提高用户体验和与其他应用的互操作性。在设计应用时，合理使用`Intent`和`Intent`过滤器是重要的最佳实践。



