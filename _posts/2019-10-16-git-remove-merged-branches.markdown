---
layout: post
title:  "Git: remove merged branches"
date:   2019-10-16 09:30:00 +0100
categories: git
---

On each git pull I get more and more branches created locally, because they appeared on the remote. Also, I end up with my own, already merged and the list just keeps growing.

The problem is that when I want to switch to a new branch and use bash-completion, the list is just too long. One time I just started digging and after some web searches ended up with this helpful bash function:

{% highlight bash %}
git_clean_branches() {
    echo "Removing branches deleted on origin..."
    git remote prune origin
    echo "Removing local merged branches..."
    git branch --merged | egrep -v "(^\*|master)" | xargs git branch -d
}
{% endhighlight %}

Ok, so what does it exactly do?

First, the branches that are no longer on the remote are also removed locally. If my colleagues merged and deleted their feature/bugfix branches, they will be removed from my local repo as well.

Secondly, I ask git to remove each branch, that I merged to master. Since I’m using ‘-d’, only the actually merged branches will be deleted and the ones I’m still working on won’t be touched.

Have a good day and a clean repo!
