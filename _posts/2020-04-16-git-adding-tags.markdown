---
layout: post
title:  "Git: Tagging "
date:   2020-04-16 11:00:00 +0100
categories: git
---

Git add a tag locally with a comment:
{% highlight bash %}
git tag -a v1.0 -m "Tagged version 1.0"
{% endhighlight %}

Push newly added tags to remote:
{% highlight bash %}
git push --follow-tags
{% endhighlight %}
