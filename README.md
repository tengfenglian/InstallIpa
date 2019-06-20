iOS使用`itms-services`协议来分发开发者打的ipa安装包，主要是企业开发账号分发及非企业开发账号测试阶段分发给测试人员的安装包。（[蒲公英](https://www.pgyer.com/)、[fir.im](https://fir.im) 正是使用这个协议来分发安装包的）

现在需要准备以下所需要的材料
ipa安装包、plist文件、及一个网页![材料](https://img-blog.csdnimg.cn/20190528154405451.png)

**ipa安装包**
这里的ipa安装包是通过公司开发者账号打包的，需要设备被加入到开发者账号中才能正常安装使用，这里就不多说啦。

**plist文件配置**
配置文件中主要需要修改的是安装包地址`url`、`bundle-identifier`、`bundle-version`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>items</key>
	<array>
		<dict>
			<key>assets</key>
			<array>
				<dict>
					<key>kind</key>
					<string>software-package</string>
					<key>url</key>
                    <!--         ！！！安装包地址           -->
					<string>https://tengfenglian.github.io/InstallIpa/PushDemo.ipa</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>display-image</string>
					<key>needs-shine</key>
					<true/>
					<key>url</key>
					<string>http://img3.100bt.com/upload/ttq/20140523/1400836956582_middle.jpg</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>full-size-image</string>
					<key>needs-shine</key>
					<true/>
					<key>url</key>
					<string>http://img3.100bt.com/upload/ttq/20140523/1400836956582_middle.jpg</string>
				</dict>
			</array>
			<key>metadata</key>
			<dict>
				<key>bundle-identifier</key>
                <!--         bundle identifier           -->
				<string>com.ltf.PushDemo</string>
				<key>bundle-version</key>
                <!--         bundle version           -->
				<string>1.0</string>
				<key>kind</key>
				<string>software</string>
				<key>subtitle</key>
				<string>Install App Subtitle</string>
				<key>title</key>
				<string>Install App Title</string>
			</dict>
		</dict>
	</array>
</dict>
</plist>

```

**html页面**
*使用 `itms-services://?action=download-manifest&url=` + `plist文件的地址`  实现安装包的加载,plist文件的地址必须是https；*
在Safari浏览器中加载下面网页响应跳转会有安装提示
```html
<meta name="viewport" content="initial-scale=1.0"/>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta name='apple-itunes-app' content='app-id=477927812; text/html; charset=utf-8' http-equiv="Content-Type">
    <title>安装App</title>
</head>
<body>
<!--  itms-services://?action=download-manifest&url= + plist文件的地址  -->
    <a href='itms-services://?action=download-manifest&url=https://tengfenglian.github.io/InstallIpa/load.plist'>一键安装</a>
</body>
</html>
```
在使用WKWebView中加载该网页响应跳转时需要拦截这个`itms-services` url scheme 并打开就会有安装提示

```objectivec
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    NSURLRequest *request = navigationAction.request;
    NSURL *url = navigationAction.request.URL;
    NSString *scheme = [url scheme];
    if ([scheme isEqualToString:@"itms-services"]) {
        if (@available(iOS 10.0, *)) {
            [[UIApplication sharedApplication] openURL:url options:@{} completionHandler:nil];
        } else {
            [[UIApplication sharedApplication] openURL:url];
        }
        decisionHandler(WKNavigationActionPolicyCancel);
        return;
    }
    decisionHandler(WKNavigationActionPolicyAllow);
}
```
![效果图](https://img-blog.csdnimg.cn/20190528163030793.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1OTIwNjc=,size_16,color_FFFFFF,t_70)


这里我使用[GitHub](https://github.com)来保存上面的材料,在GitHub中创建项目并提交以上三个文件，链接配置查看这篇[文章](https://blog.csdn.net/qq_25479327/article/details/78778282)。
