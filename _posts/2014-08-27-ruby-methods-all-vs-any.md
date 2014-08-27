---
layout: post
title: "Ruby methods: all? vs any?"
description: ""
category: Ruby
tags: []
---

### `all?`:
The method returns true if the block **NEVER** returns false or nil.

{% highlight ruby %}
["ant", "bear", "cat"].all? { |word| word.length >= 3 } #=> true
["ant", "bear", "cat"].all? { |word| word.length >= 4 } #=> false
{% endhighlight %}

### `any?`:

The method returns true if the block **EVER** returns a value other than false or nil.


{% highlight ruby %}
["ant", "bear", "cat"].any? { |word| word.length >= 3 } #=> true
["ant", "bear", "cat"].any? { |word| word.length >= 4 } #=> true
{% endhighlight %}

## Deep magic:

{% highlight ruby %}
[].all? #=> true
{% endhighlight %}

So since the block never gets called, of course it never returns false or nil, thus all returns true.

{% highlight ruby %}
[].any? #=> false
{% endhighlight %}

Since the block never gets called, it never returns a value other than false or nil, thus any returns false.

{% include JB/setup %}
