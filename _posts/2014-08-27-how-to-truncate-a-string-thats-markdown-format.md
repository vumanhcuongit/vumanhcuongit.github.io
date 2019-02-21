---
layout: post
title: "How to truncate a string that's Markdown format"
description: ""
category:
tags:
  - markdown
---
Regarding this [Markdown tip](https://coderwall.com/p/nzrqva?i=2&p=1&q=author%3Avumanhcuongit&t%5B%5D=vumanhcuongit). Today, I got a trouble about truncating  string written in Markdown format.

Firstly, I just used ```truncate(markdown(text), length: 100)``` but it doesn't work well.

{% highlight html %}
<h1>An h1 header</h1> <p>Paragraphs are separated by a blank line.</p> <p>2nd paragraph. <em>It...
{% endhighlight %}

Finally, I tried this one and it works :)

{% highlight ruby %}
truncate(markdown(item.description), length: 100, escape: false)
{% endhighlight %}

