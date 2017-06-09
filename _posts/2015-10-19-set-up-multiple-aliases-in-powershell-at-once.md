---
layout: post
title: Set up multiple aliases in Powershell at once
comments: True
blog: Tech
tags: [Powershell, Scripting]
description: In this article we will take a look at how we can make some scripts that is automaticly added as alias in Powershell. This way we can automate stuff that we do a lot and keep everything super simple to maintain.
redirect_from: "/set-up-multiple-aliases-in-powershell-at-once"
---
Using the command-line in Windows used to be a horrible experience compared to *nix. Nowdays though Windows PowerShell is a really nice command-line shell, especially in Windows 10. The shell's in Windows (thats PS and of course the good old cmd.exe) works in another way than in *nix. In Windows they treat everything as objects while in *nix they treat everything as a file. Both have their pro's and cons but I'm not the right person to dig into that. Instead I will show you how I use small scripts for my most used commands.  
## Starting to use the command-line, again...
I've grown to like Powershell more and more since I first started using it. After I gave up Linux for like 5 years ago I thought I would miss the terminal. I didn't. However, now I'm studing web development and I'm now using the Powershell for some tasks, as working with git and vagrant. I still prefer a nice GUI but I guess *nix users haven't got enough powerful computers just yet. Jokes beside, the command-line is a great tool for some tasks and I have made some scripts for my most used commands.
## Using scripts in the everyday life as an developer
In the folder `C:\Users\Oskar\Documents\WindowsPowerShell`, where Oskar is my user, I have created the file `profile.ps1` that only consists of 5 small lines:
 {% highlight powershell %}
$Path = "C:\Users\Oskar\Documents\WindowsPowerShell\myScripts"
$files = Get-ChildItem $Path\*.ps1
ForEach ($file in $files) {
    set-alias $file.BaseName $file.FullName
}
{% endhighlight %}
This scripts takes all files from `C:\Users\Oskar\Documents\WindowsPowerShell\myScripts` that ends with `.ps1`, which is a PS-script file, and sets aliases to the script file (`$file.FullName` gives back a string with the name and path to the file). The alias name of the file, `$file.BaseName`, becomes the alias for the script.
## Lets see a script!
So, what do I acutally have in my script-folder? I am currently participating in a PHP-course where I have been using a Homestead vm for Vagrant (Homestead is a virtual machine from Laravel, the popular php-framwork) a lot. In order to start the vm easy from PhpStorms I have this script:
{% highlight powershell %}
$currentPath = $Pwd 		#Store current path
cd D:\Devtools\Homestead	#Go to Homestead's folder
vagrant up					#Boot with vagrant
cd $currentPath				#Go back to where we came from
{% endhighlight %}
The script is named `homestead-up` so when I open up PhpStorms I just type in the terminal `homestead-up` and voilà, the script is excecuted and my vm is booting!    
I also have a script for commiting, it's honestly pretty unnecessary but I did it to learn how to use arguments in a script. Here goes:  
{% highlight powershell %}
if ($args[0]) {
    git commit -am $args[0].ToString()
    if(![string]::IsNullOrEmpty($args[1])) {
        if (!$args[1].ToString().ToLower().CompareTo("push")) {
            git push
            echo "Pushed it!"
        }
    }
    else {
        echo "This should not be shown"
        }
}
else {
    echo "You have to write a comment to the commit."
}
{% endhighlight %}
It takes the first argument as the commit and if it has a second argument that is equal to `push` it also pushes the commit directly to github.

Hope you learned something new!

{% include plugs/twitter.html %}  
{% include plugs/signature.html %}
