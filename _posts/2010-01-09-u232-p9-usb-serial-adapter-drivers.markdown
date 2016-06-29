---
layout: post
title:  "U232-P9 USB-Serial adapter drivers"
date:   2010-01-09 15:18:00
tags: adapter converter driver PL2303 Prolific Serial U232-P9 USB
excerpt: <p>My driver CD for the <strong>USB-Serial converter</strong> has gone AWOL, so I had to look for the appropriate file.</p>
disqus: true
---

#### Problem:

My driver CD for the **USB-Serial converter** has gone AWOL, so I had to look for the appropriate file. My **adapter** is a **generic "no-name" (PL-2303 based)** as can be seen in the photos:
![Photo of U232-P9 USB-Serial converter](/images/u232-p9-1.jpg "U232-P9 USB-Serial converter")
![Photo of U232-P9 USB-Serial converter](/images/u232-p9-2.jpg "U232-P9 USB-Serial converter")

It's a **Prolific chip**, so **[this site](http://www.prolific.com.tw/eng/downloads.asp?ID=31)** should help. I remember **not having any problems with the default driver on my desktop** (**Windows XP**). However, this time I had to use the adapter with my **netbook** (also **Windows XP**), and it **didn't work** with the **device manager** stating:

```This device cannot start. (Code 10)```

#### Solution:

According to **[this thread](http://social.answers.microsoft.com/Forums/en-US/vistahardware/thread/a366f74b-9853-4cae-95c3-4249172951da)**, there's **another version of the driver** available. I'm not sure if [<del datetime="2011-04-19T15:56:43+00:00">the one mentioned there</del>](http://www.usglobalsat.com/download/546/win_drivers.zip) is the same as [PL2303_Prolific_GPS_1013](http://www.prolific.com.tw/support/files/%5CIO%20Cable%5CPL-2303%5CDrivers%20-%20Generic%5CWindows%5CPL2303_Prolific_GPS_1013_20090319.zip), though it looks similar. But I've tried the **GPS version from Prolific**, and it did work. My desktop had **no problems with the standard version** though, so it's a bit of trial and error method.