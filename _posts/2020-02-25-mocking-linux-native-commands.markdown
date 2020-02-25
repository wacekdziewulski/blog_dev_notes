---
layout: post
title:  "Linux: Mocking native system commands"
date:   2020-02-25 11:50:00 +0100
categories: linux
---

Recently I came across a problem of testing a Linux box from the outside - basically orchestrating the instance using scripts and checking if they work correctly.

## Approach

There are two approaches here. It's either unit testing the script by mocking it in bash directly e.g. [Github: bats-mock project][bats-mock]. The other one is intercepting the calls
to regular linux commands and presenting mocked output instead of real one, so that the system behaves exactly the same every time. I was curious if the latter can be achieved.

It may not be the smartest and cleverest of solutions, but I like simplicity and the final effect matters more than the technology behind (though we love doing fancy stuff in IT).

Well, anyway, CLImate was born.

## Solution

It's a simple PoC, which allows mocking Linux system commands without breaking the system.

# PATH environment variable 

Let's see how it works. Linux allows us to call system tools like 'ls', 'ps' or 'top' without providing the absolute path e.g. /bin/ls, because of the PATH environment variable.
It contains the list of directories to look in, when searching for a certain application. They are scanned in the order they were put in the variable. My local $PATH looks like that:

{% highlight bash %}
vax@propwash:~> echo $PATH

/home/vax/.local/bin:/usr/local/go/bin/:/home/vax/source/go//bin:/home/vax/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/vax/go/bin
{% endhighlight %}

Since I'm using golang, we can see that the paths to Go binaries are in the path. Let's not forget that my Linux box will also look in /usr/bin or /usr/sbin, when looking for a binary to execute.
If I expand the PATH variable, by prepending a different directory in front, my shell will start with this directory before going to system ones e.g. /usr/bin/ or /bin/.

{% highlight bash %}
export PATH=/usr/local/climate:${PATH}
{% endhighlight %}

This way, Linux will start in the /usr/local/climate directory, and if it finds a binary having a certain name there, it won't go to /usr/bin/ or /bin/.

# Putting our mock in /usr/local/climate

Let's see if we can execute our own 'ls' command.

First, we have to create a file, called 'ls' in /usr/local/climate/ and make it executable.

{% highlight bash %}
sudo mkdir -p /usr/local/climate
touch /usr/local/climate/ls
chmod +x /usr/local/climate/ls
{% endhighlight %}

Next we have to fill it with some logic. Let's start simple.

{% highlight bash %}
vax@propwash:/usr/local/climate> cat ls

#!/bin/bash

echo "Hello world!"
{% endhighlight %}

Ok, let's see if it works now:

{% highlight bash %}
vax@propwash:/usr/local/climate> ls
Hello world!
{% endhighlight %}

Success! It's just the case of putting in some logic inside the script, which will handle the commands and parameters in the same manner as 'ls' does. Or any other system tool for that matter.

# Is it broken already?

But wait, how do I call the actual 'ls' right now? Luckily we can still use absolute paths:

{% highlight bash %}
vax@propwash:/usr/local/climate> /bin/ls
ls
{% endhighlight %}

Not much in the directory, but still, the system application still works. If You didn't make the PATH permanent, just close the shell and everything will go back to normal.
Or You can just overwrite the PATH variable while removing the /usr/local/climate from it.

{% highlight bash %}
vax@propwash:/usr/local/climate> echo $PATH
/usr/local/climate:/home/vax/.local/bin:/usr/local/go/bin/:/home/vax/source/go//bin:/home/vax/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/vax/go/bin
vax@propwash:/usr/local/climate> export PATH=/home/vax/.local/bin:/usr/local/go/bin/:/home/vax/source/go//bin:/home/vax/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/vax/go/bin
vax@propwash:/usr/local/climate> echo $PATH
/home/vax/.local/bin:/usr/local/go/bin/:/home/vax/source/go//bin:/home/vax/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/vax/go/bin
vax@propwash:/usr/local/climate> ls
ls
vax@propwash:/usr/local/climate> 
{% endhighlight %}

Voila! To deal with the parameters and application name, I created a python script, which reads its configuration and arguments and reacts if the command is meant to be mocked. It doesn't do a pass-through to the original application
if the arguments are different than the ones, the mock is configured for, but it's quite simple to achieve. [You can find the whole solution on my GitHub][github-climate].

Happy mocking!

[bats-mock]: https://github.com/jasonkarns/bats-mock
[github-climate]: https://github.com/wacekdziewulski/climate
