---
layout: tutorial
title: 编写第一个Flutter应用
permalink: /get-started/codelab/
---

<figure class="right-figure" style="max-width: 260px; padding-right: 10px">
    <img src="images/startup-namer-app.gif"
         alt="Animated GIF of the app that you will be building."
         style="border: 10px solid #333; border-radius: 10px; margin-bottom: 10px" >
</figure>
这篇教程将指导你创建第一个Flutter应用程序。如果你熟悉面向对象程序设计和基本的编程概念（如变量，循环和条件），即可完成本教程。无需具有使用Dart语言或移动编程的相关经验。


{% comment %}
TODO: (maybe, but later)
- Retake screenshots on the Android emulator? (Tao)
  Maybe alternate between Android and iOS emulators?
- Somehow cross link from code to text so people can restart
  and find their place more easily? (Tao)
{% endcomment %}

* TOC
{:toc}

<p class="h2-like">你需要做什么</p>

完成一个简单的移动应用程序，为一家创业公司进行命名推荐。用户可以选择和取消选择名称，并保存最好的一个。代码一次生成十个名称。当用户滚动时，会新生成一批名称。用户可以点击应用栏右上方的列表图标，跳转到仅显示已被收藏的名称的列表页面。

这个GIF动画可以显示出该应用是如何工作的。

<div class="whats-the-point" markdown="1">

<b> <a id="whats-the-point" class="anchor" href="#whats-the-point" aria-hidden="true"><span class="octicon octicon-link"></span></a>你将会学到:</b>

* Flutter应用程序的基本结构。
* 查找和使用package来扩展功能。
* 使用热加载加快开发效率。
* 如何实现一个有状态的控件。
* 如何创建一个无限长度的延迟加载列表。
* 如何创建并导航到第二个页面。
* 如何使用主题更改应用程序的外观。

</div>

<div class="whats-the-point" markdown="1">

<b> <a id="whats-the-point" class="anchor" href="#whats-the-point" aria-hidden="true"><span class="octicon octicon-link"></span></a>你将会用到:</b>

需要安装以下工具:

<ul markdown="1">
<li markdown="1"> Flutter SDK<br>
    Flutter SDK包括Flutter的引擎，框架，小部件，工具和Dart SDK。这个codelab需要v0.1.4或更高版本。
</li>
<li markdown="1"> Android Studio IDE<br>
    这个codelab基于the Android Studio IDE构建，但你也可以使用其他IDE，或者从命令行运行。
</li>
<li markdown="1"> 安装IDE插件<br>
    插件必须为您的编译器单独安装Flutter和Dart插件。除了Android Studio，Flutter和Dart插件也可用于 [VS Code](https://code.visualstudio.com/download) 和
    [IntelliJ](https://www.jetbrains.com/idea/download/#section=mac)IDE。
</li>
</ul>

有关如何设置环境的信息，请参阅 [Flutter安装和配置](/get-started/install/)。

</div>

# 第1步：创建初始Flutter应用

使用[第一个入门Flutter应用](/get-started/test-drive/#create-app)中的说明创建一个简单的模板化Flutter应用。将项目命名为 **startup_namer**（而不是*myapp*）。
You’ll be modifying this starter app to create the finished app.

在这个codelab中，将主要编辑Dart代码所在的 **lib / main.dart**。

<aside class="alert alert-success" markdown="1">
<i class="fa fa-lightbulb-o"> </i> **小贴士:**
将代码粘贴到应用程序中时，缩进可能会错位。您可以使用Flutter工具自动修复此问题：

* Android Studio / IntelliJ IDEA: 右键单击dart代码，然后选择 **Reformat Code with dartfmt**。
* VS Code: 单击右键，选择 **Format Document**.
* 命令行: 运行 `flutter format <filename>`.
</aside>

<ol markdown="1">

<li markdown="1"> 替换lib / main.dart。<br>
删除 **lib / main.dart** 中的所有代码。替换为下面的代码，它在屏幕中心显示“Hello World”。

{% prettify dart %}
import 'package:flutter/material.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Welcome to Flutter',
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('Welcome to Flutter'),
        ),
        body: new Center(
          child: new Text('Hello World'),
        ),
      ),
    );
  }
}
{% endprettify %}
</li>

