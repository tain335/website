---
layout: page
title: Android 开发者参考
permalink: /flutter-for-android/
---
本文档适用于 Android 开发者，开发者们可以将现有的 Android 知识应用于 Flutter 构建移动应用程序。如果你了解 Android 框架的基础知识，那么你可以将此文档当做是 Flutter 开发的一个开始。

你现有的 Android 知识与技能对构建 Flutter 应用有非常高的价值。因为 Flutter 依靠操作系统提供了众多的功能和相关配置。Flutter 是一种为移动设备构建 UI 的新方法，除了 UI 构建外它还有一套插件系统用于与 Android 或 iOS 系统进行通信。如果你是 Android 方面的专家，那么你就不必学习 Flutter 的所有内容了。

通过以下链接可以快速跳转到你想要了解的内容：

* TOC Placeholder
{:toc}

# 视图

## Android 中的 View 在 Flutter 中对应什么？

在 Android 中，View 是用于在屏幕上显示内容的基础。 如按钮，工具栏和输入框，这一切都是 View。在 Flutter 中对应 View 的是 Widget。然而与 View 相比，Widget 有一些不同之处。首先 Widget 实例仅存在于每一帧之间，并且在每一帧之间 Flutter 都会创建一课 Widget 树。相比之下，在 Android 上绘制 View 的时候，除非调用 `invalidate`方法，否则视图不会进行重绘。

不同于 Android 中 View 是可变的，在Flutter 中的 Widget 是不可变的。这种特性使得 Flutter 中的 Widget 变得十分轻量级。



## 如何更新 Widget

在 Android 中可以直接通过 View 来更新它们的状态。但是在 Flutter 中 Widget 是不可变的，所以不能直接通过 Widget 进行更新，如果需要更新 Widget 必须通过 `State`。

所以这里就引出了 `StatefulWidget` 和 `StatelessWidget`。从字面理解，StatelessWidget 是没有内部状态的即不可变。而 StatefulWidget 具有状态，即是可刷新的。

当你构建的 UI 元素中有些部分是不变的，那么使用 StatelessWidget 是一个不错的选择。

例如在 Android 中你通常会将 App 的 logo 通过 ImageView 显示。而 logo 一般不会变，因此对应 Flutter 中你就可以使用 StatelessWidget 来显示你的 logo。

如果你想通过 HTTP 请求后获得的数据或用户交互来刷新 UI 视图，你需要使用 StatefulWidget 然后主动告诉 Flutter 底层 Widget 的状态发生了变化，只有如此 Flutter 才会刷新对应的 Widget。

这里有重要的一点需要注意，StatelessWidget 和 StatefulWidget 的核心内容是一致的，它们都会在每一帧中被重构，不同之处在于 StatefulWidget 有一个 State 对象，它可以为 StatefulWidget 在不同帧之间存储数据。

如果你还是有疑惑的话，只要记住如果一个 Widget 会变化，那么它就是有状态的。但是如果一个子 Widget 是有状态的，但是其父 Widget 是不可变的话也父 Widget 可以是 StatelessWidget 。

接下来看一下如何使用 StatelessWidget。Text 是一个常见的 StatelessWidget。如果你查看其源码的话，会发现 Text 是 StatelessWidget 的一个子类

<!-- skip -->
{% prettify dart %}
new Text(
  'I like Flutter!',
  style: new TextStyle(fontWeight: FontWeight.bold),
);
{% endprettify %}

如你所见，Text 没有任何状态信息，它仅仅是用于显示构造函数传递给它的信息。

但是如果想要通过点击一个按钮来改变 ‘I like Flutter!’ ，那该如何实现？

答案是可以使用 StatefulWidget 包裹 Text，并通过点击按钮来刷新 Text 的内容。

代码如下:

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // Default placeholder text
  String textToShow = "I Like Flutter";

  void _updateText() {
    setState(() {
      // update the text
      textToShow = "Flutter is Awesome!";
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new Center(child: new Text(textToShow)),
      floatingActionButton: new FloatingActionButton(
        onPressed: _updateText,
        tooltip: 'Update Text',
        child: new Icon(Icons.update),
      ),
    );
  }
}
{% endprettify %}


## 如果使用 Widget 进行布局呢？XML 布局文件在哪里？

在 Android 中通常使用 XML 来进行 UI 的布局，但在 Flutter 中 UI 的布局是通过在 dart 文件中构建 Widget 树来实现的。

下面是一个简单的例子用于在屏幕上居中显示一个按钮。

<!-- skip -->
{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new Center(
        child: new MaterialButton(
          onPressed: () {},
          child: new Text('Hello'),
          padding: new EdgeInsets.only(left: 10.0, right: 10.0),
        ),
      ),
    );
  }
{% endprettify %}

你可以在此处查看 Flutter 提供的所有布局:
[http://doc.flutter-dev.cn/widgets/layout/](http://doc.flutter-dev.cn/widgets/layout/)

## 如何在布局中添加或移除组件

在 Android 中，你可以通过调用父布局的 addChild 或者 removeChild 来添加或移除视图，但在 Flutter 中 Widget 是不可变的，所以没有 addChild 或 removeChild 方法。相反，你可以传入一个函数，该函数返回一个 子 Widget 给父 Widget。并在该函数中通过一个 bool 值来控制子 Widget 的创建。

下面的例子展示了如果通过点按一个按钮来切换不同的子 Widget:

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // Default value for toggle
  bool toggle = true;
  void _toggle() {
    setState(() {
      toggle = !toggle;
    });
  }

  _getToggleChild() {
    if (toggle) {
      return new Text('Toggle One');
    } else {
      return new MaterialButton(onPressed: () {}, child: new Text('Toggle Two'));
    }
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new Center(
        child: _getToggleChild(),
      ),
      floatingActionButton: new FloatingActionButton(
        onPressed: _toggle,
        tooltip: 'Update Text',
        child: new Icon(Icons.update),
      ),
    );
  }
}
{% endprettify %}


