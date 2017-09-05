---
title: "Image Markers"
date: 2017-09-05T15:43:27-06:00
draft: false
weight: 3
---
Image markers are a good way to keep track of what images you have deployed in your environment. This can be useful for reporting purposes, or if you keep track of known issues in your task sequences it's a good way to know who needs remediation! There are a couple of ways to do this with pros and cons for each.

### Registry Key(s)
Just like the title suggests, create registry keys somewhere on the system. I typically do this under HKLM\Software\\_COMPANYNAME_\ImageMarker

Pros: Easy to digest and scalable (add multiple keys to track different things like date imaged, version of image, name of image, etc).

Cons: To report on it in SCCM you'll need to create a custom MOF - use this utility https://gallery.technet.microsoft.com/RegKeyToMof-28e84c28

### File Marker
This is as simple as creating a file somewhere on the system. Typically I will create a blank "EXE" file since by default SCCM is collecting metadata from EXEs during the "Software Scan Cycle"

Pros: Easy to report on (using default SCCM reports), single step in the task sequence that is simple to modify.

Cons: Looks ugly in my opinion - creates possible unnecessary clutter on the workstation.