<li markdown="1"> 运行应用程序。你应该可以看到下面的页面。

<center><img src="images/hello-world-screenshot.png" alt="screenshot of hello world app"></center>
</li>
</ol>

<p class="h2-like">小结</p>

  <ul markdown="1">
  <li markdown="1"> 本示例创建了一个Material app。
      [Material](https://material.io/guidelines/)设计语言是一套移动设备和网页上的视觉设计标准。Flutter提供了一套丰富的Material控件。
  </li>
  <li markdown="1"> main方法采用了 fat arrow (`=>`) 表示法，这是一种用于单行函数或方法的简写。
  </li>
  <li markdown="1"> 该app继承了使它本身成为一个控件的StatelessWidget类。在Flutter中，大多数时候一切都可以看作控件, 包括alignment，padding和layout。
  </li>
  <li markdown="1"> Material库中的Scaffold 控件提供了默认的应用栏(app bar)，标题和构成主页面控件树结构的body属性。 控件的子树可以非常复杂。
  </li>
  <li markdown="1"> 控件的主要工作是提供一个`build()`方法，描述如何根据其他更低层级的控件，来对这个控件进行展示。
  </li>
  <li markdown="1"> 本示例的控件树由包含了Text子控件的Center控件组成。Center控件可将它的所有子控件树对齐到屏幕中心。
  </li>
</ul>
{% comment %}
  Removing this for now. A) It might be confusing and B) the code as shown here is wrong.
  <li markdown="1"> Moving the “hello world” text into a separate widget,
      HelloWorld, results in an identical widget tree as the code above.
      (This code is informational only. You are starting with the Hello
      World code above.)

  <!-- skip -->
  {% prettify dart %}
  import 'package: flutter/material.dart';

  class MyApp extends StatelessWidget {
   @override
   Widget build(BuildContext context) {
     return new MaterialApp(
       title: 'Welcome to Flutter',
       home: new Scaffold(
         appBar: new AppBar(
           title: new Text('Welcome to Flutter'),
         ),
         body: new Center(
           child: new Text('Hello World')
         ),
       ),
     );
   }
  }
  {% endprettify %}

  Update with this code:

  class HelloWorld extends StatelessWidget {
   @override
   Widget build(BuildContext context) {
     return new Center(
       child: new Text('Hello World'),
     );
   }
  }
  </li>
{% endcomment %}

---

# 第2步：使用外部package

在这一步，将开始使用名为**english_words**的开源软件包 ，其中包含数千个最常用的英文单词以及一些实用功能。

可以在[pub.dartlang.org](https://pub.dartlang.org/flutter/)上找到[english_words](https://pub.dartlang.org/packages/english_words)软件包以及其他许多开源软件包。

<ol markdown="1">

<li markdown="1"> pubspec文件管理着Flutter app的静态资源文件(assets)。
    在 **pubspec.yaml**文件中， 将**english_words**（3.1.0或更高版本）添加到依赖列表。新的一行高亮如下：

<!-- skip -->
{% prettify dart %}
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^0.1.0
  [[highlight]]english_words: ^3.1.0[[/highlight]]
{% endprettify %}
</li>

<li markdown="1"> 在Android Studio的editor视图中查看pubspec时,
    点击右上角的**Packages get**，将把package拉取到项目中。你应该可以在控制台中看到以下内容：

<!-- skip -->
{% prettify dart %}
flutter packages get
Running "flutter packages get" in startup_namer...
Process finished with exit code 0
{% endprettify %}
</li>

<li markdown="1"> 在**lib/main.dart**中，为`english_words`添加导入，如高亮的行所示：

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';
[[highlight]]import 'package:english_words/english_words.dart';[[/highlight]]
{% endprettify %}

在键入该行时，Android Studio会为你提供有关库导入的建议。然后将导入字符串显示为灰色，让你知道导入的库尚未使用（到目前为止）。
</li>

<li markdown="1"> 改用英文单词的package来生成文本，而不是字符串“Hello World”。

<aside class="alert alert-success" markdown="1">
<i class="fa fa-lightbulb-o"> </i> **小贴士:**
“Pascal case”（也被称为“大骆驼拼写法”），意味着字符串中的每个单词（包括第一个单词）都以大写字母开头。所以，“uppercamelcase”变成“UpperCamelCase”。
</aside>

对代码进行以下更改，如高亮所示：

<!-- skip -->
{% prettify dart %}
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    [[highlight]]final wordPair = new WordPair.random();[[/highlight]]
    return new MaterialApp(
      title: 'Welcome to Flutter',
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('Welcome to Flutter'),
        ),
        body: new Center(
          //child: new Text([[highlight]]'Hello World'[[/highlight]]), // Replace the highlighted text...
          child: new Text([[highlight]]wordPair.asPascalCase[[/highlight]]),  // With this highlighted text.
        ),
      ),
    );
  }
}
{% endprettify %}
</li>