## 在 Android 中可以使用 `View.animate()` 来让 View 产生动画，在 Flutter 中如何让 Widget 产生动画呢？

在 Flutter 中可以使用 `animation` 库是 Widget 产生动画效果。

在 Andorid 中，可以通过 XML 来创建动画，同样也可以在 View 上调用 animate() 方法产生动画。而在 Flutter 中则需要将 Widget 作为 AnimationWidget 的子节点产生动画。

同 Android 中类似，在 Flutter 中有 AnimationController 和 Interpolater，它们都扩展了 Animation 类。接着将 AnimationController 和 Animation 传递给 AnimationWidget，并通过 AnimationController 来启动动画。

下面这个例子展示了如何编写一个带淡入淡出动画：

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new FadeAppTest());
}

class FadeAppTest extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Fade Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MyFadeTest(title: 'Fade Demo'),
    );
  }
}

class MyFadeTest extends StatefulWidget {
  MyFadeTest({Key key, this.title}) : super(key: key);
  final String title;
  @override
  _MyFadeTest createState() => new _MyFadeTest();
}

class _MyFadeTest extends State<MyFadeTest> with TickerProviderStateMixin {
  AnimationController controller;
  CurvedAnimation curve;

  @override
  void initState() {
    controller = new AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
    curve = new CurvedAnimation(parent: controller, curve: Curves.easeIn);
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Center(
          child: new Container(
              child: new FadeTransition(
                  opacity: curve,
                  child: new FlutterLogo(
                    size: 100.0,
                  )))),
      floatingActionButton: new FloatingActionButton(
        tooltip: 'Fade',
        child: new Icon(Icons.brush),
        onPressed: () {
          controller.forward();
        },
      ),
    );
  }
}
{% endprettify %}


查阅 [http://doc.flutter-dev.cn/widgets/animation/](http://doc.flutter-dev.cn/widgets/animation/) 和 [http://doc.flutter-dev.cn/tutorials/animation](http://doc.flutter-dev.cn/tutorials/animation) 了解更多关于动画的细节。

## 如何使用 Canvas 进行绘制

在 Android 中可以使用 Canvas 进行自定义的绘制。

Flutter 中的 CustomPaint 和 CustomPainter 这两个类可以帮助你在 Canvas 上进行绘制。

查看下面链接，可以了解到如何使用上面提到的两个类自定义一个签名功能：

 [https://stackoverflow.com/questions/46241071/create-signature-area-for-mobile-app-in-dart-flutter](https://stackoverflow.com/questions/46241071/create-signature-area-for-mobile-app-in-dart-flutter)

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';
class SignaturePainter extends CustomPainter {
  SignaturePainter(this.points);
  final List<Offset> points;
  void paint(Canvas canvas, Size size) {
    var paint = new Paint()
      ..color = Colors.black
      ..strokeCap = StrokeCap.round
      ..strokeWidth = 5.0;
    for (int i = 0; i < points.length - 1; i++) {
      if (points[i] != null && points[i + 1] != null)
        canvas.drawLine(points[i], points[i + 1], paint);
    }
  }
  bool shouldRepaint(SignaturePainter other) => other.points != points;
}
class Signature extends StatefulWidget {
  SignatureState createState() => new SignatureState();
}
class SignatureState extends State<Signature> {
  List<Offset> _points = <Offset>[];
  Widget build(BuildContext context) {
    return new GestureDetector(
      onPanUpdate: (DragUpdateDetails details) {
        setState(() {
          RenderBox referenceBox = context.findRenderObject();
          Offset localPosition =
          referenceBox.globalToLocal(details.globalPosition);
          _points = new List.from(_points)..add(localPosition);
        });
      },
      onPanEnd: (DragEndDetails details) => _points.add(null),
      child: new CustomPaint(painter: new SignaturePainter(_points)),
    );
  }
}
class DemoApp extends StatelessWidget {
  Widget build(BuildContext context) => new Scaffold(body: new Signature());
}
void main() => runApp(new MaterialApp(home: new DemoApp()));
{% endprettify %}


## 如何构建自定义 Widget

在 Android 中通常通过继承 View 或其子类来构建自定义的 View。

在 Flutter 中构建自定义的 Widget 通常采用的是组合其他 Widget 的方式而非传统的继承。

下面看一下如何构建一个自定义的按钮，它将构造方法传入的文字作为自己按钮的文字进行显示。这里可以看到我们是通过将其两者进行组合来实现，而非继承 RaiseButton 来实现。

<!-- skip -->
{% prettify dart %}
class CustomButton extends StatelessWidget {
  final String label;
  CustomButton(this.label);

  @override
  Widget build(BuildContext context) {
    return new RaisedButton(onPressed: () {}, child: new Text(label));
  }
}
{% endprettify %}

接下来你就可以像使用其他 Widget 一样来使用 CustomButton 了：

<!-- skip -->
{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new Center(
      child: new CustomButton("Hello"),
    );
  }
}
{% endprettify %}

# Intents

## Android 中的 Intent 在 Flutter 中对应什么

在 Android 中 Intent 主要有两种用途：在 Activity 之间切换和调用外部组件。Flutter 中没有 Intent 的概念，但如果需要的话 Flutter 可以通过插件的方式触发 Intent。

在 Flutter 切换界面可以通过 Route 来实现。另外值得注意的是在管理 Flutter 中的多个界面的时候有两个重要的概念：Route 和 Navigator。一个 Route 就代表了一个界面（类似与 Activity），而 Navigator 则是一个管理 Route 的 Widget。Navigator 可以通过 pop 和 push 的方法在界面之间进行切换。

类似 Android 中，可以在 AndroidManifest 中定义 Activity，在 Flutter 中可以将指定 Route 的映射集合添加到 MaterialApp 的根目录。
<!-- skip -->
{% prettify dart %}
 void main() {
  runApp(new MaterialApp(
    home: new MyAppHome(), // becomes the route named '/'
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => new MyPage(title: 'page A'),
      '/b': (BuildContext context) => new MyPage(title: 'page B'),
      '/c': (BuildContext context) => new MyPage(title: 'page C'),
    },
  ));
}
{% endprettify %}
接着你就可以通过如下方式来操作 Route。

