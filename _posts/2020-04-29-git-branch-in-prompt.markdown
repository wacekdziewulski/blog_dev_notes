---
layout: post
title:  "Git: Branch Name on bash prompt"
date:   2020-04-29 17:00:00 +0100
categories: git
---

I use git through the command line, so I find it very useful to be able to see what branch I'm on instantly, when I enter a git project directory.
Since `PS1` is highly configurable, one can add the git branch to the prompt line, which saves us one `git branch` call every time.

# Getting the current git branch

There is quite a recent command to get the current branch name when in a git directory:

{% highlight bash %}
%> git symbolic-ref --short HEAD
GitTags
{% endhighlight %}

However, if we're not in a directory, where we have git sources, an error will pop up:

{% highlight bash %}
%> git symbolic-ref --short HEAD
fatal: not a git repository (or any of the parent directories): .git
{% endhighlight %}

It's pretty reasonable, but the problem here is that if we use it in the prompt, it may show up whenever we navigate to a non-git folder.
Luckily, since it's an error, it shows up on STDERR, therefore we can just redirect the stream to `/dev/null`.

# My prompt

The prompt that I use on my Linux boxes looks like that. I like things in colour - it's more clear what is what. The branch name shows up
only if I'm in a git project directory. Otherwise it's just blank space.

{% highlight bash %}
PS1="\[\e[1;32m\]\u@propwash\[\e[0m\]:\[\e[1;34m\]\w\[\e[1;33m\] \$(git symbolic-ref --short HEAD 2>/dev/null)\[\e[0m\]> "
{% endhighlight %}

![My current bash prompt]({{'assets/git/gitBranchOnPrompt.png' | relative_url}})

## References

* [Git symbolic-ref reference][git-symbolic-ref]

[git-symbolic-ref]: https://git-scm.com/docs/git-symbolic-ref