<li markdown="1"> 如果app正在运行，请使用Flutter Hot Reload按钮
    (<img src="images/hot-reload-button.png" alt="lightning bolt icon">)更新app。每次单击按钮或保存项目时，都会看到随机的单词配对文本，这是因为配对的词是在build方法内部生成的，每次app需要渲染时，或在Flutter Inspector中切换Platform时都会运行

<center><img src="images/step2-screenshot.png" alt="screenshot at completion of second step"></center>
</li>

</ol>

<p class="h2-like">有问题？</p>

如果你的应用程序运行不正常，请检查拼写错误。如有需要，可使用以下链接中的代码使项目回到正轨

* [**pubspec.yaml**](https://gist.githubusercontent.com/Sfshaza/bb51e3b7df4ebbf3dfd02a4a38db2655/raw/57c25b976ec34d56591cb898a3df0b320e903b99/pubspec.yaml)
(**pubspec.yaml**文件不会再更改。)
* [**lib/main.dart**](https://gist.githubusercontent.com/Sfshaza/bb51e3b7df4ebbf3dfd02a4a38db2655/raw/57c25b976ec34d56591cb898a3df0b320e903b99/main.dart)

---

# 第3步：添加有状态的控件

State<em>less</em>控件是不可改变的，这意味着它们的属性不能改变&mdash;&mdash;所有的值都是final的。

State<em>ful</em>控件在其生命周期保持的状态可能会变化，实现一个有状态的控件至少需要两个类：: 1) a StatefulWidget class
that creates an instance of 2) a State class. The StatefulWidget
class is, itself, immutable, but the State class persists over the
lifetime of the widget.

In this step, you’ll add a stateful widget, RandomWords, which creates
its State class, RandomWordsState. The State class will eventually
maintain the proposed and favorite word pairs for the widget.

<ol markdown="1">
<li markdown="1"> Add the stateful RandomWords widget to main.dart.
    It can go anywhere in the file, outside of MyApp, but the solution
    places it at the bottom of the file. The RandomWords widget does little
    else besides creating its State class:

<!-- skip -->
{% prettify dart %}
class RandomWords extends StatefulWidget {
  @override
  createState() => new RandomWordsState();
}
{% endprettify %}
</li>

<li markdown="1"> Add the RandomWordsState class. Most of the
    app’s code resides in this class, which maintains the state for the
    RandomWords widget. This class will save the generated word pairs,
    which grow infinitely as the user scrolls, and also favorite
    word pairs, as the user adds or removes them from the list by
    toggling the heart icon.

You’ll build this class bit by bit. To begin, create a minimal
class by adding the highlighted text:

<!-- skip -->
{% prettify dart %}
[[highlight]]class RandomWordsState extends State<RandomWords> {[[/highlight]]
[[highlight]]}[[/highlight]]
{% endprettify %}
</li>

<li markdown="1"> After adding the state class, the IDE complains that
    the class is missing a build method. Next, you'll add a basic
    build method that generates the word pairs by moving the
    word generation code from MyApp to RandomWordsState.