<!-- skip -->
{% prettify dart %}
Navigator.of(context).pushNamed('/b');
{% endprettify %}

Intent 另外比较实用的作用是调用外部组件，如 Camera 或文件选择器，如果要在 Flutter 中实现类似功能，你需要在平台代码中集成或实用现有的库。

查看[开发扩展包](http://doc.flutter-dev.cn/developing-packages/) 章节查看如何集成平台本地功能。



## 在 Flutter 中如何处理来自外部的 Intent

Flutter 可以和 Android 层进行交互来共享传入的 Intent。

在下面的例子中，我们在 AndroidManifest 中注册了发送文本的 Intent 过滤，然后 App 的 Android 层就可以与 Flutter 层共享传入的文本。

下面例子的基本流程是首先我们处理 Android 层 Intent 传入的数据，然后当 Flutter 层发送请求时将文本通过 MethodChannel 传递给 Flutter 层。

首先在 AndroidManifest 中注册感兴趣的 Intent。

<!-- skip -->
{% prettify xml %}
     <activity
            android:name=".MainActivity"
            android:launchMode="singleTop"
            android:theme="@style/LaunchTheme"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize">
            <!-- This keeps the window background of the activity showing
                 until Flutter renders its first frame. It can be removed if
                 there is no splash screen (such as the default splash screen
                 defined in @style/LaunchTheme). -->
            <meta-data
                android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
                android:value="true" />
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.SEND" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
{% endprettify %}

接着在 MainActivity 中处理接收到的文本信息并保存下来，当 Flutter 层请求文本信息时将其传递给 Flutter 层即可。
<!-- skip -->
{% prettify java %}
package com.yourcompany.shared;

import android.content.Intent;
import android.os.Bundle;

import java.nio.ByteBuffer;

import io.flutter.app.FlutterActivity;
import io.flutter.plugin.common.ActivityLifecycleListener;
import io.flutter.plugin.common.MethodCall;
import io.flutter.plugin.common.MethodChannel;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class MainActivity extends FlutterActivity {
    String sharedText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        GeneratedPluginRegistrant.registerWith(this);
        Intent intent = getIntent();
        String action = intent.getAction();
        String type = intent.getType();

        if (Intent.ACTION_SEND.equals(action) && type != null) {
            if ("text/plain".equals(type)) {
                handleSendText(intent); // Handle text being sent
            }
        }

        new MethodChannel(getFlutterView(), "app.channel.shared.data").setMethodCallHandler(new MethodChannel.MethodCallHandler() {
            @Override
            public void onMethodCall(MethodCall methodCall, MethodChannel.Result result) {
                if (methodCall.method.contentEquals("getSharedText")) {
                    result.success(sharedText);
                    sharedText = null;
                }
            }
        });
    }


    void handleSendText(Intent intent) {
        sharedText = intent.getStringExtra(Intent.EXTRA_TEXT);
    }
}
{% endprettify %}

最后在 Flutter 层，可以选择在 `initState` 中对文本进行请求。

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample Shared App Handler',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  static const platform = const MethodChannel('app.channel.shared.data');
  String dataShared = "No data";

  @override
  void initState() {
    super.initState();
    getSharedText();
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(body: new Center(child: new Text(dataShared)));
  }

  getSharedText() async {
    var sharedData = await platform.invokeMethod("getSharedText");
    if (sharedData != null) {
      setState(() {
        dataShared = sharedData;
      });
    }
  }
}
{% endprettify %}



## startActivityForResult 在 Flutter 中如何实现

可以通过 Navigator 的 push 方法返回的 Futrue 来获得页面的返回数据。例如你想启动一个位置选择的界面，然后从中获得用户选择的结果，可以使用如下方法：

<!-- skip -->
{% prettify dart %}
Map coordinates = await Navigator.of(context).pushNamed('/location');
{% endprettify %}

接着在文职选择界面，当用户选择完位置以后，可以调用如下方法把结果传递给上面的 coordinates。

<!-- skip -->
{% prettify dart %}
Navigator.of(context).pop({"lat":43.821757,"long":-79.226392});
{% endprettify %}

# UI 中的异步

##  runOnUiThread 在 Flutter 中对应什么方法

Dart 是单线程执行模型、支持 Isolate（一种多线程模型）、事件循环和异步编程的。除非使用 Isolate，不然你的 Dart 代码都是在 UI 线程中进行并由事件循环进行驱动。

