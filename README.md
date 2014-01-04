# JavaScriptBridge

[![Version](http://cocoapod-badges.herokuapp.com/v/JavaScriptBridge/badge.png)](http://cocoadocs.org/docsets/JavaScriptBridge)
[![Platform](http://cocoapod-badges.herokuapp.com/p/JavaScriptBridge/badge.png)](http://cocoadocs.org/docsets/JavaScriptBridge)
[![Build Status](https://travis-ci.org/kishikawakatsumi/JavaScriptBridge.png?branch=master)](https://travis-ci.org/kishikawakatsumi/JavaScriptBridge)

Write iOS apps in Javascript! JavaScriptBridge provides the way to write iOS apps with JavaScript.
JavaScriptBridge bridges Cocoa touch to JavaScriptCore (JavaScriptCore.framework is introduced in iOS 7).

## Usage

```objc
// Retrieve the prepared context
JSContext *context = [JSBScriptingSupport globalContext];

// Add framework support if needed.
// ('Foundation', 'UIKit', 'QuartzCore' enabled by default.)
[context addScriptingSupport:@"MapKit"];
[context addScriptingSupport:@"MessageUI"];

// Evaluate script
[context evaluateScript:
 @"var window = UIWindow.new();"
 @"window.frame = UIScreen.mainScreen().bounds;"
 @"window.backgroundColor = UIColor.whiteColor();"
 @"window.makeKeyAndVisible();"
];
```

### Syntax / Naming conventions

**Class name**
- Same as Objectige-C

**Variable declaration**
- Get rid of `Type` instead use `var`

*Objective-C*
```objc
UILabel *label;
```

*JavaScript*
```javascript
var label;
```

**Properties**
- Use dot syntax

```objc
UISlider *slider = [[UISlider alloc] initWithFrame:frame];
slider.backgroundColor = [UIColor clearColor];
slider.minimumValue = 0.0;
slider.maximumValue = 100.0;
slider.continuous = YES;
slider.value = 50.0;
```

*JavaScript*
```javascript
var slider = UISlider.alloc().initWithFrame(frame);
slider.backgroundColor = UIColor.clearColor();
slider.minimumValue = 0.0;
slider.maximumValue = 100.0;
slider.continuous = true;
slider.value = 50.0;
```

**Invoking method**
- Use dot syntax
- All colons are removed from the selector
- Any lowercase letter that had followed a colon will be capitalized

*Objective-C*
```objc
UIWindow *window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
```

*JavaScript*
```javascript
var window = UIWindow.alloc().initWithFrame(UIScreen.mainScreen().bounds);
```

**Struct (CGRect, NSRange, etc.)**
- Use Hashes

*Objective-C*
```objc
UIView *view = [UIView new];
view.frame = CGRectMake(20, 80, 280, 80);

CGFloat x = view.frame.origin.x;
CGFloat width = view.frame.size.width;
```

*JavaScript*
```javascript
var view = UIView.new();
view.frame = {x: 20, y: 80, width: 280, height: 80};

var x = view.frame.x; // => 20
var width = view.frame.width; // => 280
```

## Examples
###Hello world on JavaScriptBridge

This is the most simple way.

```objc
@implementation JSBAppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    JSContext *context = [JSBScriptingSupport globalContext];
    
    [context evaluateScript:
     @"var window = UIWindow.new();"
     @"window.frame = UIScreen.mainScreen().bounds;"
     @"window.backgroundColor = UIColor.whiteColor();"
     @""
     @"var navigationController = UINavigationController.new();"
     @"var viewController = UIViewController.new();"
     @"viewController.navigationItem.title = 'Make UI with JavaScript';"
     @""
     @"var view = UIView.new();"
     @"view.backgroundColor = UIColor.redColor();"
     @"view.frame = {x: 20, y: 80, width: 280, height: 80};"
     @""
     @"var label = UILabel.new();"
     @"label.backgroundColor = UIColor.blueColor();"
     @"label.textColor = UIColor.whiteColor();"
     @"label.text = 'Hello World.';"
     @"label.font = UIFont.boldSystemFontOfSize(24);"
     @"label.sizeToFit();"
     @""
     @"var frame = label.frame;"
     @"frame.x = 10;"
     @"frame.y = 10;"
     @"label.frame = frame;"
     @""
     @"view.addSubview(label);"
     @"viewController.view.addSubview(view);"
     @""
     @"navigationController.viewControllers = [viewController];"
     @""
     @"window.rootViewController = navigationController;"
     @"window.makeKeyAndVisible();"
     ];
    
    return YES;
}

@end
```

###Writing apps with only JavaScript

See the [UICatalog](https://github.com/kishikawakatsumi/JavaScriptBridge/tree/master/Examples/UICatalog/UICatalog) example.

## Enhancements
###Define custom classes

You can define custom class in JavaScript.
It is needs to interact system provided framework.

```javascript
var MainViewController = JSB.define('MainViewController : UITableViewController <UITableviewDataSource, UITableviewDelegate>', // Declaration
// Instance Method Definitions
{
  viewDidLoad: function() {
    self.navigationItem.title = 'UICatalog';
  },
  tableViewNumberOfRowsInSection: function(tableView, section) {
    return self.menuList.length;
  },
  tableViewCellForRowAtIndexPath: function(tableView, indexPath) {
    var cell = UITableViewCell.alloc().initWithStyleReuseIdentifier(3, 'Cell');
    cell.accessoryType = 1;
    cell.textLabel.text = self.menuList[indexPath.row]['title'];
    cell.detailTextLabel.text = self.menuList[indexPath.row]['explanation'];

    return cell;
  },
  tableViewDidSelectRowAtIndexPath: function(tableView, indexPath) {
    var targetViewController = self.menuList[indexPath.row]['viewController'];
    self.navigationController.pushViewControllerAnimated(targetViewController, true);
  }
});
```

###Modules

JavaScriptBridge provides simple module system `require/exports` funcitons, like Node.js.
See [example](https://github.com/kishikawakatsumi/JavaScriptBridge/tree/master/Examples/UICatalog/UICatalog/js).

```javascript
var ButtonsViewController = JSB.require('buttonsViewController');
var ControlsViewController = JSB.require('controlsViewController');
var WebViewController = JSB.require('webViewController');
var MapViewController = JSB.require('mapViewController');

var MainViewController = JSB.define('MainViewController : UITableViewController', {
  viewDidLoad: function() {
    self.navigationItem.title = 'UICatalog';
    // 〜〜
});

JSB.exports = MainViewController;
```

## Requirements
- **iOS 7 or later**
- JavaScriptCore.framework

## Installation

JavaScriptBridge is available through [CocoaPods](http://cocoapods.org), to install
it simply add the following line to your Podfile:

    pod "JavaScriptBridge"

## Author

kishikawa katsumi, kishikawakatsumi@mac.com

## License

[Apache]: http://www.apache.org/licenses/LICENSE-2.0
[MIT]: http://www.opensource.org/licenses/mit-license.php
[GPL]: http://www.gnu.org/licenses/gpl.html
[BSD]: http://opensource.org/licenses/bsd-license.php

JavaScriptBridge is available under the [MIT license][MIT]. See the LICENSE file for more info.
