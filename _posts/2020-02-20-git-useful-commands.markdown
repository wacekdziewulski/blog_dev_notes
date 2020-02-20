---
layout: post
title:  "Useful git commands"
date:   2020-02-20 09:30:00 +0100
categories: git
---

Some aliases for $HOME/.gitconfig to make your work easier:
{% highlight bash %}
[alias]
    unstage = reset HEAD
    discard = checkout --
    co = checkout
    cma = commit -a -m
    dc = diff --cached
{% endhighlight %}

Display all of the changes to a certain file, one commit after the other.

{% highlight bash %}
git log --follow -p -- filename
{% endhighlight %}

Show a graph of commits on all branches, one line per commit with relative dates

{% highlight bash %}
git log --graph --pretty=pretty-history --abbrev-commit --date=relative --all
{% endhighlight %}