例如你可以在 UI 线程执行网络请求而不会导致 UI 线程的阻塞：

<!-- skip -->
{% prettify dart %}
loadData() async {
  String dataURL = "https://jsonplaceholder.typicode.com/posts";
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = JSON.decode(response.body);
  });
}
{% endprettify %}

通过调用 setState 方法触发界面的重新构建来刷新并更新数据。

下面是一个完整的获得网络数据并在 ListView 上进行更新的例子：

<!-- skip -->
{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();

    loadData();
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text("Sample App"),
        ),
        body: new ListView.builder(
            itemCount: widgets.length,
            itemBuilder: (BuildContext context, int position) {
              return getRow(position);
            }));
  }

  Widget getRow(int i) {
    return new Padding(
        padding: new EdgeInsets.all(10.0),
        child: new Text("Row ${widgets[i]["title"]}")
    );
  }

  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = JSON.decode(response.body);
    });
  }
}
{% endprettify %}



## Android 中的 AsyncTask 或 IntentService 在 Flutter 对应什么

在 Android 中当你想要进行网络操作时通常会使用 AsyncTask，以避免主线程遭到阻塞。另外 AsyncTask 有一个线程池专门为你管理线程。

由于 Flutter 是单线程并由事件驱动（类似 Node.js），因此你不必为线程管理或需要类似的 AsyncTask 和 IntentService 而感到担心。

需要异步执行的时候只需要将方法声明为异步方法并在方法中使用 await 来等待即可。
<!-- skip -->
{% prettify dart %}
loadData() async {
  String dataURL = "https://jsonplaceholder.typicode.com/posts";
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = JSON.decode(response.body);
  });
}
{% endprettify %}

以上就是你通常进行网络或数据库操作的方式。

在 Android 中，当你继承一个 AsyncTask 的时候，通常要重载它的 3 个方法，OnPreExecute, doInBackground 和 onPostExecute。而在 Flutter 中没有这种麻烦事，你要做的仅仅是 await 一个长时间的操作，剩下的事 Dart 的事件循环机制会帮你搞定。

但是，有时你可能会处理一些数据量较大叫密集的操作，Flutter 的 UI 还是可能会受到影响。

在这种情况下，还是有何 AsyncTask 类似的解决方案的。在 Flutter 中可以利用 CPU 多核的性质来并行处理事务，而这一工作则是又 Isolate 完成。

Isolate 是独立的执行线程，和主线程不共享任何内存。这意味着你不能在 Isolate 中给主线程的变量赋值或者调用 setState 方法来更新 UI。

让我们看一个 Isolate 的简单例子，学习下如何与主线程交流并共享数据来更新 UI：

<!-- skip -->
{% prettify dart %}
  loadData() async {
    ReceivePort receivePort = new ReceivePort();
    await Isolate.spawn(dataLoader, receivePort.sendPort);

    // The 'echo' isolate sends it's SendPort as the first message
    SendPort sendPort = await receivePort.first;

    List msg = await sendReceive(sendPort, "https://jsonplaceholder.typicode.com/posts");

    setState(() {
      widgets = msg;
    });
  }

// the entry point for the isolate
  static dataLoader(SendPort sendPort) async {
    // Open the ReceivePort for incoming messages.
    ReceivePort port = new ReceivePort();

    // Notify any other isolates what port this isolate listens to.
    sendPort.send(port.sendPort);

    await for (var msg in port) {
      String data = msg[0];
      SendPort replyTo = msg[1];

      String dataURL = data;
      http.Response response = await http.get(dataURL);
      // Lots of JSON to parse
      replyTo.send(JSON.decode(response.body));
    }
  }

  Future sendReceive(SendPort port, msg) {
    ReceivePort response = new ReceivePort();
    port.send([msg, response.sendPort]);
    return response.first;
  }
{% endprettify %}

`dataLoader` 方法在它独立的 Isolate 中运行，你可以在其中执行更多的 CPU 密集型处理，例如解析一万行以上的 JSON 的数据或执行密集型数学计算。

你可以参考下面完整的例子：

<!-- skip -->
{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:async';
import 'dart:isolate';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    loadData();
  }

  showLoadingDialog() {
    if (widgets.length == 0) {
      return true;
    }

    return false;
  }

  getBody() {
    if (showLoadingDialog()) {
      return getProgressDialog();
    } else {
      return getListView();
    }
  }

  getProgressDialog() {
    return new Center(child: new CircularProgressIndicator());
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text("Sample App"),
        ),
        body: getBody());
  }

  ListView getListView() => new ListView.builder(
      itemCount: widgets.length,
      itemBuilder: (BuildContext context, int position) {
        return getRow(position);
      });

  Widget getRow(int i) {
    return new Padding(padding: new EdgeInsets.all(10.0), child: new Text("Row ${widgets[i]["title"]}"));
  }

  loadData() async {
    ReceivePort receivePort = new ReceivePort();
    await Isolate.spawn(dataLoader, receivePort.sendPort);

    // The 'echo' isolate sends it's SendPort as the first message
    SendPort sendPort = await receivePort.first;

    List msg = await sendReceive(sendPort, "https://jsonplaceholder.typicode.com/posts");

    setState(() {
      widgets = msg;
    });
  }

