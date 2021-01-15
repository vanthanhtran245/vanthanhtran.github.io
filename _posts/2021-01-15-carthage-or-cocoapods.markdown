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
| ---------------------------------------------------------------------- | ---------------------------------------------------------------------- |
|  CocoaPods is easy to setup |  It requires knowledge of another programming language i.e Ruby on which CocoaPods is built.                       |
|  CocoaPods automate the entire process of building, linking the dependency to targets.                     |  Hard to integrate when already using Xcode workspace. |
|  The CocoaPods community has done an impressive job to fix Apple’s shortcomings                     |  Hard to remove once integrated. |
|  CocoaPods supports libraries having subspec.                     |  CocoaPods takes controls of entire Xcode project and if something fails entire project stops building. The fixing the errors thrown by CocoaPods are the hard and time-consuming task and requires an understanding of what CocoaPods changed inside the iOS project. |
|  Lots of contributors with well-grown community                     |  Pods force your project into a specific structure. CocoaPods updating Xcode Projects and Files is like magic without understanding what’s changed. It works in a black box way. |
|  Works well for the small projects with less code to get something working very quickly.                     |  Integrating with Continuous Integration Server is hard as we have to install and manage Ruby libraries. All the dependency needs to be installed and built for every build Or we have to check in the entire third-party dependencies inside the project. Caching mechanism doesn’t always give clean results. |
|  CocoaPods automate the entire process of building, linking the dependency to targets.                     |  The building of iOS app became an intransparent and slow process. |
|  CocoaPods automate the entire process of building, linking the dependency to targets.                     |  Building libraries and frameworks that support CocoaPods became such a pain for iOS developers who are not skilled in Ruby. The developer needs to write .podspec file and follow many unrelated Ruby conventions. |

## Carthage

Carthage is another simple dependency manager for the Cocoa application. Carthage has been written purely in Swift to manage iOS dependencies without changing anything inside your Xcode projects.

We can install Carthage using HomeBrew Or you can download the .pkg from Github and install it manually.

```console
brew install carthage
```

We are now ready to use Carthage. As above, we need to get SwiftyJSON using Carthage then we have to creat file called Cartfile with the following content.

```console
github "SwiftyJSON/SwiftyJSON"
```

Now that, we have specified our dependency in the Cartfile, Run

```console
carthage update
```

This will fetch dependencies into a Carthage/Checkouts folder, then build each one. Now everything CocoaPods does automatically as magic, we have to do it manually.

On your application targets General settings tab, in the Linked Frameworks and Libraries section, drag and drop each framework you want to use from the Carthage/Build folder on disk. After doing this all manual work, we should be able to import dependencies. However, the manual work mentioned above is one time or after adding the new dependency.

### What Changes Carthage Made?

Carthage won’t touch Xcode settings or Project files. Carthage is very simple and just check out and build the dependencies and leave it to you to add the binaries to Xcode. It gives you full control of what we are adding to Xcode.

### Carthage Build Process

The typical build process that Carthage projects include following steps.

* Carthage retrieves the libraries and framework using carthage update command which happens only once.
* Xcode will not rebuild any framework when building the project. This speeds up the build process.
* The framework needs to rebuild wh we get the new version of any dependency.

### Benefits and Downsides of Carthage

|                          Benefits                           |                                                             Downsides |
| ---------------------------------------------------------------------- | ---------------------------------------------------------------------- |
|  Carthage is decentralised, simple and ruthless dependency management for iOS |  Carthage has too many manual steps that need to be performed while setup.                       |
|  Carthage is purely written in Swift so iOS developers can understand the technology behind Carthage and probably write another tool using CarthageKit                     |  Carthage is still new framework and not many contributors as CocoaPods |
|  Carthage is easy to integrate and easy to remove from the project if it doesn’t suit project needs.                    |  HCarthage has no clean way to support subspec within libraries. |
|  Carthage won’t touch your Xcode settings or project files. It just downloads and builds the dependencies so you have proper control on what you are doing.                    |  Carthage checkout and builds the frameworks, we might need to check-in those frameworks for faster builds which adds repository size. |
|  Carthage works great for the large or eclectic codebases because of its flexibility                    |  Carthage installed with homebrew is not backward compatible. |
|  Building and updating lib(s) are easier with Carthage.                     |  The building of iOS app became an intransparent and slow process. |
|  Making Swift libraries Carthage compatible is easy.                     |   |
|  Decentralised                    |   |
|  Supports submodules                    |   |

## Tips for Selecting Carthage Or CocoaPods

Now that, we have seen the pros and cons of both CocoaPods and Carthage but the question is which one to pick for your iOS project. I hate this answer but the answer is “It depends”. There nothing wrong with Carthage or CocoaPods but choosing the right one really depends on the team and skills of the engineers working in the team.

Here are some tips:

* First of all, try to avoid adding any dependency to your iOS project unless it’s really really required. Apple has given you lots of native frameworks and tools. There are also possibilities to add extra functionality to the native frameworks provided by Apple. Think 10 times before adding any dependency to your project. e.g When making network calls, do you really need Alamofire, whats missing that Apple’s native URLSession cannot solve your problem. Try to achieve everything with native frameworks as there are rare chances that Apple will break your project.
* While starting new projects, think 100 times before using CocoaPods. When you started using CocoaPods, it’s very hard to remove it from your Xcode project and you will lose control over your Xcode project.
* Prefer Carthage first as it’s written in Swift and if you think it’s not suitable then go for CocoaPods and deal with Ruby.
* While integrating libraries with the large code base, use Carthage as it needs to be built only once.
* If you are thinking to use many libraries in your project CocoaPods won’t be a good choice as they will build the framework everytime you do the clean build. Also as mentioned earlier you don’t have the chance to stop building libraries that you don’t update often. This adds extra time to your build process.
* Don’t be afraid to use both CocoaPods and Carthage in the single project as it makes sense in many situations as mentioned above. We can take advantage of the features of both CocoaPods and Carthage at the same time.
* If you are not skilled in Ruby, then definitely avoid CocoaPods. It will become very hard to maintain the version of Ruby and Ruby libraries on the local machine as well as on Continuous Integration server.
* Libraries using multiple repos can be well handled with CocoaPods subspec while there is no clean solution for Carthage.
* Whatever you select, always check-in the source code of the third-party libraries in the SCM repositories. Never risk your project when library goes missing from Github.
* Use Carthage to check out the private or internal frameworks.
