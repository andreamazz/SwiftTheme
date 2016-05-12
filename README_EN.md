# SwiftTheme
[![Language: Swift 2](https://img.shields.io/badge/language-swift2-f48041.svg?style=flat)](https://developer.apple.com/swift)
![Platform: iOS 7+](https://img.shields.io/badge/platform-iOS%207%2B-blue.svg?style=flat)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Cocoapods compatible](https://img.shields.io/badge/Cocoapods-compatible-4BC51D.svg?style=flat)](https://cocoapods.org)
[![License: MIT](http://img.shields.io/badge/license-MIT-lightgrey.svg?style=flat)](https://github.com/jiecao-fm/SwiftTheme/blob/master/LICENSE)
[![Release version](https://img.shields.io/badge/release-0.1-blue.svg)]()

[Introduction](#Introduction) - [Example](#Example) - [Install](#Install) - [Document](#Document) - [Contribution](#Contribution)

![](https://github.com/jiecao-fm/SwiftThemeResources/blob/master/Screenshots/day.png)
![](https://github.com/jiecao-fm/SwiftThemeResources/blob/master/Screenshots/night.png)

## Introduction
### The Beginning Of The Story

As the project requirement, we need to add night mode to our app "节操精选". It's not as simple as changing brightness or alpha only on the top-level view. In fact, it need a entirely new interface: different colors, different alpha, different image cuts. More accurately, so called "night mode" is themes/skinning feature that can switch between a bright theme and a dark theme.

How to achieve this? Maybe we can set a global variable represents current selected theme, use different background colors or different image cuts based on the variable during the controllers initialization. But how to deal with the views that have been initialized? Yes, maybe you think we can use notification to change their colors or image cuts. Thought here, you should premonition that your controllers will be filled up with notification register/unregister, if...else and UI updating codes. Worse, if you forget to unregister the notifications app may crash.

After some consideration, we put forward higher requirements on the task: create a simple and reusable themes/skinning framework, here as you see.

### Goals

Make SwiftTheme a simple, powerful, high-performance, extensible themes/skinning framework. Provide a unified solution for iOS.

## Demos


### Index Mode

Vary background color of UIView according to the theme setting?

```swift
view.theme_backgroundColor = ThemeColorPicker(colors: "#FFF", "#000")
```

Vary text color of UILable and UIButton?

```swift
label.theme_textColor = ThemeColorPicker(colors: "#000", "#FFF")
button.theme_setTitleColor(ThemeColorPicker(colors: "#000", "#FFF"), forState: .Normal)
```

Vary image of UIImageView?

```swift
imageView.theme_image = ThemeImagePicker(names: "day", "night")
```

No problem! A miracle happens after you executing the one line of code below!

```swift
//these numbers represent the parameters' index. eg. "ThemeColorPicker(colors: "#000", "#FFF")", index 0 represents "#000", index 1 represents "#FFF"
ThemeManager.setTheme(isNight ? 1 : 0)
```

> Index mode is a fast way for these situation: a few themes, but not many, no need to download more new themes.


### Plist Mode
You may want to make you app download and install an indefinite number of themes. To fulfill this requirement, we provide the plist mode. Simply put, you write the configurations in a plist file, such as colors, image cuts and so on. Then, you can use their keys in the logic code. So, the plist file and the resource files it used constitute a theme package.

Usage demo of plist mode.

```swift
view.theme_backgroundColor = ThemeColorPicker(keyPath: "Global.backgroundColor")
imageView.theme_image = ThemeImagePicker(keyPath: "SelectedThemeCell.iconImage")
```
> Similar with the index mode. Only the specific parameters become keys. And as such, we give it the extension ability.


The plist file name is the first paramter of the switching method. In this example, the plist file and other resource files are in the application bundle. It's also ok if they are in sandbox.

```swift
ThemeManager.setTheme("Red", path: .MainBundle)
```

> plist mode allow you install more themes without modifying logic code. So, you can add the feature that, downloading and installing themes for your app.

the screenshots of the plist and image files we used above:

![](https://github.com/jiecao-fm/SwiftThemeResources/blob/master/Screenshots/2.pic.jpg)
![](https://github.com/jiecao-fm/SwiftThemeResources/blob/master/Screenshots/1.pic.jpg)

### Objective-C

Fully compatible with Objective-C, usage demo:

```objective-c
lbl.theme_backgroundColor = [ThemeColorPicker pickerWithColors:@[@"#FAF9F9", @"#E2E2E2"]];
```

### Features
- [x] Written in Swift
- [x] Fully compatible with Objective-C
- [x] Based on runtime
- [x] Simple integration
- [x] Extension property prefix with "theme_*", friendly with IDE auto-completion
- [x] Support UIAppearance
- [x] Index mode, fast integration
- [x] Plist mode, extend infinite themes
- [x] Friendly error logs
- [x] Strongly typed ThemePicker, detect errors during compilling
- [x] Complete demos


## Installation

> *Swift code is built as dynamic framework by Cocoapods, Carthage and the project itself, so dynamic frameworks works on iOS8+*
>
> **If you want it work on iOS7, you should copy the source files into you project**

#### Cocoapods
```swift
pod 'SwiftTheme'
use_frameworks!
```

#### Carthage
```swift
github "jiecao-fm/SwiftTheme"
```

#### Framework
Build the source project, drop the SwiftTheme.framework into your project

#### Source files（iOS7）
Copy all the files in "Source" folder into your project

## Documents

Note：`①` usage of index mode `②` usage of plist mode

### *Basic Usage*
***

#### Configurate appearance

SwiftTheme provide new properties for views, they all beigin with `theme_`. Such as `theme_backgroundColor` corresponds `backgroundColor`.

```swift
①
view.theme_backgroundColor = ThemeColorPicker(colors: "#FFF", "#000")
view.theme_image = ThemeImagePicker(names: "day", "night")
②
view.theme_backgroundColor = ThemeColorPicker(keyPath: "SomeColorKeyPath")
view.theme_image = ThemeImagePicker(keyPath: "SomeImageKeyPath")
```
> Different type of properties receive different type of Pickers. Thus, IDE will warn you if you pass a wrong parameter.

#### Switch themes

When you switch themes, all the `theme_` properties you set will update with animation. Usage:

```swift
①
ThemeManager.setTheme(0) // ThemePickers will use the first parameter, eg. "#FFF" "day"
ThemeManager.setTheme(1) // ThemePickers will use the second parameter, eg. "#000" "night"
②
// use "day.plist" in the appllication bundle as the theme configuration file. In this mode, SwiftTheme will find the resource files in the appllication bundle.
ThemeManager.setTheme("day", path: .MainBundle)
// use "night.plist" in the sandbox as the theme configuration file, "someURL" is its file path. In this mode, SwiftTheme will find the resource files in the same path.
ThemeManager.setTheme("night", path: .Sandbox(someURL))
// use a dictionary as the theme configuration, but find resource files in the sandbox.(Not recommend)
ThemeManager.setTheme(dict, path: .Sandbox(someURL))
```

#### Custom behaviors

SwiftTheme posts a notification named `ThemeUpdateNotification` when theme changes, you can observe this notification anywhere and do whatever you want:
```swift
NSNotificationCenter.defaultCenter().addObserver(self, selector: "doSomething", name: ThemeUpdateNotification, object: nil)
```
```objective-c
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(doSomething) name:@"ThemeUpdateNotification" object:nil];
```

### *Now supported properties*
***

> Child classes inherit the properties from their super class, such as UILabel have theme_alpha inherited from UIView. These properties will not be list in child classes below.

##### UIView
- var theme_alpha: ThemeCGFloatPicker?
- var theme_backgroundColor: ThemeColorPicker?
- var theme_tintColor: ThemeColorPicker?

##### UIApplication
- func theme_setStatusBarStyle(picker: ThemeStatusBarStylePicker, animated: Bool)

##### UIBarButtonItem
- var theme_tintColor: ThemeColorPicker?

##### UILabel
- var theme_textColor: ThemeColorPicker?
- var theme_highlightedTextColor: ThemeColorPicker?
- var theme_shadowColor: ThemeColorPicker?

##### UINavigationBar
- var theme_barTintColor: ThemeColorPicker?
- var theme_titleTextAttributes: ThemeDictionaryPicker?

##### UITabBar
- var theme_barTintColor: ThemeColorPicker?

##### UITableView
- var theme_separatorColor: ThemeColorPicker?

##### UITextField
- var theme_textColor: ThemeColorPicker?

##### UITextView
- var theme_textColor: ThemeColorPicker?

##### UIToolbar
- var theme_barTintColor: ThemeColorPicker?

##### UISwitch
- var theme_onTintColor: ThemeColorPicker?
- var theme_thumbTintColor: ThemeColorPicker?

##### UISlider
- var theme_thumbTintColor: ThemeColorPicker?
- var theme_minimumTrackTintColor: ThemeColorPicker?
- var theme_maximumTrackTintColor: ThemeColorPicker?

##### UISearchBar
- var theme_barTintColor: ThemeColorPicker?

##### UIProgressView
- var theme_progressTintColor: ThemeColorPicker?
- var theme_trackTintColor: ThemeColorPicker?

##### UIPageControl
- var theme_pageIndicatorTintColor: ThemeColorPicker?
- var theme_currentPageIndicatorTintColor: ThemeColorPicker?

##### UIImageView
- var theme_image: ThemeImagePicker?

##### UIButton
- func theme_setImage(picker: ThemeImagePicker, forState state: UIControlState)
- func theme_setBackgroundImage(picker: ThemeImagePicker, forState state: UIControlState)
- func theme_setTitleColor(picker: ThemeColorPicker, forState state: UIControlState)

##### CALayer
- var theme_borderColor: ThemeCGColorPicker?
- var theme_shadowColor: ThemeCGColorPicker?

### *Picker*
***

#### ThemeColorPicker
```swift
// supported formats:
// "#ffcc00"		RGB
// "#ffcc00dd"		RGBA
// "#FFF"			RGB in short
// "#013E"			RGBA in short
①
ThemeColorPicker(colors: "#FFFFFF", "#000")
ThemeColorPicker.pickerWithColors(["#FFFFFF", "#000"])
②
ThemeColorPicker(keyPath: "someStringKeyPath")
ThemeColorPicker.pickerWithKeyPath("someStringKeyPath")
```

#### ThemeImagePicker
```swift
①
ThemeImagePicker(names: "image1", "image2")
ThemeImagePicker.pickerWithNames(["image1", "image2"])
ThemeImagePicker(images: UIImage(named: "image1")!, UIImage(named: "image2")!)
ThemeImagePicker.pickerWithImages([UIImage(named: "image1")!, UIImage(named: "image2")!])
②
ThemeImagePicker(keyPath: "someStringKeyPath")
ThemeImagePicker.pickerWithKeyPath("someStringKeyPath")
```

#### ThemeCGFloatPicker
```swift
①
ThemeCGFloatPicker(floats: 1.0, 0.7)
ThemeCGFloatPicker.pickerWithFloats([1.0, 0.7])
②
ThemeCGFloatPicker(keyPath: "someNumberKeyPath")
ThemeCGFloatPicker.pickerWithKeyPath("someNumberKeyPath")
```

#### ThemeCGColorPicker
```swift
①
ThemeCGColorPicker(colors: "#FFFFFF", "#000")
ThemeCGColorPicker.pickerWithColors(["#FFFFFF", "#000"])
②
ThemeCGColorPicker(keyPath: "someStringKeyPath")
ThemeCGColorPicker.pickerWithKeyPath("someStringKeyPath")
```

#### ThemeDictionaryPicker
```swift
①
ThemeDictionaryPicker(dicts: ["key": "value"], ["key": "value"])
ThemeDictionaryPicker.pickerWithDicts([["key": "value"], ["key": "value"]])
②
// Reading dictionary from plist is not supported now
```

#### ThemeStatusBarStylePicker
```swift
①
ThemeStatusBarStylePicker(styles: .Default, .LightContent)
ThemeStatusBarStylePicker.pickerWithStyles([.Default, .LightContent])
②
// name the key you like, but the available values are "UIStatusBarStyleDefault" and "UIStatusBarStyleLightContent"
ThemeStatusBarStylePicker(keyPath: "someStringKeyPath")
ThemeStatusBarStylePicker.pickerWithKeyPath("someStringKeyPath")
```

### *More*

Download this project and find more. There are two demo targets:

- `Demo` shows how to use index mode and how to save the last selection of themes and other general usages.
- `PlistDemo` shows how to use plist mode and how to download themes that packaged in zip files.

## Contribution

### Issue
If you find a bug or need a help, you can [create a issue](https://github.com/jiecao-fm/SwiftTheme/issues/new)


### Pull Request
Expect your pull request :D. But please make sure it's needed by most developers and make it simple to use. If you are not sure you can create a issue and let's discuss before coding.

### Todo
- [ ] Improve documents
- [ ] Support more properties
- [ ] [Open Issue](https://github.com/jiecao-fm/SwiftTheme/issues)

### Contributors
[GeSen](https://github.com/wxxsw), [Zhoujun](https://github.com/shannonchou)


## Lisence

The MIT License (MIT)

Copyright (c) 2016 节操精选 http://jiecao.fm

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.