// the entry point for the isolate
  static dataLoader(SendPort sendPort) async {
    // Open the ReceivePort for incoming messages.
    ReceivePort port = new ReceivePort();

    // Notify any other isolates what port this isolate listens to.
    sendPort.send(port.sendPort);

    await for (var msg in port) {
      String data = msg[0];
      SendPort replyTo = msg[1];

      String dataURL = data;
      http.Response response = await http.get(dataURL);
      // Lots of JSON to parse
      replyTo.send(JSON.decode(response.body));
    }
  }

  Future sendReceive(SendPort port, msg) {
    ReceivePort response = new ReceivePort();
    port.send([msg, response.sendPort]);
    return response.first;
  }

}


{% endprettify %}

## Android 中的 OkHttp 在 Flutter 中对应什么

在 Flutter 中使用 `http` 扩展库将使得网络通信变得异常简单。

虽然 `http` 扩展库没有实现 OkHttp 的所有功能，但其抽象出了很多常用的功能，使得原本你要自己实现的网络调用变成一些极为简单的方法。

[https://pub.dartlang.org/packages/http](https://pub.dartlang.org/packages/http)

你可以在 pubspec.yaml 添加 http 包的依赖来使用它：

<!-- skip -->
{% prettify yaml %}
dependencies:
  ...
  http: '>=0.11.3+12'
{% endprettify %}

接着就可以进行网络请求了，如下：

<!-- skip -->
{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
[...]
  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = JSON.decode(response.body);
    });
  }
}
{% endprettify %}

一旦你得到了你需要的数据，就可以通过调用 setState 方法通知 Flutter 将网络调用的结果更新到 UI 上。

## 在 Flutter 中，当有任务在执行时，如何显示进度

在 Android 中，当你执行长时间的任务是，会在界面上显示一个进度指示器表明当前有任务在执行。

在 Flutter 中这可以通过进度指示器 Widget 来实现。你可以通过一个 boolean 值来告诉 Flutter 是否需要显示进度指示器。

在下面这个例子中，我们将 build 方法分解为三个不同的方法。如果 showLoadingDialog 为 true 时显示进度指示器，否则将显示带有数据的 ListView：

<!-- skip -->
{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    loadData();
  }

  showLoadingDialog() {
    if (widgets.length == 0) {
      return true;
    }

    return false;
  }

  getBody() {
    if (showLoadingDialog()) {
      return getProgressDialog();
    } else {
      return getListView();
    }
  }

  getProgressDialog() {
    return new Center(child: new CircularProgressIndicator());
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text("Sample App"),
        ),
        body: getBody());
  }

  ListView getListView() => new ListView.builder(
      itemCount: widgets.length,
      itemBuilder: (BuildContext context, int position) {
        return getRow(position);
      });

  Widget getRow(int i) {
    return new Padding(padding: new EdgeInsets.all(10.0), child: new Text("Row ${widgets[i]["title"]}"));
  }

  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = JSON.decode(response.body);
    });
  }
}
{% endprettify %}

# 项目结构与资源

## 分辨率相关的图像资源应存储在哪里？（HDPI/XXHDPI）

Flutter 遵循像 iOS 这样简单的3种分辨率格式。 1x，2x 和 3x。

你可以创建一个名为 images 的文件夹，并为每个图像文件生成一个 @2x 和 @3x 的图像文件，并将它们放在对应文件夹中，像这样

- …/my_icon.png
- …/2.0x/my_icon.png
- …/3.0x/my_icon.png

接着在 pubspec.yaml 中声明这些图像资源。

<!-- skip -->
{% prettify yaml %}
assets:
 - images/a_dot_burr.jpeg
 - images/a_dot_ham.jpeg
     {% endprettify %}

然后你就可以使用 AssetImage 来获得这些图形资源。

<!-- skip -->
{% prettify dart %}
return new AssetImage("images/a_dot_burr.jpeg");
{% endprettify %}

## 字符串如何存储，如何存储不同语言的字符串

目前最佳方案就是创建一个 Strings 的类，并将字符创以静态字段的形式存储在其中：

<!-- skip -->
{% prettify dart %}
class Strings{
  static String welcomeMessage = "Welcome To Flutter";
}
{% endprettify %}

接着你可以向如下中方式进行访问：

<!-- skip -->
{% prettify dart %}
 new Text(Strings.welcomeMessage)
{% endprettify %}

Flutter 对 Android 资源的可访问性提供了基本的支持，但目前这个功能还在进行中。

