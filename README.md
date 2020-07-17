# InfoTest

NOTICE
We are working closely with the Flutter Team to integrate allthe###(@http://www.baidu.com/@)### Community Plugin features in the Official WebView Plugin. We will try our best to resolve PRs and Bugfixes, but our priority right now is to merge our two code-bases. Once the merge is complete we will deprecate the Community Plugin in favor of the Official one.

Thank you for all your support, hopefully you'll also show it for Official Plugin too.

Keep Fluttering!

Flutter WebView Plugin
pub package

Plugin that allows Flutter to communicate with a native WebView.

Warning: The webview is not integrated in the widget tree, it is a native view on top of the flutter view. You won't be able see snackbars, dialogs, or other flutter widgets that would overlap with the region of the screen taken up by the webview.

The getSafeAcceptedType() function is available only for minimum SDK of 21. eval() function only supports SDK of 19 or greater for evaluating Javascript.

Getting Started
For help getting started with Flutter, view our online documentation.

iOS
In order for plugin to work correctly, you need to add new key to ios/Runner/Info.plist

<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
    <key>NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
NSAllowsArbitraryLoadsInWebContent is for iOS 10+ and NSAllowsArbitraryLoads for iOS 9.

How it works
Launch WebView Fullscreen with Flutter navigation
new MaterialApp(
      routes: {
        "/": (_) => new WebviewScaffold(
          url: "https://www.google.com",
          appBar: new AppBar(
            title: new Text("Widget webview"),
          ),
        ),
      },
    );
Optional parameters hidden and initialChild are available so that you can show something else while waiting for the page to load. If you set hidden to true it will show a default CircularProgressIndicator. If you additionally specify a Widget for initialChild you can have it display whatever you like till page-load.

e.g. The following will show a read screen with the text 'waiting.....'.

return new MaterialApp(
  title: 'Flutter WebView Demo',
  theme: new ThemeData(
    primarySwatch: Colors.blue,
  ),
  routes: {
    '/': (_) => const MyHomePage(title: 'Flutter WebView Demo'),
    '/widget': (_) => new WebviewScaffold(
      url: selectedUrl,
      appBar: new AppBar(
        title: const Text('Widget webview'),
      ),
      withZoom: true,
      withLocalStorage: true,
      hidden: true,
      initialChild: Container(
        color: Colors.redAccent,
        child: const Center(
          child: Text('Waiting.....'),
        ),
      ),
    ),
  },
);
FlutterWebviewPlugin provide a singleton instance linked to one unique webview, so you can take control of the webview from anywhere in the app

listen for events

final flutterWebviewPlugin = new FlutterWebviewPlugin();

flutterWebviewPlugin.onUrlChanged.listen((String url) {

});
Listen for scroll event in webview
final flutterWebviewPlugin = new FlutterWebviewPlugin();
flutterWebviewPlugin.onScrollYChanged.listen((double offsetY) { // latest offset value in vertical scroll
  // compare vertical scroll changes here with old value
});

flutterWebviewPlugin.onScrollXChanged.listen((double offsetX) { // latest offset value in horizontal scroll
  // compare horizontal scroll changes here with old value
});
Note: Do note there is a slight difference is scroll distance between ios and android. Android scroll value difference tends to be larger than ios devices.

Hidden WebView
final flutterWebviewPlugin = new FlutterWebviewPlugin();

flutterWebviewPlugin.launch(url, hidden: true);
Close launched WebView
flutterWebviewPlugin.close();
Webview inside custom Rectangle
final flutterWebviewPlugin = new FlutterWebviewPlugin();

flutterWebviewPlugin.launch(url,
  fullScreen: false,
  rect: new Rect.fromLTWH(
    0.0,
    0.0,
    MediaQuery.of(context).size.width,
    300.0,
  ),
);
Injecting custom code into the webview
Use flutterWebviewPlugin.evalJavaScript(String code). This function must be run after the page has finished loading (i.e. listen to onStateChanged for events where state is finishLoad).

If you have a large amount of JavaScript to embed, use an asset file. Add the asset file to pubspec.yaml, then call the function like:

Future<String> loadJS(String name) async {
  var givenJS = rootBundle.loadString('assets/$name.js');
  return givenJS.then((String js) {
    flutterWebViewPlugin.onStateChanged.listen((viewState) async {
      if (viewState.type == WebViewState.finishLoad) {
        flutterWebViewPlugin.evalJavascript(js);
      }
    });
  });
}
Accessing local files in the file system
Set the withLocalUrl option to true  in the launch function or in the Webview scaffold to enable support for local URLs.

Note that, on iOS, the localUrlScope option also needs to be set to a path to a directory. All files inside this folder (or subfolder) will be allowed access. If ommited, only the local file being opened will have access allowed, resulting in no subresources being loaded. This option is ignored on Android.

Ignoring SSL
