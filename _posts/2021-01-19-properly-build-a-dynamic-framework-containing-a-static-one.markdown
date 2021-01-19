---
layout: post
title: Properly build a dynamic framework containing a static one
date: 2021-01-18 00:00:00 +0300
description: Properly build a dynamic framework containing a static one
img: fw.jpg
tags: [Framework] # add tag
---

I'm trying to prepare a single dynamic framework for third party. My framework (A.framework) uses third-party recognition static framework (B.framework). I can't provide separate A and B frameworks to the customer. Ideally B.framework should be built and included into my A.framework's binary, so the customer's app will only embed A.framework without any additional actions to link with that third-party app.

Usually below is the approach

**1. Added B.framework to the project.**

**2. Added B.framework to "Linked Frameworks and Libraries" in the corresponding target.**

**3. Built A.framework.**

**4. Created a demo application and included A.framework to the project.**

**5. Added A.framework to "Embedded Binaries".**

But after run a application you will see the message

![FW]({{site.baseurl}}/assets/img/fwfail.png)

# Why?

![Alt Text](https://media4.giphy.com/media/cOztgarXataikLpRII/200.gif)

 Even if the static library B is compiled with the framework A, a consuming client (an app or a framework) will still need to find the .swiftmodule or .modulemap file of the library B. The module information is used to expose the Swift API (or C,C++,objc) of module B when module A is imported!

# How to resolved it?

![Alt Text](https://media.tenor.com/images/0cb034c5f8c08a9c2e6be0b36192e670/tenor.gif)

## If the static library B is only used for internal implementation of framework A, you can add the `@_implementationOnly import B` everywhere in framework A, and the requirement to know about the module information of B will disappeared for the consuming client side since the compiler does not need to expose back the API of module B (again this works if no public symbols in A refers to symbols in B)!

## If A exports types in B, then you need to tell the compiler where to find the module / interfaces of B even when consuming A! This is why moving files around or trying to copy the framework A to an other machine without bringing the interface of B will cause a compiler issue telling you Missing required module 'B'. Simply tell the compiler where to find it the module information of B using HEADER_SEARCH_PATHS ! 

## Be careful, the framework A still knows about the absolute path of the interfaces of B, so if you update HEADER_SEARCH_PATHS for the interface of B after moving the framework A around and do not delete the original module map, you may get a build error with Redefinition of module 'B'.

![Alt Text](https://media.tenor.com/images/f912a1ad7406e614e2fe33ab8d54179a/tenor.gif)