Flutter 鼓励开发者使用 [intl
package](https://pub.dartlang.org/packages/intl) 来进行国际化和本地化。

## Android 中的 Gradle 文件在 Flutter 中对应什么

在 Android 中，你可以通过位于 Android 项目中的 Gradle 文件来添加依赖项。

在 Flutte 中，虽然在 Flutter 项目的 Android 文件夹下有 Gradle 文件，但只有在添加平台集成所需的依赖时，才能使用这些文件。否则，你可以使用 pubspec.yaml 声明特定于 Flutter 的外部依赖项。 

在 [Pub](https://pub.dartlang.org/flutter/packages/) 中可以找到很多使用的依赖库。

# Activities 和 Fragments

## Android 中的 Activity 和 Fragment 在 Flutter 中对应什么

在 Android 中 Activity 代表用户可完成一项特定功能或任务的界面。Fragment 代表了一种模块化 UI 代码的方式，为更大的屏幕构建更复杂的用户界面，并帮助在小屏幕和大屏幕之间扩展你的应用程序。在 Flutter 中这两者都属于 Widget 的概念。

## 如何监听 Android Activity 的生命周期

在 Android 中可以通过重载方法来监听 Activity 的生命周期。

在 Flutter 中你可以通过注册到 WidgetsBinding 的观察者并监听 didChangeAppLifecycleState 更改事件来监听 Activity 的生命周期事件。

以下是你可以监听到的生命周期事件：

- resumed - 应用程序可见并可以和用户进行交互，等价于 Android 中的 OnResume 。
- inactive - 应用程序处于非活动状态，并且无法与用户交互。此事件仅适用于 iOS 设备。
- paused - 应用程序当前对用户不可见，无法与用户交互，并且在后台运行。等价于 Android 中的 OnPause。
- suspending - 应用程序将暂时中止。这在iOS上未使用。



<!-- skip -->
{% prettify dart %}
import 'package:flutter/widgets.dart';

class LifecycleWatcher extends StatefulWidget {
  @override
  _LifecycleWatcherState createState() => new _LifecycleWatcherState();
}

class _LifecycleWatcherState extends State<LifecycleWatcher> with WidgetsBindingObserver {
  AppLifecycleState _lastLifecyleState;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    setState(() {
      _lastLifecyleState = state;
    });
  }

  @override
  Widget build(BuildContext context) {
    if (_lastLifecyleState == null)
      return new Text('This widget has not observed any lifecycle changes.', textDirection: TextDirection.ltr);
    return new Text('The most recent lifecycle state this widget observed was: $_lastLifecyleState.',
        textDirection: TextDirection.ltr);
  }
}

void main() {
  runApp(new Center(child: new LifecycleWatcher()));
}
{% endprettify %}

# 布局

##  Android 中的 LinearLayout 在 Flutter 中对应什么

在 Android 中，使用 LinearLayout 来使你的小部件垂直或水平放置。在 Flutter 中，你可以使用 Row 或者 Co​​lumn 来实现相同的效果。

如果你注意到这两个代码示例中“Row”和“Column”结构异常相似。它们的 child 完全一致，可以利用这个特性在相同 child 的情况下开发丰富的布局。
<!-- skip -->
{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        new Text('Row One'),
        new Text('Row Two'),
        new Text('Row Three'),
        new Text('Row Four'),
      ],
    );
  }
{% endprettify %}

<!-- skip -->
{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        new Text('Column One'),
        new Text('Column Two'),
        new Text('Column Three'),
        new Text('Column Four'),
      ],
    );
  }
{% endprettify %}

## Android 中的 RelativeLayout 在 Flutter 对应什么

RelativeLayout 用于使你的 Widget 相对于彼此放置。在Flutter中，有几种方法可以实现相同的结果。

你可以通过使用 Column，Row 和 Stack 等 Widget 的组合来实现 RelativeLayout 的效果。你可以为小部件构造函数指定相对于父项布局的规则。

在 Flutter 中构建 RelativeLayout 的一个很好的例子：
[https://stackoverflow.com/questions/44396075/equivalent-of-relativelayout-in
-flutter](https://stackoverflow.com/questions/44396075/equivalent-of-relativelayout-in-flutter)

## Android 中 ScrollView 在 Flutter 中对应什么

在 Android 中，ScrollView 允许你放置 Widget，以便在用户设备的屏幕比你的内容小的情况下可以进行滑动。

在 Flutter 中，最简单的方法是使用 ListView 。在 Flutter 中，ListView 既是 ScrollView 又是 Android中对应的 ListView。
<!-- skip -->
{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new ListView(
      children: <Widget>[
        new Text('Row One'),
        new Text('Row Two'),
        new Text('Row Three'),
        new Text('Row Four'),
      ],
    );
  }
{% endprettify %}

# 手势检测和触摸事件处理

## 如何将一个 onClick 监听添加到 Flutter 中的 Widget 中

在 Android 中，可以通过调用方法'setOnClickListener'将 OnClick 附加到按钮等视图上。

在 Flutter 中，添加触摸监听器有两种方法：
1. 如果 Widget 支持事件检测，则可以将一个函数传递给它并进行处理。例如，RaisedButton 有一个 onPressed 参数

   <!-- skip -->
  {% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return new RaisedButton(
        onPressed: () {
          print("click");
        },
        child: new Text("Button"));
  }
   {% endprettify %}

2. 如果 Widget 不支持事件检测，则可以将该 Widget 包装到 GestureDetector 中，并将函数传递给 onTap 参数。

   <!-- skip -->
  {% prettify dart %}
class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        body: new Center(
      child: new GestureDetector(
        child: new FlutterLogo(
          size: 200.0,
        ),
        onTap: () {
          print("tap");
        },
      ),
    ));
  }
}
   {% endprettify %}

## 我如何处理 Widget 上的其他手势

使用 GestureDetector 我们可以监听广泛的手势，例如

- 点击

  - `onTapDown` 屏幕某一位置的按下事件。
  - `onTapUp` 屏幕某一位置的手势抬起事件。
  - `onTap` 点击事件
  - `onTapCancel` 值产生的 onTapDown 却没有产生 onTapUp 的事件。

- 双击

  - `onDoubleTap` 用户在同一位置进行两次快速点击。

- 长按

  - `onLongPress` 长时间点按屏幕某一点时产生的事件。

- 垂直拖动

  - `onVerticalDragStart` 屏幕上某一点开始垂直移动的事件。
  - `onVerticalDragUpdate` 屏幕上某一点垂直移动过程中的更新事件。
  - `onVerticalDragEnd` 屏幕上某一点停止垂直移动后的事件。

- 水平拖动

  - `onHorizontalDragStart` 屏幕上某一点开始水平移动的事件。
  - `onHorizontalDragUpdate` 屏幕上某一点水平移动过程中的更新事件。
  - `onHorizontalDragEnd` 屏幕上某一点停止水平移动后的事件。


