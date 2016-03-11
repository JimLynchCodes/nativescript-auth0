# Auth0 Social Authentication Plugin for NativeScript

[Auth0: Identity made simple for developers](https://auth0.com)

## Setup
- Login to your auth0 account, get your domain\clientid info
- Setup the allowed providers, what you pick here is what AUTOMATICALLY shows up on the login box (so cool and easy)
- Make sure you have an allowed callback url per the [docs](https://auth0.com/docs/quickstart/native-mobile/ios-objc/aspnet-webapi#before-starting)

## iOS
Initalize on load in app.js, put this before application.start();

``` js
if (application.ios) {
    var __extends = this.__extends || function (d, b) {
        for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
        function __() { this.constructor = d; }
        __.prototype = b.prototype;
        d.prototype = new __();
    };
    
    var appDelegate = (function (_super) {
        __extends(appDelegate, _super);
        function appDelegate() {
            _super.apply(this, arguments);
        }
        
        appDelegate.prototype.applicationDidFinishLaunchingWithOptions = function (application, launchOptions) {
            global.a0lock = new A0Lock();
            
        };
        
        appDelegate.ObjCProtocols = [UIApplicationDelegate];
        return appDelegate;
    })(UIResponder);
    application.ios.delegate = appDelegate;
}

//Application.start goes somewhere below here
```

In your login script reference the plugin
``` js
var auth0 = require("nativescript-auth0");
```

This is how you open the default lock screen, returns a Promise
``` js
auth0.show().then(function(args){
		console.log(args.profile);
		console.log(args.token);
	});
```

This is how you open a single Idp, returns a Promise.  So you could have like a custom button on your page which calls this to trigger the Idp login screen.

``` js
var connectionName = "my-auth0-connection"; //Connection name from the auth0 backend
auth0.showIdp(connectionName).then(function(args){
		console.log(args.profile);
		console.log(args.token);
	});
```
The connectionName parameter is the name of the connection from your auth0 backend 

## Setting Credentials
### iOS
Credentials for iOS are set in the apps info.plist.

- Replace DOMAIN-GOES-HERE with your auth0 domain
- Replace CLIENTID-GOES-HERE with your auth0 clientId, note the URLScheme needs an a0 prefix, find replace should just work.

## Android

This won't work yet until {N} can support impliments on the application

[Auth0 Sample](https://auth0.com/docs/quickstart/native-mobile/android/aspnet-webapi#3-initialize-lock)

[Git Issue #283](https://github.com/NativeScript/android-runtime/issues/283)

## Styling

Auth0 lets you customize a lot about the widget, I've wrapped them into some helpers.  
* Pass the colors as Hex codes, convert to UIColor on iOS
* Image the name of the image which exists in your resources
```
// Examples
var imageName = "Icon";
var imageName = "Default";
```
* Fonts require a NativeScript font object
```
// Example
var fontModule = require("ui/styling/font");
var font = new fontModule.Font("Open Sans", 20, "normal", "normal");
auth0.themeTitle("#666666", font);
```
* Must call your theme BEFORE calling the .show method

```
auth0.themePrimaryButton(normalColor, highlightColor, textColor, font);
auth0.themeSecondaryButton(backgroundColor, textColor, font, normalImage, highlightedImage);
auth0.themeTextField(textColor, placeholderTextColor, iconColor, font);
auth0.themeTitle(textColor, font);
auth0.themeIcon(backgroundColor, imageName);
auth0.themeBackground(backgroundColor, imageName);
auth0.themeDescription(textColor, font);
auth0.themeSeperator(textColor, font);
auth0.themeCredentialBox(borderColor, separatorColor, backgroundColor);
auth0.themeCloseButton(tintColor);
```

## Parameters
[Documentation](https://auth0.com/docs/libraries/lock-ios/sending-authentication-parameters)
``` js
auth0.addParameter("foo", "bar").then(function(args){
        console.log(args);    
    });
```

``` js
auth0.addScopeParameter("login").then(function(args){
        console.log(args);    
    });
```

## ISSUES
### iOS Cocoapod error on build: 
Creating shallow clone of spec repo `master` from `https://github.com/CocoaPods/Specs.git`
[!] Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`.
You can try adding it manually in `~/.cocoapods/repos` or via `pod repo add`.
Processing node_modules failed. Error:Error: Command sandbox-pod failed with exit code 1

####Fix:
in the terminal run 'pod setup'
(which should create cocoapods/repos/master)

## TODO
* Custom Login
* Token Helpers, store in Keychain