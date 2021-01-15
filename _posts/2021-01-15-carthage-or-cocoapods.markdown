---
layout: post
title: Carthage or Cocoapods
date: 2021-01-15 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: carthagevscocoapods.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [carthage, cocoapods] # add tag
---

In this post, i will critically evaluate Swift dependency managers CocoaPods and Carthage so that iOS developers can make the right choice for their apps.

## CocoaPods

Love it or hate it, you have to use it. Cocoapods exists since the old Objective-C days and works with Swift as well. This is mostly used dependency manage so far for the iOS project which is the de facto standard tool. CocoaPods is built with Ruby and is installable with the default Ruby available on OS X.

```console
sudo gem install cocoapods
```

CocoaPods can be initialised with pod init command which will create template Podfile but we can create our own simple Podfile will look like this

```console
platform :ios, '8.0'
use_frameworks!
target 'MyApp' do
  pod 'SwiftyJSON', '~> 2.3'
end
```

This will setup SwiftyJSON which is the very popular Swift library for parsing JSON for the My App target. Now, we can download dependency using the magical command

```console
pod install
```

### What Changes CocoaPods Made?

The above command (pod install) is very magical which make lots of changes to our Xcode project under the hood. The most of the times, it’s really hard to understand that what has been changed.

* CocoaPods creates Xcode Workspace directory, which has .xcworkspace extension where it builds all the dependency. We have to use Xcode workspace in order to make CocoaPods work.
* CocoaPods added some scripts in the build phases of our target. There are usually three scripts added to the build phases of the target

![Cocoapods]({{site.baseurl}}/assets/img/cocoapods1.png)

* Added Podfile.lock (Locked versions of libraries)
* Linked the Pods Frameworks to “Link Binaries with Libraries”

![Cocoapods]({{site.baseurl}}/assets/img/cocoapods2.png)

* A Pods directory containing source code of the Pod dependencies, supporting files, xcconfigfiles

![Cocoapods]({{site.baseurl}}/assets/img/cocoapods3.png)

* Lots of things inside your Xcode build Settings. It’s hard to cover everything here.

### CocoaPods Build Process

The typical build process that CocoaPods projects include following steps.

* CocoaPods will build and compile our frameworks every time whenever you are doing the clean build or run pod install or pod update for the project.
* Xcode then checks if Podfile.lock has changed if that changed then Xcode will build the dependency framework again otherwise uses the pre-built frameworks.
* You might have seen that project taking a long time when you do the clean build or delete derived data when using CocoaPods.
* CocoaPods will build all the libraries mentioned in the Podfile for that target, there might be some cases where you don’t want to build some libraries all the time but CocoaPods won’t have that option.

### Benefits and Downsides of Cocoapods

|                          Benefits                           |                                                             Downsides |
| ------------------------------------------------------------- | ------------------------------------------------------------- |
| * CocoaPods is easy to setup | * It requires knowledge of another programming language i.e Ruby on which CocoaPods is built.                       |
| * CocoaPods automate the entire process of building, linking the dependency to targets.                     | * Hard to integrate when already using Xcode workspace. |
| * The CocoaPods community has done an impressive job to fix Apple’s shortcomings                     | * Hard to remove once integrated. |
| * CocoaPods supports libraries having subspec.                     | * CocoaPods takes controls of entire Xcode project and if something fails entire project stops building. The fixing the errors thrown by CocoaPods are the hard and time-consuming task and requires an understanding of what CocoaPods changed inside the iOS project. |
| * Lots of contributors with well-grown community                     | * Pods force your project into a specific structure. CocoaPods updating Xcode Projects and Files is like magic without understanding what’s changed. It works in a black box way. |
| * Works well for the small projects with less code to get something working very quickly.                     | * Integrating with Continuous Integration Server is hard as we have to install and manage Ruby libraries. All the dependency needs to be installed and built for every build Or we have to check in the entire third-party dependencies inside the project. Caching mechanism doesn’t always give clean results. |
| * CocoaPods automate the entire process of building, linking the dependency to targets.                     | * The building of iOS app became an intransparent and slow process. |
| * CocoaPods automate the entire process of building, linking the dependency to targets.                     | * Building libraries and frameworks that support CocoaPods became such a pain for iOS developers who are not skilled in Ruby. The developer needs to write .podspec file and follow many unrelated Ruby conventions. |