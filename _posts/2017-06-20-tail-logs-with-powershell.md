---
layout: post
title: Tail logs with a simple one-line Powershell script
comments: True
blog: Tech
tags: [Powershell, Windows]
description: With a small one-liner you can listen to logs in Powershell
---

With just a small one-liner you can listen to logs in Powershell:
```
> Get-Content .\application_2017-05-30.log -Wait -Tail 0
```

`application_2017-05-30.log` is the name of the log file. `-Wait -Tail 0` means "Wait for more and print out the last 0 lines". So basically it won't write out any old lines in the log but only new ones.

With a simple regex we can filter out what particularly lines we want to write out. In the snippet below we write out all (new) lines containing `UserRepository` in the line:

```
> Get-Content .\application_2017-05-30.log -Wait -Tail 0 | Select-String '.+(UserRepository).+'
```

{% include plugs/twitter.html %}  
{% include plugs/signature.html %}