Add the build method to RandomWordState, as shown
by the highlighted text:

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  [[highlight]]@override[[/highlight]]
  [[highlight]]Widget build(BuildContext context) {[[/highlight]]
    [[highlight]]final wordPair = new WordPair.random();[[/highlight]]
    [[highlight]]return new Text(wordPair.asPascalCase);[[/highlight]]
  [[highlight]]}[[/highlight]]
}
{% endprettify %}
</li>

<li markdown="1"> Remove the word generation code from MyApp by making
    the highlighted changes below:

<!-- skip -->
{% prettify dart %}
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    [[strike]]final wordPair = new WordPair.random();[[/strike]]  // Delete this line

    return new MaterialApp(
      title: 'Welcome to Flutter',
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('Welcome to Flutter'),
       ),
        body: new Center(
          //child: new [[highlight]]Text(wordPair.asPascalCase),[[/highlight]] // Change the highlighted text to...
          child: new [[highlight]]RandomWords(),[[/highlight]] // ... this highlighted text
        ),
      ),
    );
  }
}
{% endprettify %}
</li>

</ol>

Restart the app. If you try to hot reload, you might see a warning:

{% prettify sh %}
Reloading...
Not all changed program elements ran during view reassembly; consider
restarting.
{% endprettify %}

It may be a false positive, but consider restarting in order to make sure
that your changes are reflected in the app's UI.

The app should behave as before, displaying a word
pairing each time you hot reload or save the app.

<center><img src="images/step3-screenshot.png" alt="screenshot at completion of third step"></center>

<p class="h2-like">Problems?</p>

If your app isn't running correctly, you can use the code
at the following link to get back on track.

* [**lib/main.dart**](https://gist.githubusercontent.com/Sfshaza/d7f13ddd8888556232476be8578efe40/raw/329c397b97309ce99f834bf70ebb90778baa5cfe/main.dart)

---

# Step 4: Create an infinite scrolling ListView

In this step, you'll expand RandomWordsState to generate
and display a list of word pairings. As the user scrolls, the list
displayed in a ListView widget, grows infinitely. ListView's
`builder` factory constructor allows you to build a list view
lazily, on demand.

<ol markdown="1">

<li markdown="1"> Add a `_suggestions` list to the RandomWordsState
class for saving suggested word pairings. The variable begins with
an underscore (`_`)&mdash;prefixing an identifier with an underscore enforces
privacy in the Dart language.

Also, add a `biggerFont` variable for making the font size larger.

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  [[highlight]]final _suggestions = <WordPair>[];[[/highlight]]

  [[highlight]]final _biggerFont = const TextStyle(fontSize: 18.0);[[/highlight]]
  ...
}
{% endprettify %}
</li>

<li markdown="1"> Add a `_buildSuggestions()` function to the RandomWordsState
class. This method builds the ListView that displays the suggested word
pairing.

The ListView class provides a builder property, `itemBuilder`,
a factory builder and callback function specified as an anonymous function.
Two parameters are passed to the function&mdash;the BuildContext,
and the row iterator, `i`. The iterator begins at 0 and increments
each time the function is called, once for every suggested word pairing.
This model allows the suggested list to grow infinitely as the user scrolls.

