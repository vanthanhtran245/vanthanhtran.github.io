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

1. Added B.framework to the project.
2. Added B.framework to "Linked Frameworks and Libraries" in the corresponding target.
3. Built A.framework.
4. Created a demo application and included A.framework to the project.
5. Added A.framework to "Embedded Binaries".

![Alt Text](https://media.tenor.com/images/f912a1ad7406e614e2fe33ab8d54179a/tenor.gif)

But after run a application you will see the message

![FW]({{site.baseurl}}/assets/img/fwfail.png)

# Why?

![Alt Text](https://media4.giphy.com/media/cOztgarXataikLpRII/200.gif)