例如下面这个例子，是使用 GestureDetector，通过双击 FlutterLogo 来使其旋转：

<!-- skip -->
{% prettify dart %}
AnimationController controller;
CurvedAnimation curve;

@override
void initState() {
  controller = new AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
  curve = new CurvedAnimation(parent: controller, curve: Curves.easeIn);
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        body: new Center(
          child: new GestureDetector(
            child: new RotationTransition(
                turns: curve,
                child: new FlutterLogo(
                  size: 200.0,
                )),
            onDoubleTap: () {
              if (controller.isCompleted) {
                controller.reverse();
              } else {
                controller.forward();
              }
            },
        ),
    ));
  }
}
{% endprettify %}

# Listview 和 Adapter

## Android 中的 ListView 在 Flutter 中的替代品是什么

答案还是 ListView ！

In an Android ListView, you create an
adapter that you can then pass into the
ListView which will render each row with what your adapter returns. However you
have to make sure you recycle your rows , otherwise, you get all sorts of crazy
visual glitches and memory issues.

In Flutter, due to Flutters immutable widget pattern, you pass in a List of
Widgets to your ListView and Flutter will take care of making sure they are
scrolling fast and smooth.

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new ListView(children: _getListData()),
    );
  }

  _getListData() {
    List<Widget> widgets = [];
    for (int i = 0; i < 100; i++) {
      widgets.add(new Padding(padding: new EdgeInsets.all(10.0), child: new Text("Row $i")));
    }
    return widgets;
  }
}
{% endprettify %}

## How do I know which list item is clicked on

In Android, the ListView has a method to find out which item was clicked
'onItemClickListener'. Flutter makes it easier by letting you just use the
touch handling that the widgets you passed in have.

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new ListView(children: _getListData()),
    );
  }

  _getListData() {
    List<Widget> widgets = [];
    for (int i = 0; i < 100; i++) {
      widgets.add(new GestureDetector(
        child: new Padding(
            padding: new EdgeInsets.all(10.0),
            child: new Text("Row $i")),
        onTap: () {
          print('row tapped');
        },
      ));
    }
    return widgets;
  }
}
{% endprettify %}

## How do I update ListView's dynamically

On Android, you would update the adapter and call notifyDataSetChanged. In
Flutter if you were to update the list of widgets inside a setState(), you
would quickly see that your data did not change visually.

This is because when setState is called, the Flutter rendering engine will go
through all the widgets to see if they have changed. When it gets to your
ListView it will do a `==operator` and see that the two ListViews are the same
and nothing has changed, hence no update to the data.

To update your ListView then is to create a new List() inside of setState and
copy over all the old data to the new list. This is a simple way to achieve an
update.

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    for (int i = 0; i < 100; i++) {
      widgets.add(getRow(i));
    }
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new ListView(children: widgets),
    );
  }

  Widget getRow(int i) {
    return new GestureDetector(
      child: new Padding(
          padding: new EdgeInsets.all(10.0),
          child: new Text("Row $i")),
      onTap: () {
        setState(() {
          widgets = new List.from(widgets);
          widgets.add(getRow(widgets.length + 1));
          print('row $i');
        });
      },
    );
  }
}
{% endprettify %}

However the recommended, efficient, and effective way is to use a
ListView.Builder. This method is great when you have a dynamic
List or a List with very large amounts of data. This is essentially
the equivalent of using RecyclerView on Android which automatically
recycles list elements for you:

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    for (int i = 0; i < 100; i++) {
      widgets.add(getRow(i));
    }
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text("Sample App"),
        ),
        body: new ListView.builder(
            itemCount: widgets.length,
            itemBuilder: (BuildContext context, int position) {
              return getRow(position);
            }));
  }

  Widget getRow(int i) {
    return new GestureDetector(
      child: new Padding(
          padding: new EdgeInsets.all(10.0),
          child: new Text("Row $i")),
      onTap: () {
        setState(() {
          widgets.add(getRow(widgets.length + 1));
          print('row $i');
        });
      },
    );
  }
}
{% endprettify %}

Instead of creating a "new ListView" we create a new ListView.builder which
takes two key parameters, the initial length of the list and an ItemBuilder
function.

The ItemBuilder function is a lot like the getView function in an Android
adapter, it takes in a position and you return the row you want rendered for
that position.

Lastly, but most important, if you notice the onTap function, we don't recreate
the List anymore and instead just .add to it.



# Working with Text

## How do I set custom fonts on my Text widgets

In Android SDK (as of Android O), you would create a Font resource file and
pass it into the FontFamily param for your TextView.

In Flutter first you need to take your font file and place in folder in your
project (best practice is to create a folder called assets).

Next in your pubspec.yaml file you would declare the fonts

<!-- skip -->
{% prettify yaml %}
fonts:
   - family: MyCustomFont
     fonts:
       - asset: fonts/MyCustomFont.ttf
       - style: italic
           {% endprettify %}

and lastly you would assign the font to your Text widget

<!-- skip -->
{% prettify dart %}
@override
Widget build(BuildContext context) {
  return new Scaffold(
    appBar: new AppBar(
      title: new Text("Sample App"),
    ),
    body: new Center(
      child: new Text(
        'This is a custom font text',
        style: new TextStyle(fontFamily: 'MyCustomFont'),
      ),
    ),
  );
}
{% endprettify %}

## How do I style my Text widgets

