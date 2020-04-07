---
layout: post
title:  "GoLang: Popular type conversions"
date:   2020-03-26 22:00:00 +0100
categories: golang
---

## Introduction

I'm slowly working on my Go project called [PriceWatch][pricewatch-github]. I have decided to implement it in Go, because it seemed suitable for a web scraper, but a tad faster than python due to being compiled to native code. And it's an incentive to learn new things.
As a newbie in the language I stumbled on basic things so I decided to round up some basic type conversions here.

# string -> io.Reader

{% highlight golang %}
ioReader := strings.NewReader(s)
{% endhighlight %}

# io.Reader -> string

{% highlight golang %}
buf := new(bytes.Buffer)
buf.ReadFrom(ioReader)
s := buf.String()
{% endhighlight %}

Happy coding in Go!

[pricewatch-github]: https://github.com/wacekdziewulski/PriceWatch/
