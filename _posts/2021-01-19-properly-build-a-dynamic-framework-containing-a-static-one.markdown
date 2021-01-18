---
layout: post
title: Properly build a dynamic framework containing a static one
date: 2021-01-18 00:00:00 +0300
description: Properly build a dynamic framework containing a static one
img: fw.jpeg# Add image post (optional)
tags: [Framework] # add tag
---

I'm trying to prepare a single dynamic framework for third party. My framework (A.framework) uses third-party recognition static framework (B.framework). I can't provide separate A and B frameworks to the customer. Ideally B.framework should be built and included into my A.framework's binary, so the customer's app will only embed A.framework without any additional actions to link with that third-party app.

Usually below is the approach