Add the highlighted lines below:

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  ...
  [[highlight]]Widget _buildSuggestions() {[[/highlight]]
    [[highlight]]return new ListView.builder([[/highlight]]
      [[highlight]]padding: const EdgeInsets.all(16.0),[[/highlight]]
      // The itemBuilder callback is called once per suggested word pairing,
      // and places each suggestion into a ListTile row.
      // For even rows, the function adds a ListTile row for the word pairing.
      // For odd rows, the function adds a Divider widget to visually
      // separate the entries. Note that the divider may be difficult
      // to see on smaller devices.
      [[highlight]]itemBuilder: (context, i) {[[/highlight]]
        // Add a one-pixel-high divider widget before each row in theListView.
        [[highlight]]if (i.isOdd) return new Divider();[[/highlight]]

        // The syntax "i ~/ 2" divides i by 2 and returns an integer result.
        // For example: 1, 2, 3, 4, 5 becomes 0, 1, 1, 2, 2.
        // This calculates the actual number of word pairings in the ListView,
        // minus the divider widgets.
        [[highlight]]final index = i ~/ 2;[[/highlight]]
        // If you've reached the end of the available word pairings...
        [[highlight]]if (index >= _suggestions.length) {[[/highlight]]
          // ...then generate 10 more and add them to the suggestions list.
          [[highlight]]_suggestions.addAll(generateWordPairs().take(10));[[/highlight]]
        [[highlight]]}[[/highlight]]
        [[highlight]]return _buildRow(_suggestions[index]);[[/highlight]]
      [[highlight]]}[[/highlight]]
    [[highlight]]);[[/highlight]]
  [[highlight]]}[[/highlight]]
}
{% endprettify %}
</li>

<li markdown="1"> The `_buildSuggestions` function calls `_buildRow` once per
word pair. This function displays each new pair in a ListTile,
which allows you to make the rows more attractive in the next step.

Add a `_buildRow` function to RandomWordsState:

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  ...

  [[highlight]]Widget _buildRow(WordPair pair) {[[/highlight]]
    [[highlight]]return new ListTile([[/highlight]]
      [[highlight]]title: new Text([[/highlight]]
        [[highlight]]pair.asPascalCase,[[/highlight]]
        [[highlight]]style: _biggerFont,[[/highlight]]
      [[highlight]]),[[/highlight]]
    [[highlight]]);[[/highlight]]
  [[highlight]]}[[/highlight]]
}
{% endprettify %}
</li>

<li markdown="1"> Update the build method for RandomWordsState to use
`_buildSuggestions()`, rather than directly calling the word
generation library. Make the highlighted changes:

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  ...
  @override
  Widget build(BuildContext context) {
    [[strike]]final wordPair = new WordPair.random();[[/strike]] // Delete these two lines.
    [[strike]]Return new Text(wordPair.asPascalCase);[[/strike]]
    [[highlight]]return new Scaffold ([[/highlight]]
      [[highlight]]appBar: new AppBar([[/highlight]]
        [[highlight]]title: new Text('Startup Name Generator'),[[/highlight]]
      [[highlight]]),[[/highlight]]
    [[highlight]]body: _buildSuggestions(),[[/highlight]]
    [[highlight]]);[[/highlight]]
  }
  ...
}
{% endprettify %}
</li>

<li markdown="1"> Update the build method for MyApp.
    Remove the Scaffold and AppBar instances from MyApp.
    These will be managed by RandomWordsState, which makes it easier to
    change the name of the route in the app bar as the user
    navigates from one screen to another in the next step.

Replace the original method with the highlighted build method below:

<!-- skip -->
{% prettify dart %}
class MyApp extends StatelessWidget {
  @override
  [[highlight]]Widget build(BuildContext context) {[[/highlight]]
    [[highlight]]return new MaterialApp([[/highlight]]
      [[highlight]]title: 'Startup Name Generator',[[/highlight]]
      [[highlight]]home: new RandomWords(),[[/highlight]]
    [[highlight]]);[[/highlight]]
  [[highlight]]}[[/highlight]]
}
{% endprettify %}
</li>

</ol>

Restart the app. You should see a list of word pairings. Scroll down
as far as you want and you will continue to see new word pairings.

<center><img src="images/step4-screenshot.png" alt="screenshot at completion of fourth step"></center>

<p class="h2-like">Problems?</p>

If your app isn't running correctly, you can use the code
at the following link to get back on track.

* [**lib/main.dart**](https://gist.githubusercontent.com/Sfshaza/d6f9460a04d3a429eb6ac0b0f07da564/raw/34fe240f4122435c871bb737708ee0357741801c/main.dart)

---

# Step 5: Add interactivity

In this step, you'll add tappable heart icons to each row.
When the user taps an entry in the list, toggling its
"favorited" state, that word pairing is added or removed from a
set of saved favorites.

<ol markdown="1">
<li markdown="1"> Add a `_saved` Set to RandomWordsState. This Set stores
    the word pairings that the user favorited. Set is preferred to List
    because a properly implemented Set doesn't allow duplicate entries.

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  final _suggestions = <WordPair>[];

  [[highlight]]final _saved = new Set<WordPair>();[[/highlight]]

  final _biggerFont = const TextStyle(fontSize: 18.0);
  ...
}
{% endprettify %}
</li>