Along with customizing fonts you can customize a lot of different styles on a
Text widget.

The style parameter of a Text widget takes a TextStyle object, where you can
customize many parameters such as

- color
- decoration
- decorationColor
- decorationStyle
- fontFamily
- fontSize
- fontStyle
- fontWeight
- hashCode
- height
- inherit
- letterSpacing
- textBaseline
- wordSpacing

# Form Input

## What is the equivalent of a "hint" on an Input

In Flutter you can easily show a "hint" or a placeholder text for your input by
adding an InputDecoration object to the decoration constructor parameter for
the Text Widget

<!-- skip -->
{% prettify dart %}
body: new Center(
  child: new TextField(
    decoration: new InputDecoration(hintText: "This is a hint"),
  )
)
{% endprettify %}

## How do I show validation errors

Just like how you would with a "hint", you can pass in a InputDecoration object
to the decoration constructor for the Text widget.

However, you would not want to start off with showing an error and typically
would want to show it when the user has entered some invalid data. This can be
done by updating the state and passing in a new InputDecoration object.

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(new SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => new _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  String _errorText;

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("Sample App"),
      ),
      body: new Center(
        child: new TextField(
          onSubmitted: (String text) {
            setState(() {
              if (!isEmail(text)) {
                _errorText = 'Error: This is not an email';
              } else {
                _errorText = null;
              }
            });
          },
          decoration: new InputDecoration(hintText: "This is a hint", errorText: _getErrorText()),
        ),
      ),
    );
  }

  _getErrorText() {
    return _errorText;
  }

  bool isEmail(String em) {
    String emailRegexp =
        r'^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$';

    RegExp regExp = new RegExp(p);

    return regExp.hasMatch(em);
  }
}
{% endprettify %}


# Flutter Plugins

## How do I access the GPS sensor

To access the GPS sensor you can use the community plugin
[https://pub.dartlang.org/packages/location](https://pub.dartlang.org/packages/location)

## How do I access the Camera

A popular community plugin to access the camera is
[https://pub.dartlang.org/packages/image_picker](https://pub.dartlang.org/packages/image_picker)

## How do I log in with Facebook

To access Facebook Connect functionality you can use
[https://pub.dartlang.org/packages/flutter_facebook_connect](https://pub.dartlang.org/packages/flutter_facebook_connect) .

## How do I build my own custom native integrations

If there is platform specific functionality that Flutter or its community
Plugins are missing then you can build your own following this tutorial
[https://flutter.io/developing-packages/](https://flutter.io/developing-packages/) .

Flutter's plugin architecture in a nutshell is a lot like using an Event bus in
Android: you fire off a message and let the receiver process and emit a result
back to you, in this case the receiver would be iOS or Android.

## How do I use the NDK in my Flutter application

If you use the NDK in your current Android application and want your Flutter
application to take advantage of your native libraries then it's possible by
building a custom plugin.

Your custom plugin would first talk to your Android app, where you would be
able to call your `native` marked functions. Once a response is ready, you
would be able to send a message back to Flutter and render the result.

# Themes

## How do I theme my Material-styled app

Flutter out of the box comes with a beautiful implementation of Material
Design, which takes care of a lot of styling and theming needs that you would
typically do. Unlike Android where you declare themes in XML and then assign it
to your application via AndroidManifest.xml, in Flutter you can declare themes
via the top level widget.

To take full advantage of Material Components in your app, you can declare a top
level widget `MaterialApp` as the entry point to your application. MaterialApp
is a convenience widget that wraps a number of widgets that are commonly
required for applications implementing Material Design. It builds upon a WidgetsApp by
adding Material specific functionality.

If you don't want to use Material Components, then you can declare a top level
widget `WidgetsApp` which is a convenience class that wraps a number of widgets
that are commonly required for an application

To customize the colors and styles of Material Components you can pass in a
ThemeData object to the MaterialApp widget, for example in the code below you
can see the primary swatch is set to blue and all text selection color should
be red.

<!-- skip -->
{% prettify dart %}
class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Sample App',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
        textSelectionColor: Colors.red
      ),
      home: new SampleAppPage(),
    );
  }
}
{% endprettify %}


# Databases and local storage

## How do I access Shared Preferences in Flutter?
In Android, you can store a small collection of key-value pairs by using
the SharedPreferences API.

In Flutter, you can access this functionality by using the
Shared Preferences plugin [Shared_Preferences](https://pub.dartlang.org/packages/shared_preferences)

This plugin wraps the functionality of both Shared Preferences and 
NSUserDefaults (the iOS equivalent).

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(
    new MaterialApp(
      home: new Scaffold(
        body: new Center(
          child: new RaisedButton(
            onPressed: _incrementCounter,
            child: new Text('Increment Counter'),
          ),
        ),
      ),
    ),
  );
}

_incrementCounter() async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  int counter = (prefs.getInt('counter') ?? 0) + 1;
  print('Pressed $counter times.');
  prefs.setInt('counter', counter);
}

{% endprettify %}

## How do I access SQLite in Flutter?
In Android, you would use SQLite to store structured data that you can query 
via SQL.

In Flutter, you can access this functionality by using the SQFlite plugin 
[SQFlite](https://pub.dartlang.org/packages/sqflite)

# Notifications

## How do I setup Push Notifications 
In Android, you would use Firebase Cloud Messaging to setup push 
notifications for your app.

In Flutter, you can access this functionality by using the Firebase_Messaging
plugin [Firebase_Messaging](https://github.com/flutter/plugins/tree/master/packages/firebase_messaging)
 
