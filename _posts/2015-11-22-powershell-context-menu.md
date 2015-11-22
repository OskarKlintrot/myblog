---
layout: post
title: Add Powershell option to the context menu
comments: True
blog: Tech
tags: [Powershell, Windows]
description: A quick and dirty guide to add an "Open Powershell here" option to the Windows 10 context menu
---
I noticed that the tutorials I found for adding an "Open Powershell here" option to the Explorer context menu (the one that pop ups when you right-click on an empty space) didn't work for Windows 10. Adding it is really simple though, you just open regedit (`win+R` and then type in `regedit` and press enter) and go to the following location:
  `HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\background\shell`

Right click on the folder `shell` and choose `new -> key` and name the new key to `powershell`. Change the value on `(Standard)` to "Open Powershell here" by simply dubble click on the key. Then add a new key by right-clicking on the key `powershell` (the one you previously added) and choose `new -> key`. Name it `command` and change the value to `powershell`.

That's it! Now it should work! If not, try and reboot the system.

{% include plugs/twitter.html %}  
{% include plugs/signature.html %}