<li markdown="1"> In the `_buildRow` function, add an `alreadySaved`
    check to ensure that a word pairing hasn't already been added to
    favorites.

<!-- skip -->
{% prettify dart %}
  Widget _buildRow(WordPair pair) {
    [[highlight]]final alreadySaved = _saved.contains(pair);[[/highlight]]
    ...
  }
{% endprettify %}
</li>

<li markdown="1"> Also in `_buildRow()`, add heart-shaped icons to the
    ListTiles to enable favoriting. Later, you'll add the ability to
    interact with the heart icons.

Add the highlighted lines below:

<!-- skip -->
{% prettify dart %}
  Widget _buildRow(WordPair pair) {
    final alreadySaved = _saved.contains(pair);
    return new ListTile(
      title: new Text(
        pair.asPascalCase,
        style: _biggerFont,
      ),
      [[highlight]]trailing: new Icon([[/highlight]]
        [[highlight]]alreadySaved ? Icons.favorite : Icons.favorite_border,[[/highlight]]
        [[highlight]]color: alreadySaved ? Colors.red : null,[[/highlight]]
      [[highlight]]),[[/highlight]]
    );
  }
{% endprettify %}
</li>

<li markdown="1"> Restart the app. You should now see open hearts on
    each row, but they aren't yet interactive.
</li>

<li markdown="1"> Make the hearts tappable in the `_buildRow`
    function. If a word entry has already been added to favorites,
    tapping it again removes it from favorites.
    When the heart has been tapped, the function calls
    `setState()` to notify the framework that state has changed.

Add the highlighted lines:

<!-- skip -->
{% prettify dart %}
  Widget _buildRow(WordPair pair) {
    final alreadySaved = _saved.contains(pair);
    return new ListTile(
      title: new Text(
        pair.asPascalCase,
        style: _biggerFont,
      ),
      trailing: new Icon(
        alreadySaved ? Icons.favorite : Icons.favorite_border,
        color: alreadySaved ? Colors.red : null,
      ),
      [[highlight]]onTap: () {[[/highlight]]
        [[highlight]]setState(() {[[/highlight]]
          [[highlight]]if (alreadySaved) {[[/highlight]]
            [[highlight]]_saved.remove(pair);[[/highlight]]
          [[highlight]]} else {[[/highlight]]
            [[highlight]]_saved.add(pair);[[/highlight]]
          [[highlight]]}[[/highlight]]
        [[highlight]]});[[/highlight]]
      [[highlight]]},[[/highlight]]
    );
  }
{% endprettify %}
</li>
</ol>

<aside class="alert alert-success" markdown="1">
<i class="fa fa-lightbulb-o"> </i> **Tip:**
In Flutter's react style framework, calling `setState()` triggers
a call to the `build()` method for the State object, resulting in
an update to the UI.
</aside>

Hot reload the app. You should be able to tap any row to favorite, or unfavorite,
the entry. Note that tapping a row generates an implicit ink splash animation
that emanates from the heart icon.

<center><img src="images/step5-screenshot.png" alt="screenshot at completion of 5th step"></center>

<p class="h2-like">Problems?</p>

If your app isn't running correctly, you can use the code
at the following link to get back on track.

