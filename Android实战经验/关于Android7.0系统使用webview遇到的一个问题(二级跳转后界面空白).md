## 前言

webview在之前的项目开发中我还尚未真正使用过，也仅仅停留在自己写的demo。不过早就听闻过此控件的坑不少，这次就被领教了。

因为有个项目需要用到webview，所以我还是先看看别人大神们总结过的资料，看看什么地方可以运用到项目中。嗯，我就是看了这篇[WebView·开车指南](http://www.jianshu.com/p/6a7c91f1d804)之后，就开始上路了。

一路上并未遇到什么障碍，用公司的测试机也未出现什么异常的地方，直到把项目运行在自己的手机上时，我掉进坑里！！！

> 什么坑呢？在Android7.0系统中，第一次webview加载时，显示完全没有问题的，当我点击webview里的内容进行二级跳转的时候，就会显示一片空白，什么都没有！！

在google、百度一轮之后，发现遇到这样的问题的人很少，资料也基本上没有。这时候就只能靠自己了。

首先，先看看为什么会出现空白页面，所以我在**onPageFinished()**的回调方法里，对url打了个log，输出的是**about:blank**。这是怎么回事呢？点击事件后连个url都不见了吗？

然后我把目光都放在了下图中的这个回调方法，因为可能就在loadUrl的时候出了什么问题了。


![截图来自WebView·开车指南](http://upload-images.jianshu.io/upload_images/2355123-957e1c72271a7564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```java
            //是否在webview内加载页面
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                view.loadUrl(request.toString());
                return true;
            }
```

打Log一看，果然load的不是一个url，而是一个对象。于是我看看request这个对象下才看到我所要跳转的url。嗯，再看看这个request持有的方法就有个**getUrl()** 方法，调用之后就报错了，因为只能在5.0系统以上才能用，直觉告诉我，问题就是出现在这里了，最后，我在这个回调方法中稍微改动了下，如下：

![截图](http://upload-images.jianshu.io/upload_images/2355123-9249518005f9bdfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```java
            //是否在webview内加载页面
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                    view.loadUrl(request.getUrl().toString());
                } else {
                    view.loadUrl(request.toString());
                }
                return true;
            }
```

运行项目，ok了！

当然，这里面肯定有很多学问和为什么在里头，鉴于项目还在赶进度我就没有继续深究了，希望搞懂这个问题的朋友能告知下！

**最后，小弟不才，还望多多指教！**