* [**lib/main.dart**](https://gist.githubusercontent.com/Sfshaza/936ce0059029a8c6e88aaa826a3789cd/raw/a3065d5c681a81eff32f75a9cd5f4d9a5b24f9ff/main.dart)

---

# Step 6: Navigate to a new screen

In this step, you'll add a new screen (called a _route_ in Flutter) that
displays the favorites. You'll learn how to navigate between the home route
and the new route.

In Flutter, the Navigator manages a stack containing the app's routes.
Pushing a route onto the Navigator's stack, updates the display to that route.
Popping a route from the Navigator's stack, returns the display to the previous
route.

<ol markdown="1">
<li markdown="1"> Add a list icon to the AppBar in the build method
    for RandomWordsState.  When the user clicks the list icon, a new
    route that contains the favorites items is pushed to the Navigator,
    displaying the icon.

<aside class="alert alert-success" markdown="1">
<i class="fa fa-lightbulb-o"> </i> **Tip:**
Some widget properties take a single widget (`child`), and other properties,
such as `action`, take an array of widgets (`children`),
as indicated by the square brackets (`[]`).
</aside>

Add the icon and its corresponding action to the build method:

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  ...
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('Startup Name Generator'),
        [[highlight]]actions: <Widget>[[[/highlight]]
          [[highlight]]new IconButton(icon: new Icon(Icons.list), onPressed: _pushSaved),[[/highlight]]
        [[highlight]]],[[/highlight]]
      ),
      body: _buildSuggestions(),
    );
  }
  ...
}
{% endprettify %}
</li>

<li markdown="1"> Add a `_pushSaved()` function to the RandomWordsState class.

<!-- skip -->
{% prettify dart %}
class RandomWordsState extends State<RandomWords> {
  ...
  [[highlight]]void _pushSaved() {[[/highlight]]
  [[highlight]]}[[/highlight]]
}
{% endprettify %}

Hot reload the app. The list icon appears in the app bar.
Tapping it does nothing yet, because the `_pushSaved` function is empty.
</li>

<li markdown="1"> When the user taps the list icon in the app bar,
    build a route and push it to the Navigator's stack.
    This action changes the screen to display the new route.

The content for the new page is built in MaterialPageRoute's `builder`
property, in an anonymous function.

Add the call to Navigator.push, as shown by the highlighted code,
which pushes the route to the Navigator's stack.

<!-- skip -->
{% prettify dart %}
  [[highlight]]void _pushSaved() {[[/highlight]]
    [[highlight]]Navigator.of(context).push([[/highlight]]
    [[highlight]]);[[/highlight]]
  [[highlight]]}[[/highlight]]
{% endprettify %}
</li>

<li markdown="1"> Add the MaterialPageRoute and its builder. For now,
    add the code that generates the ListTile rows. The `divideTiles()`
    method of ListTile adds horizontal spacing between each ListTile.
    The `divided` variable holds the final rows, converted to a list
    by the convienice function, `toList()`.

<!-- skip -->
{% prettify dart %}
  void _pushSaved() {
    Navigator.of(context).push(
      [[highlight]]new MaterialPageRoute([[/highlight]]
        [[highlight]]builder: (context) {[[/highlight]]
          [[highlight]]final tiles = _saved.map([[/highlight]]
                [[highlight]](pair) {[[/highlight]]
              [[highlight]]return new ListTile([[/highlight]]
                [[highlight]]title: new Text([[/highlight]]
                  [[highlight]]pair.asPascalCase,[[/highlight]]
                  [[highlight]]style: _biggerFont,[[/highlight]]
                [[highlight]]),[[/highlight]]
              [[highlight]]);[[/highlight]]
            [[highlight]]},[[/highlight]]
          [[highlight]]);[[/highlight]]
          [[highlight]]final divided = ListTile[[/highlight]]
              [[highlight]].divideTiles([[/highlight]]
            [[highlight]]context: context,[[/highlight]]
            [[highlight]]tiles: tiles,[[/highlight]]
          [[highlight]])[[/highlight]]
              [[highlight]].toList();[[/highlight]]
        [[highlight]]},[[/highlight]]
      [[highlight]]),[[/highlight]]
    );
  }
{% endprettify %}
</li>

<li markdown="1"> The builder property returns a Scaffold,
    containing the app bar for the new route, named
    "Saved Suggestions." The body of the new route
    consists of a ListView containing the ListTiles rows;
    each row is separated by a divider.

Add the highlisted code below:

<!-- skip -->
{% prettify dart %}
  void _pushSaved() {
    Navigator.of(context).push(
      new MaterialPageRoute(
        builder: (context) {
          final tiles = _saved.map(
                (pair) {
              return new ListTile(
                title: new Text(
                  pair.asPascalCase,
                  style: _biggerFont,
                ),
              );
            },
          );
          final divided = ListTile
              .divideTiles(
            context: context,
            tiles: tiles,
          )
              .toList();

          [[highlight]]return new Scaffold([[/highlight]]
            [[highlight]]appBar: new AppBar([[/highlight]]
              [[highlight]]title: new Text('Saved Suggestions'),[[/highlight]]
            [[highlight]]),[[/highlight]]
            [[highlight]]body: new ListView(children: divided),[[/highlight]]
          [[highlight]]);[[/highlight]]
        },
      ),
    );
  }
{% endprettify %}
</li>

<li markdown="1"> Hot reload the app. Favorite some of the selections and
    tap the list icon in the app bar. The new route appears containing
    the favorites. Note that the Navigator adds a "Back" button to the
    app bar. You didn't have to explicitly implement Navigator.pop.
    Tap the back button to return to the home route.
</li>
</ol>

<center><img src="images/step6a-screenshot.png" alt="screenshot at completion of 6th step"><img src="images/step6b-screenshot.png" alt="second route"></center>

<p class="h2-like">Problems?</p>

If your app isn't running correctly, you can use the code
at the following link to get back on track.

* [**lib/main.dart**](https://gist.github.com/Sfshaza/bc5547e112e4dc3a1aa87afdf917caeb)

---
# Step 7: Change the UI using Themes

In this final step, you'll play with the app's theme. The
_theme_ controls the look and feel of your app. You can use
the default theme, which is dependent on the physical device
or emulator, or you can customize the theme to reflect your branding.

<ol markdown="1">
<li markdown="1"> You can easily change an app's theme by configuring
    the ThemeData class.  Your app currently uses the default theme,
    but you'll be changing the primary color to be white.

Change the app's theme to white by adding the highlighted code to MyApp:

<!-- skip -->
{% prettify dart %}
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Startup Name Generator',
      [[highlight]]theme: new ThemeData([[/highlight]]
        [[highlight]]primaryColor: Colors.white,[[/highlight]]
      [[highlight]]),[[/highlight]]
      home: new RandomWords(),
    );
  }
}
{% endprettify %}
</li>

<li markdown="1"> Hot reload the app. Notice that the entire background is white,
even the app bar.
</li>

<li markdown="1"> As an exercise for the reader, use
[ThemeData](https://docs.flutter.io/flutter/material/ThemeData-class.html)
to change other aspects of the  UI. The
[Colors](https://docs.flutter.io/flutter/material/Colors-class.html)
class in the Material library provides many color constants you can play with,
and hot reload makes experimenting with the UI quick and easy.
</li>
</ol>

<center><img src="images/step7-themes.png" alt="screenshot at completion of 7th step"></center>

<p class="h2-like">有问题?</p>

如果你又不能正常运行了，请使用以下链接中的代码查看最终应用的代码。

* [**lib/main.dart**](https://gist.githubusercontent.com/Sfshaza/c07c91a4061fce4b5eacaaf4d82e4993/raw/4001a72c0133b97c8e16bdeb3195ca03525696bd/main.dart)

---

# 干得漂亮！

您已经编写了一个可在iOS和Android上运行的交互式Flutter应用程序。在这个codelab中，你已经完成了：

* 从头开始创建一个Flutter app。
* 编写Dart语言代码。
* 使用外部的第三方库。
* 使用hot reload加快开发。
* 实现了一个有状态的控件，为你的应用增加可交互性。
* 用ListView和ListTiles创建了一个延迟加载的无限滚动列表。
* 创建了一个路由，并添加在主页面路由和新路由之间跳转的逻辑。
* 学习如何使用主题更改应用程序的UI外观。

<p class="h2-like">下一步</p>

[下一步: 学习更多](/get-started/learn-more/)
